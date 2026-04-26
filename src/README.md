Control software
====

*!
 * MindPlus + MPU6050 + OpenMV - WRO Future Engineers
 * Plataforma: Arduino Nano
 *
 * v5 (sobre v4):
 *  - INTEGRACIÓN OPENMV vía SoftwareSerial (pines 2=RX, 3=TX).
 *  - Parser robusto del protocolo "ID,X,AREA,WIDTH,ZONE\n" enviado
 *    desde la OpenMV (X ya viene normalizado a -100..+100).
 *  - Lógica de esquive integrada al lazo de control:
 *        ID == 1 (ROJO)  -> pasar por la DERECHA del pilar
 *        ID == 2 (VERDE) -> pasar por la IZQUIERDA del pilar
 *        ID == 0         -> seguir paredes con URM10 (lógica v4)
 *  - Timeout de visión: si la OpenMV no envía mensajes en VISION_TIMEOUT_MS,
 *    se asume "sin pilar" para no quedarse persiguiendo fantasmas.
 *  - Latch corto: cuando el pilar se sale del FOV por estar muy cerca,
 *    se mantiene la última orden de esquive durante PILAR_LATCH_MS
 *    para evitar oscilaciones al rozar el obstáculo.
 *
 * Cableado OpenMV -> Nano:
 *   OpenMV P4 (TX, UART1) -> Nano D2 (RX SoftwareSerial)
 *   OpenMV P5 (RX, UART1) -> Nano D3 (TX SoftwareSerial)   *no se usa, opcional*
 *   GND común OBLIGATORIO
 *
 * Conexiones MPU6050 (Arduino Nano):
 *   VCC -> 3.3V o 5V   GND -> GND   SDA -> A4   SCL -> A5
 */

#include <Wire.h>
#include <SoftwareSerial.h>
#include <DFRobot_Servo.h>
#include <DFRobot_URM10.h>*!
 * MindPlus + MPU6050 + OpenMV - WRO Future Engineers
 * Plataforma: Arduino Nano
 *
 * v5 (sobre v4):
 *  - INTEGRACIÓN OPENMV vía SoftwareSerial (pines 2=RX, 3=TX).
 *  - Parser robusto del protocolo "ID,X,AREA,WIDTH,ZONE\n" enviado
 *    desde la OpenMV (X ya viene normalizado a -100..+100).
 *  - Lógica de esquive integrada al lazo de control:
 *        ID == 1 (ROJO)  -> pasar por la DERECHA del pilar
 *        ID == 2 (VERDE) -> pasar por la IZQUIERDA del pilar
 *        ID == 0         -> seguir paredes con URM10 (lógica v4)
 *  - Timeout de visión: si la OpenMV no envía mensajes en VISION_TIMEOUT_MS,
 *    se asume "sin pilar" para no quedarse persiguiendo fantasmas.
 *  - Latch corto: cuando el pilar se sale del FOV por estar muy cerca,
 *    se mantiene la última orden de esquive durante PILAR_LATCH_MS
 *    para evitar oscilaciones al rozar el obstáculo.
 *
 * Cableado OpenMV -> Nano:
 *   OpenMV P4 (TX, UART1) -> Nano D2 (RX SoftwareSerial)
 *   OpenMV P5 (RX, UART1) -> Nano D3 (TX SoftwareSerial)   *no se usa, opcional*
 *   GND común OBLIGATORIO
 *
 * Conexiones MPU6050 (Arduino Nano):
 *   VCC -> 3.3V o 5V   GND -> GND   SDA -> A4   SCL -> A5
 */

#include <Wire.h>
#include <SoftwareSerial.h>
#include <DFRobot_Servo.h>
#include <DFRobot_URM10.h>
#include <MPU6050.h>

// =================== Configuración general ===================
#define ANGULO_VUELTA       360.0
#define VUELTAS_OBJETIVO    3
#define ANGULO_RESPALDO     1080.0
#define MUESTRAS_CALIB      1000
#define VELOCIDAD_PWM       93

// --- Alineación inicial ---
#define ALINEACION_MS       800
#define TOL_ALINEACION_CM   2.0
#define VEL_ALINEACION      35

// --- Arranque suave ---
#define SOFT_START_MS       800
#define VEL_INICIAL         45

// --- Ajuste fino del punto de paro ---
#define OFFSET_PARO_MS      -30

// --- Filtrado de URM10 ---
#define VENTANA_FILTRO      3
#define DIST_MIN_VALIDA     1.0
#define DIST_MAX_VALIDA     300.0

// --- Freno activo ---
#define FRENO_REVERSA_MS    80
#define FRENO_REVERSA_PWM   0

// =================== Configuración OpenMV / esquive ===================
#define PIN_OPENMV_RX       2        // Conectar al TX de la OpenMV
#define PIN_OPENMV_TX       3        // Conectar al RX de la OpenMV (opcional)
#define OPENMV_BAUD         115200

#define VISION_TIMEOUT_MS   200      // Sin datos > este tiempo => ID = 0
#define PILAR_LATCH_MS      250      // Mantener orden de esquive si se pierde el blob

// Offset de esquive equivalente al "+115" original (HuskyLens -160..+160)
// pero ajustado al rango -100..+100 que envía la OpenMV.
// Súbelo si esquivas demasiado tarde, bájalo si esquivas demasiado pronto.
#define OFFSET_PILAR        70

// Área mínima del blob para considerarlo válido (filtro adicional, opcional)
#define AREA_MIN_VALIDA     150

// =================== Variables Mind+ ===================
volatile float mind_n_ul_der, mind_n_ul_izq, mind_n_ERROR, mind_n_lado;

// Variables que vienen de la OpenMV
volatile int   mind_n_ID    = 0;     // 0=nada, 1=rojo, 2=verde, 3=magenta
volatile int   mind_n_X     = 0;     // -100..+100
volatile long  mind_n_AREA  = 0;
volatile int   mind_n_WIDTH = 0;
volatile char  mind_n_ZONE  = 'N';   // 'L','C','R','N'

// =================== Objetos ===================
Servo         servo_8;
DFRobot_URM10 urm10;
MPU6050       mpu;
SoftwareSerial openmv(PIN_OPENMV_RX, PIN_OPENMV_TX);

// =================== Variables del giroscopio ===================
float yaw = 0.0;
float gyroZ_offset = 0.0;
unsigned long t_anterior_us = 0;
const float GYRO_LSB_POR_DPS = 131.0;

// =================== Lógica de vueltas y parada ===================
unsigned long t_inicio = 0;
unsigned long t_vuelta[VUELTAS_OBJETIVO + 1] = {0};
int  contador_vueltas = 0;
unsigned long t_objetivo_paro = 0;
bool prediccion_lista = false;
bool detenido = false;

// =================== Buffers del filtro de URM10 ===================
float buf_der[VENTANA_FILTRO];
float buf_izq[VENTANA_FILTRO];
int   idx_filtro = 0;
float ultima_valida_der = 30.0;
float ultima_valida_izq = 30.0;

// =================== Variables del parser OpenMV ===================
char  rx_buf[48];
uint8_t rx_idx = 0;
unsigned long t_ultimo_msg = 0;
unsigned long t_ultimo_pilar = 0;     // cuándo vimos un ID>0 por última vez
int  ultimo_ID_visto = 0;             // para el latch


// =================== Utilidades URM10 ===================
float mediana3(float a, float b, float c) {
  float maxAB = (a > b) ? a : b;
  float minAB = (a > b) ? b : a;
  if (c >= maxAB) return maxAB;
  if (c <= minAB) return minAB;
  return c;
}

void inicializarFiltros() {
  float d = urm10.getDistanceCM(4, 5);
  float i = urm10.getDistanceCM(4, 7);
  if (d < DIST_MIN_VALIDA || d > DIST_MAX_VALIDA) d = 30.0;
  if (i < DIST_MIN_VALIDA || i > DIST_MAX_VALIDA) i = 30.0;
  ultima_valida_der = d;
  ultima_valida_izq = i;
  for (int k = 0; k < VENTANA_FILTRO; k++) {
    buf_der[k] = d;
    buf_izq[k] = i;
  }
}

void leerSensoresFiltrados() {
  float r_der = urm10.getDistanceCM(4, 5);
  float r_izq = urm10.getDistanceCM(4, 7);

  if (r_der >= DIST_MIN_VALIDA && r_der <= DIST_MAX_VALIDA) ultima_valida_der = r_der;
  if (r_izq >= DIST_MIN_VALIDA && r_izq <= DIST_MAX_VALIDA) ultima_valida_izq = r_izq;

  buf_der[idx_filtro] = ultima_valida_der;
  buf_izq[idx_filtro] = ultima_valida_izq;
  idx_filtro = (idx_filtro + 1) % VENTANA_FILTRO;

  mind_n_ul_der = mediana3(buf_der[0], buf_der[1], buf_der[2]);
  mind_n_ul_izq = mediana3(buf_izq[0], buf_izq[1], buf_izq[2]);
}


// =================== Parser OpenMV ===================
// Procesa los bytes que hayan llegado por SoftwareSerial.
// Cuando recibe '\n', intenta parsear "ID,X,AREA,WIDTH,ZONE".
// Llamar tan seguido como sea posible (cada iteración del loop).
void actualizarOpenMV() {
  while (openmv.available()) {
    char c = openmv.read();

    if (c == '\r') continue;          // ignorar CR

    if (c == '\n') {
      rx_buf[rx_idx] = '\0';

      // Parsear: ID,X,AREA,WIDTH,ZONE
      int    p_ID, p_X, p_W;
      long   p_AREA;
      char   p_ZONE;
      int    n = sscanf(rx_buf, "%d,%d,%ld,%d,%c",
                        &p_ID, &p_X, &p_AREA, &p_W, &p_ZONE);

      if (n == 5) {
        // Filtro adicional por área (descarta blobs muy pequeños)
        if (p_ID > 0 && p_AREA < AREA_MIN_VALIDA) {
          p_ID = 0;
        }
        mind_n_ID    = p_ID;
        mind_n_X     = p_X;
        mind_n_AREA  = p_AREA;
        mind_n_WIDTH = p_W;
        mind_n_ZONE  = p_ZONE;
        t_ultimo_msg = millis();

        if (mind_n_ID > 0) {
          ultimo_ID_visto = mind_n_ID;
          t_ultimo_pilar  = millis();
        }
      }
      rx_idx = 0;
    }
    else if (rx_idx < sizeof(rx_buf) - 1) {
      rx_buf[rx_idx++] = c;
    }
    else {
      // Overflow: descartar buffer
      rx_idx = 0;
         }
  }

  // Timeout de visión: sin mensajes -> sin pilar
  if (millis() - t_ultimo_msg > VISION_TIMEOUT_MS) {
    mind_n_ID = 0;
  }
}

// Devuelve el ID activo a usar para esquivar.
// Aplica el "latch": si acabamos de perder el pilar (ID=0 ahora pero
// hace pocos ms había uno), seguimos esquivando con el último visto.
int idActivoConLatch() {
  if (mind_n_ID > 0) return mind_n_ID;
  if (ultimo_ID_visto > 0 &&
      (millis() - t_ultimo_pilar) < PILAR_LATCH_MS) {
    return ultimo_ID_visto;
  }
  return 0;
}


// =================== MPU6050 ===================
void calibrarGiroscopio() {
  Serial.println(F("Calibrando giroscopio... NO mover el robot"));
  long suma = 0;
  for (int i = 0; i < MUESTRAS_CALIB; i++) {
    int16_t gx, gy, gz;
    mpu.getRotation(&gx, &gy, &gz);
    suma += gz;
    delay(2);
  }
  gyroZ_offset = (float)suma / MUESTRAS_CALIB;
  Serial.print(F("Offset Z (LSB) = "));
  Serial.println(gyroZ_offset);
}


// =================== Motores ===================
void detenerMotores() {
  digitalWrite(10, LOW);
  digitalWrite(9,  LOW);
  analogWrite(11, 0);
  servo_8.angle(90);
}

void frenarActivo() {
  digitalWrite(10, LOW);
  digitalWrite(9,  HIGH);
  analogWrite(11, map(FRENO_REVERSA_PWM, 0, 100, 0, 255));
  delay(FRENO_REVERSA_MS);
  digitalWrite(10, LOW);
  digitalWrite(9,  LOW);
  analogWrite(11, 0);
  servo_8.angle(90);
}


// =================== Cálculo del ERROR de dirección ===================
// Devuelve el ángulo objetivo del servo (65..115) según haya pilar o no.
int calcularError() {
  int id_uso = idActivoConLatch();

  if (id_uso == 1) {
    // ROJO -> pasar por la DERECHA
    // Empujamos virtualmente la X hacia la izquierda con +OFFSET_PILAR,
    // así el robot vira a la derecha para "centrar" el pilar a su izquierda.
    return constrain(
             map(mind_n_X + OFFSET_PILAR, -100, 100, 65, 115),
             65, 115);
  }
  else if (id_uso == 2) {
    // VERDE -> pasar por la IZQUIERDA
    return constrain(
             map(mind_n_X - OFFSET_PILAR, -100, 100, 65, 115),
             65, 115);
  }
  else {
    // Sin pilar -> seguir paredes con URM10
    return constrain(
             map((int)((mind_n_ul_izq - mind_n_ul_der) * 2), -100, 100, 65, 115),
             65, 115);
  }
}


// =================== Avance ===================
// PWM 0..100 + corrección de dirección con OpenMV o paredes.
void avanzarConPWM(int pwm_0_100) {
  digitalWrite(10, HIGH);
  digitalWrite(9,  LOW);
  analogWrite(11, map(pwm_0_100, 0, 100, 0, 255));

  leerSensoresFiltrados();
  actualizarOpenMV();

  mind_n_ERROR = calcularError();
  servo_8.angle(abs(mind_n_ERROR));
}

void seguirCarril() {
  avanzarConPWM(VELOCIDAD_PWM);
}


// =================== Alineación inicial ===================
void alinearInicio() {
  Serial.println(F("Alineando al carril..."));
  unsigned long t0 = millis();
  while (millis() - t0 < ALINEACION_MS) {
    leerSensoresFiltrados();
    actualizarOpenMV();           // por si ya hay un pilar enfrente al arrancar

    mind_n_ERROR = calcularError();
    servo_8.angle(abs(mind_n_ERROR));

    digitalWrite(10, HIGH);
    digitalWrite(9,  LOW);
    analogWrite(11, map(VEL_ALINEACION, 0, 100, 0, 255));

    Serial.print(F("[ALIN] der ")); Serial.print(mind_n_ul_der);
    Serial.print(F(" izq "));       Serial.print(mind_n_ul_izq);
    Serial.print(F(" ID "));        Serial.print(mind_n_ID);
    Serial.print(F(" err "));       Serial.println(mind_n_ERROR);

    // Solo salir antes si NO hay pilar y las paredes están equilibradas
    if (mind_n_ID == 0 &&
        fabs(mind_n_ul_izq - mind_n_ul_der) < TOL_ALINEACION_CM) {
      Serial.println(F("[ALIN] Centrado OK, saliendo antes."));
      break;
    }
    delay(20);
  }
}


// =================== Arranque suave ===================
void arranqueSuave() {
  Serial.println(F("Arranque suave..."));
  unsigned long t0 = millis();
  while (millis() - t0 < SOFT_START_MS) {
    float frac = (float)(millis() - t0) / (float)SOFT_START_MS;
    int pwm = VEL_INICIAL + (int)((VELOCIDAD_PWM - VEL_INICIAL) * frac);

    int16_t gx, gy, gz;
    mpu.getRotation(&gx, &gy, &gz);
    unsigned long t_ahora_us = micros();
    float dt = (t_ahora_us - t_anterior_us) / 1000000.0;
    t_anterior_us = t_ahora_us;
    float gyroZ_dps = ((float)gz - gyroZ_offset) / GYRO_LSB_POR_DPS;
    yaw += gyroZ_dps * dt;

    avanzarConPWM(pwm);
    delay(10);
  }
}


// =================== Setup ===================
void setup() {
  Serial.begin(9600);
  openmv.begin(OPENMV_BAUD);
  Wire.begin();
  Wire.setClock(400000);

  pinMode(10, OUTPUT);
  pinMode(9,  OUTPUT);
  pinMode(11, OUTPUT);

  servo_8.attach(8);
  servo_8.angle(90);

  mpu.initialize();
  if (!mpu.testConnection()) {
    Serial.println(F("ERROR: MPU6050 no responde."));
    while (true) { delay(500); }
  }
  Serial.println(F("MPU6050 conectado."));
  mpu.setFullScaleGyroRange(MPU6050_GYRO_FS_250);

  delay(1500);
  calibrarGiroscopio();
  inicializarFiltros();

  // Limpiar buffer OpenMV (basura de arranque)
  while (openmv.available()) openmv.read();
  t_ultimo_msg = millis();

  alinearInicio();

  Serial.println(F("Iniciando recorrido..."));
  t_inicio = millis();
  t_vuelta[0] = t_inicio;
  t_anterior_us = micros();

  arranqueSuave();
}


// =================== Loop ===================
void loop() {
  if (detenido) {
    detenerMotores();
    return;
  }

  // ---- 1) Yaw ----
  int16_t gx, gy, gz;
  mpu.getRotation(&gx, &gy, &gz);

  unsigned long t_ahora_us = micros();
  float dt = (t_ahora_us - t_anterior_us) / 1000000.0;
  t_anterior_us = t_ahora_us;

  float gyroZ_dps = ((float)gz - gyroZ_offset) / GYRO_LSB_POR_DPS;
  yaw += gyroZ_dps * dt;

  float yaw_abs = fabs(yaw);

  // ---- 2) Contador de vueltas ----
  int vueltas_actual = (int)(yaw_abs / ANGULO_VUELTA);
  if (vueltas_actual > contador_vueltas && vueltas_actual <= VUELTAS_OBJETIVO) {
    contador_vueltas = vueltas_actual;
    t_vuelta[contador_vueltas] = millis();

    unsigned long ms_acumulados = t_vuelta[contador_vueltas] - t_inicio;
    Serial.print(F(">>> Vuelta "));
    Serial.print(contador_vueltas);
    Serial.print(F(" en "));
    Serial.print(ms_acumulados / 1000.0, 2);
    Serial.println(F(" s"));

    if (contador_vueltas == 1) {
      unsigned long T1 = t_vuelta[1] - t_vuelta[0];
      t_objetivo_paro = t_vuelta[1] + 2UL * T1 + (unsigned long)OFFSET_PARO_MS;
      prediccion_lista = true;
    }
    else if (contador_vueltas == 2) {
      unsigned long T2 = t_vuelta[2] - t_vuelta[1];
      t_objetivo_paro = t_vuelta[2] + T2 + (unsigned long)OFFSET_PARO_MS;
    }
  }

  // ---- 3) Paro por tiempo ----
  if (prediccion_lista && millis() >= t_objetivo_paro) {
    detenido = true;
    frenarActivo();
    Serial.print(F(">>> DETENIDO POR TIEMPO. Yaw "));
    Serial.println(yaw, 2);
    return;
  }

  // ---- 4) Respaldo por giro ----
  if (yaw_abs >= ANGULO_RESPALDO) {
    detenido = true;
    frenarActivo();
    Serial.print(F(">>> DETENIDO POR GIRO. Yaw "));
    Serial.println(yaw, 2);
    return;
  }

  // ---- 5) Control: pilares + paredes ----
  seguirCarril();

  // ---- 6) Telemetría ----
  Serial.print(F("ID "));   Serial.print(mind_n_ID);
  Serial.print(F(" X "));   Serial.print(mind_n_X);
  Serial.print(F(" A "));   Serial.print(mind_n_AREA);
  Serial.print(F(" der ")); Serial.print(mind_n_ul_der);
  Serial.print(F(" izq ")); Serial.print(mind_n_ul_izq);
  Serial.print(F(" err ")); Serial.print(mind_n_ERROR);
  Serial.print(F(" yaw ")); Serial.print(yaw, 1);
  Serial.print(F(" v "));   Serial.print(contador_vueltas);
  Serial.println();

  // -------- Gancho opcional: reducir velocidad cuando el pilar está cerca --------
  // Si quieres, dentro de avanzarConPWM puedes bajar el PWM cuando
  // mind_n_AREA supere algún umbral, p. ej.:
  //
  //   int pwm_uso = VELOCIDAD_PWM;
  //   if (idActivoConLatch() != 0 && mind_n_AREA > 4000) pwm_uso -= 15;
  //   avanzarConPWM(pwm_uso);
  //
  // Útil para no embestir el pilar si tu chasis derrapa a velocidad alta.
}

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/1-35.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/36-70.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/70-104.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/104-138.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/139-173.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/174-207.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/207-241.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/242-275.png" /></div>
<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/276-310.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/311-344.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/345-378.png" /></div>
<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/379-412.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/413-446.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/447-481.png" /></div>

<div align="center"><img width="1000" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/0375450821a12ab30b87c88115721aebb2d8e235/video/481-490.png" /></div>

