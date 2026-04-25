Electromechanical diagrams
====

This directory contain several schematic diagrams in form of JPEG, PNG or PDF of the electromechanical components illustrating all the elements (electronic components and motors)

------
PCB
---
<div align="center"><img width="700" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/cd8feb88dfa141fe0f5516e4ea4622495802dc7b/PCB%202026-04-11%20092141.png" /></div>

## HuskyLens DFRobot

<div align="center">
<img width="600" height="800" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/87e61e8ba56e91d8834b0b9088b2661a81aa32ee/Camara.jpeg" />
</div>

<div align="center">

|**Parametrer**|**Description**|
|--------------|---------------|
| **Operating Voltage**      | 5V DC|
| **Operating Current**      |230mA (Typical)|
| **Frequency**              |400 kHz|
| **Max Range**              |02.0m|
| **Min Range**              |0.5m|
| **Accuracy**               |30f/s
| **Measuring Angle**        | 320x240|
| **Trigger Input Signal**   |I2C or UART (serial)|
| **Echo Output Signal**     |serial UART or I2C interface|
| **Dimensions**             |52mm x 44.5mm|
| **Working Temperature**    |36.4°C to 43.2°C|


We conect the Husky DFRobot using screws in the legos piece to unite them, we conect them with the PCB and program it.

### Medium motor EV3
<div align="center">
<img width="500" height="400" alt="EV3 Medium Motor" src="https://github.com/user-attachments/assets/c0c91a6f-02e9-4b4f-8271-1a25b965384c" />
</div>

## Specifications

<div align="center">
  
| Specification              | Description |
|-----------------------------|-------------|
| **Model**                   | EV3 Medium Motor (45503) |
| **Type**                    | Servo Motor with integrated rotation sensor |
| **Operating Voltage**       | 9 V DC |
| **Nominal Power**           | 2.5 W |
| **No-load Speed**           | ~260 rpm |
| **Stall Torque**            | ~8 N·cm (0.08 N·m) |
| **Stall Current**           | ~0.7 A |
| **Tacho Sensor Resolution** | 1° (360 counts per rotation) |
| **Weight**                  | 75 g |
| **Cable Length**            | 25 cm |
| **Connector Type**          | EV3 Plug |
| **Control Features**        | Speed, position, and direction control | 


It operates at 9V DC and is designed to provide a balance between speed and torque, making it suitable for medium-load
We connect these motors directly to the EV3, and control them directly with the programming block for these motors, which in our robot consumes the following:

EV3 Brick controls all motors. Powered by 10V 2050 mAh battery.  
- **Movement motors (2):** 150–250 mA  
- **Steering motor (1):** 120–250 mA
  
In terms of power consumption, the motor typically draws around 250–300 mA under normal load, but this can increase up to 1.2 A when operating under heavy load or stall conditions. Its power output reaches approximately 2.5 W, depending on the applied voltage and load.
