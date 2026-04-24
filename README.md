WRO-2026-FUTURE ENGINEERS
---------------------------
Official repository of Team Los Grises Junior for the WORLD ROBOT OLYMPIAD 2026-Category: Future-Engineers – World Robot Olympiad 2026.
<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/e16448bbbef45e752d7e7cce1f7787d7d7da0b3f/s%20(1).jpg" /></div>

## TEAM PHOTO
<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/48995af1c66a6dbecf6ee10ec0bb18f319f49acc/Foto%20divertida%20equipo.jpeg" /></div>


TEAM MEMBERS
---------
Coach:Eduardo Alvarado Gonzalez
<div align="center">
<img width="400" height="300" alt="Eduardo Gonzalez" src="https://github.com/user-attachments/assets/20ccfb81-0bc1-4147-aa43-fefe649a55c0" />
</div>
Age:40

Role: Coach and founder.

"An engineer and professor founded the Los Grises Superiores in 2014, since then with outstanding national and international participations."

---------------------------

Barbara Arlee Flores Marroquin

Age:15

<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/d7c3253b324f993227be77801cb9ee21452fcf70/Photo%20Barbara.jpg" /></div>

Role:Captain and electronic 

"I got started in robotics in my sophomore year of high school, having been selected for my strong academic performance and adaptability, as well as my interest in challenges and complex designs, and I received a diploma for my achievements.I have honed my skills by taking additional robotics courses at this institution, where, in addition to learning, I received a certificate of completion."

---------------------------

Arath Alejandro Reyna Granados

Age:15

<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/a03ef2db1c7b82816e94747b85b8fe7dd413cee5/Arath.jpeg" /></div>

Role: Programmer

"I started in robotics at age 13 thanks to my mom, who started a robotics course at my school. There, I was told about a summer course offered by the Normal Superior of Nuevo León, Mexico, where I learned many things. After the course ended, a few months passed, and we were invited back. I was selected to compete in WRO."

---------------------------

Alexis Fernando Martínez Tapia

Age:14

<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/6fc438b44809f8b7383caa233e0a7e4161a2b77c/Alexis.jpeg" /></div>

Role: Mechanic

"I started the robotic for first time at the age of 10 years, i thake another course in Normal Superior Mexico Nuevo Leon in finals of 2024 and i go to Normal Superior to take another course of robotics where i am gona to participate in mi first tournament in Los Grises Junior."

---------------------------

## 📚 Contents
- [Project Overview](#project-overview)
- [Vehicle Photos](#vehicle-photos)
- [Components and Hardware](#components-and-hardware)
- [Mobility Management](#mobility-management)
  - [Chassis](#chassis)
  - [Steering System](#steering-system)
  - [Movement and Traction System](#movement-and-traction-system)
  - [Differential](#about-differential)
- [Documentation](#Documentation)
- [Power and Sense Management](#power-and-sense-management)
  - [Power Management](#power-management)
  - [Sense Management](#sense-management)
- [Obstacle Management](#obstacle-management)
- [WRO 2026 Competition Info](#wro-2026-competition-info)
  
## Project overview

This document describes the design and implementation of an autonomous vehicle built to compete in the World Robot Olympiad 2026, under the category known as Future Engineers. This competition requires teams to conceive, assemble, and program a vehicle capable of operating without human intervention, adapting to different environmental conditions and established rules.
The Future Engineers category is divided into two challenges:

Open Challenge

The vehicle must autonomously complete three full laps on a fixed-layout track.
Both the starting position and the driving direction (clockwise or counterclockwise) are randomly determined before each attempt.

Obstacle Challenge

It follows the same dynamics as the Open Challenge, with the difference that the vehicle must identify and respond to colored pillars that act as traffic signals:

Red pillars → Turn right
Green pillars → Turn left


<div align="center"><img width="400" height="300" alt="OpenMV Cam H7 Plus" src="https://github.com/AlexisNinja2026/Borrador-/blob/c52217912910eeeb9e481bed0007484d9a322ab2/WRO%20obstacles.png" /></div>

Vehicle's photos
====

<div align="center">

| Front | Back |
|:--:|:--:|
|    |    |

| Bottom | Top |
|:--:|:--:|
|    |    |

| Left | Right |
|:--:|:--:|
|    |     |


## Components and Hardware
====

|COMPONENT| DESCRIPTION | IMAGE | PURCHASE LINK|
|---------|-------------|-------|--------------|
|Arduino Nano  |ATmega328-based microcontroller for control tasks| <div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/user-attachments/assets/0d45ec5e-d666-4185-be01-94dc062a9f37" /></div> |[Buy here](https://www.google.com/aclk?sa=L&ai=DChsSEwiB6uqh3eOTAxWqswMAHZ9CG58YACICCAEQARoCb2E&co=1&ase=2&gclid=Cj0KCQjwv-LOBhCdARIsAM5hdKe3TtkoMagErxFPnI8yuVRjH7gES3rhXEC8jofcvzb60-d1IdbEuBYaAiyPEALw_wcB&cid=CAASugHkaNjVthr8YFbnDwsn-roKy5t_YsC0Mkf3bF607XqWIcotDRZXOEbzPQZ5nr1VZkA1VNJ6WE--_9-trzQm35iiBOJzKicUHLUJk9ugnfKfBixW5bOISn9DYFt-pzZbHnLkNRL0bNpLc21i4OJkTdH7hXmE3RM3vbdCYAMSlBqU6U1PcKNI4YkK5Me2xgzHLz56A91I4ZKQZ3mSOYcxTaT57QZ6zCuujbiTu72hyncfjgrtHyeJ84o4TQA&cce=2&category=acrcp_v1_33&sig=AOD64_2fR0450xwF3ggpGZB719NCMgWYtQ&ctype=5&q=&nis=4&ved=2ahUKEwiL6-Ch3eOTAxUomSYFHeyqHckQ9aACKAB6BAgMEBA&adurl=) |
|HuskyLens DFRobot|Is an AI-powered machine vision sensor designed to detect faces, objects, lines, colors, and tags without complex algorithms.| <div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/6169b3e8b34ef922ef62c5e202158eb07e2cbc6e/Camara.jpeg" /></div> |[Buy here](https://a.co/d/0gZv6Qfk)|
|MPU6050| Is a 6-axis Motion Tracking device combining a 3-axis gyroscope and a 3-axis accelerometer on a single chip.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/495ec39225eaf2b197f723f051676fdd03c3bafd/WhatsApp%20Image%202026-04-11%20at%2012.01.33%20PM.jpeg" /></div> |[Buy here](https://a.co/d/0gZv6Qfk)|
|TB6612FNG|Is a compact, high-efficiency dual H-bridge motor driver capable of independently controlling two DC motors, at 1.2A continuous current.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/09027d427a44b83a59869e85d09dbbb3274b0b93/TB6612FNG.jpeg" /></div> |[Buy here](https://a.co/d/0eNh6qiz)|
|EV3 Motor|Are intelligent, tacho-feedback servos with built-in rotation sensors (1-degree resolution) for precise, Auto-ID control..|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/user-attachments/assets/c0c91a6f-02e9-4b4f-8271-1a25b965384c" /></div> |[Buy here](https://ebay.us/m/QtXLZA)|
|Ultrasonic Sensor|Is an ultrasonic distance sensor that uses sonar to determine the distance to an object.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/09027d427a44b83a59869e85d09dbbb3274b0b93/WhatsApp%20Image%202026-04-09%20at%203.25.49%20PM.jpeg" /></div> |[Buy here](https://www.steren.com.mx/sensor-ultrasonico.html)|
|Servomotor|Is a rotary or linear actuator designed for high-precision control of position, speed, and acceleration, typically operating within a closed-loop system.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/ffb3b1e2eb393abe945b06120520b39b8c333b27/servo.jpg" /></div> |[Buy here](https://www.steren.com.mx/micro-servomotor-con-torque-de-1-8-kgf-cm.html)|
|18650 Li-ion Rechargeable Battery 2200 mAh|Is a cylindrical rechargeable lithium-ion cell with a capacity of 2200 mAh, commonly used in portable electronics and power devices, measuring approximately 18 mm in diameter and 65 mm in length.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/6ece9ad176f7af3fbcfbfabdd9c675156f8046a3/bateria-recargable-li-ion-2200-mah-tipo-18650.jpg" /></div> |[Buy here](https://www.steren.com.mx/bateria-recargable-li-ion-2200-mah-tipo-18650.html?srsltid=AfmBOoqVXbVVKKaVMNmSyXv6rzEmUdTJ3o1khVPWLHKSNEBCjl5N5MGprtI)|
|Mini560 Buck Step-Down Voltage Regulator 5–20V to 3.3V 5A|Is a compact DC-DC buck converter designed to step down an input voltage from 5–20V to a stable 3.3V output, supporting up to 5A of current, commonly used for powering microcontrollers and low-voltage electronic circuits.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/93bdb984c063d3ec5457223a3d6dfa9a36d276a4/Mini560%20Regulador%20Step%20Down%20Buck%205-20v%20A%203.3v%205a.webp" /></div> |[Buy here](https://www.steren.com.mx/regulador-de-voltaje-positivo-5v-1a.html?srsltid=AfmBOop5WdfK0k6EYuWDN6c5NZKdqrNnHo_tHIPNIs3u9HatMaUCIQl2CDg)|
|5V/1A positive voltage regulator|Is a linear positive voltage regulator designed to provide a stable 5V output with up to 1A of current, commonly used in power supply circuits to regulate and maintain a constant voltage for electronic components.|<div align="center"><img width="500" height="250" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/b0b09cf5b315e9625422a291d922a3d7fb5e0e03/Regulator.jpg" /></div> |[Buy here](https://www.steren.com.mx/regulador-de-voltaje-positivo-5v-1a.html?srsltid=AfmBOop5WdfK0k6EYuWDN6c5NZKdqrNnHo_tHIPNIs3u9HatMaUCIQl2CDg)

---------------
## Mobility Management

# Chassis
----
## Power and Sense Management
---
The Power and Sensor Management system of the vehicle is engineered to optimize energy distribution and ensure reliable operation of all electronic components. It carefully regulates the power supply to motors and sensors, maintaining stability and efficiency under varying load conditions. This system enables precise control over sensor readings and motor performance, supporting accurate navigation, obstacle detection, and overall vehicle functionality.

## Power Management
------------
### Battery 18650
<div align="center"><img width="600" height="350" alt="image" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/6ece9ad176f7af3fbcfbfabdd9c675156f8046a3/bateria-recargable-li-ion-2200-mah-tipo-18650.jpg" /></div>

## Specifications:

<div align="center">

| Specification           | Description                   |
|-------------------------|-------------------------------|
| **Type**                | Li-ion Rechargeable           |
| **Model**               | 18650                         |
| **Nominal Voltage**     | 3.7 V                         |
| **Capacity**            | 2200 mAh                      |
| **Enclosure**           | Metallic (for safety)         |
| **Active Element**      | Lithium-ion                   |
| **Dimensions**          | 18 mm diameter × 65 mm length |

</div>

The 18650 Li-ion Rechargeable Battery is a cylindrical metallic-encased cell 
with a nominal voltage of 3.7V and a capacity of 2200 mAh, making it suitable 
for high-consumption electronic applications and robotics projects.

In this setup, we use two 18650 batteries connected in series to provide a 
combined output voltage of approximately 7.4V. This voltage is then regulated 
through a DC-DC buck converter to supply stable power to the Arduino Nano and 
the ultrasonic sensors, ensuring consistent operation throughout the run.

## Sense Management
------
### Microcontroller(Arduino nano)
<div align="center"><img width="600" height="350" alt="OpenMV Cam H7 Plus" src="https://github.com/user-attachments/assets/0d45ec5e-d666-4185-be01-94dc062a9f37" /></div>

## Specifications:

<div align="center">

| Specification            | Description |
|---------------------------|-------------|
| **Microcontroller**       | ATmega328P |
| **Operating Voltage**     | 5 V |
| **Input Voltage (recommended)** | 7–12 V |
| **Input Voltage (limit)** | 6–20 V |
| **Digital I/O Pins**      | 14 (6 PWM outputs) |
| **Analog Input Pins**     | 8 |
| **DC Current per I/O Pin** | 40 mA |
| **Flash Memory**          | 32 KB (2 KB used by bootloader) |
| **SRAM**                  | 2 KB |
| **EEPROM**                | 1 KB |
| **Clock Speed**           | 16 MHz |
| **USB Connection**        | Mini USB |
| **Dimensions**            | 45 mm x 18 mm |
| **Weight**                | ~7 g |

</div>

The Arduino Nano is a compact,microcontroller board based on the ATmega328P. Despite its small size, it offers full functionality with 14 digital I/O pins, 8 analog inputs, and a 16 MHz clock speed.
It operates at 5V and can be powered through a Mini USB connection, a regulated 5V pin, or an external 7–12V input,

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

</div>

It operates at 9V DC and is designed to provide a balance between speed and torque, making it suitable for medium-load
We connect these motors directly to the EV3, and control them directly with the programming block for these motors, which in our robot consumes the following:

EV3 Brick controls all motors. Powered by 10V 2050 mAh battery.  
- **Movement motors (2):** 150–250 mA  
- **Steering motor (1):** 120–250 mA
  
In terms of power consumption, the motor typically draws around 250–300 mA under normal load, but this can increase up to 1.2 A when operating under heavy load or stall conditions. Its power output reaches approximately 2.5 W, depending on the applied voltage and load.

## Ultrasonic HC-SR04

<div align="center">
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/da485b0f-8730-43e4-9475-f638a68670e4" />
</div>


## Specifications:

<div align="center">

| **Parameter**             | **Description**                              |
|----------------------------|----------------------------------------------|
| **Operating Voltage**      | 5V DC                                        |
| **Operating Current**      | 15 mA (typical)                              |
| **Frequency**              | 40 kHz                                       |
| **Max Range**              | 400 cm (4 m)                                 |
| **Min Range**              | 2 cm                                         |
| **Accuracy**               | ±3 mm                                        |
| **Measuring Angle**        | < 15°                                        |
| **Trigger Input Signal**   | 10 µs TTL pulse                              |
| **Echo Output Signal**     | TTL signal, proportional to distance         |
| **Dimensions**             | 45 mm x 20 mm x 15 mm                        |
| **Working Temperature**    | -15°C to +70°C                               |

</div>

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

</div>

--------------------
<div align="left">
## Obstacle management

The obstacle avoidance system was designed in accordance with the competition rules: a **red pillar** indicates a right turn, while a **green pillar** indicates a left turn.  
To determine the correct avoidance side, the robot calculates the difference between the **detected X position** from the camera and a predefined **X target**.  
This approach ensures a reliable and consistent maneuver direction, improving the robot’s decision-making during navigation.
he vision system was also designed to support the obstacle avoidance logic.
For this purpose, the X-coordinate at the base of the detected blob was used as the main metric, providing more reliable results than using the object’s area, which occasionally produced inconsistent data.

------
PCB
---
<div align="center"><img width="700" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/cd8feb88dfa141fe0f5516e4ea4622495802dc7b/PCB%202026-04-11%20092141.png" /></div>

------
# Documentation


This is the process and documentation section during the WRO|Future Engineers event on building our autonomous robot

  Chasis prototype
 -
  <div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/9585aa53627c91c51d5f9f3e4480d6173736c801/Chasis%20prototype%201.png" /></div>
  This was our first design of our lego chasis.

  Robot prototype
  -
  This is our first design robot, whith a little part of electronic components (ultrasonic sensor)
<div align="center"><img width="600" height="500" alt="OpenMV Cam H7 Plus" src="https://github.com/barbaraarlee1726-dot/WRO-2026-Superiores-Junior/blob/bd4cadf06a61e07020c7968a2cfd60552cc2694a/other/Chasis%20prototype%202.png" /></div>

Final Design
-
This was our final design for the WRO Future Engineers, this robot was used on WRO Future Engineers.


-------
## WRO 2026 Competition Info
----
[WRO 2026 RULES Future Engineers English](https://wro-association.org/wp-content/uploads/WRO-2025-Future-Engineers-Self-Driving-Cars-General-Rules.pdf)

[WRO 2026 RULES Future Engineers Spanish](https://drive.google.com/file/d/1IJEXWpP0N-TZuE2kj__HQJZUOQY-1Yf7/view?pli=1) 
