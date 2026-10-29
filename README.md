# Mini Swarm Vehicle

A small, modular ground robot built as a platform for developing and testing swarm-robotics and autonomous-navigation concepts — self-designed mechanical chassis, electronics, and low-level motor control, driven by an NVIDIA Jetson for onboard autonomy.

![Assembled vehicle](images/Image_render.png)

**Features:**
- ROS 2-compatible 2WD mobile platform for indoor applications (this repo holds the hardware, electronics and Arduino firmware; high-level ROS software is not included)
- Robust 3D printed plates with DC encoder motors attached with coupling through plastic rubber tyres
- Differential drive platform
- Supports custom robotic algorithm development, including navigation, SLAM, and perception

## Overview
The vehicle is a compact two-wheel differential-drive robot (2 rear geared DC motors + 1 passive front caster) with an IMU (MPU-6050), a 2D LiDAR (YDLIDAR G4) and a depth camera (RealSense D435) for perception, intended to operate individually or as part of a coordinated swarm. It's built around a 3-plate 3D-printed chassis, an Arduino Nano low-level motor/encoder controller, and a Jetson Nano (a Raspberry Pi 5 is listed in the BOM as an alternative) for higher-level compute. The Arduino and the Jetson talk over a USB serial link. It is used as the follower robot in the [multi-robot navigation and tracking project](https://github.com/Huzaifa-Rahim-Malik/LLM-Multi-Robot-Navigation-Tracking).

![System_Overview](images/System_Overview.png)

## Specifications
| Parameter | Value |
|---|---|
| Drive | 2WD differential (2 rear driven wheels + 1 front ball caster) |
| Chassis plates | Bottom and centre 212 × 150 mm, top 150 × 150 mm, 3 mm thick; battery holder 99 × 64 mm |
| Overall dimensions (L × W × H) | TODO: measure the assembled vehicle |
| Weight | 1.5 kg |
| Controller (high-level) | NVIDIA Jetson Nano (Raspberry Pi 5 as alternative) |
| Controller (low-level) | Arduino Nano V3 |
| Motor driver | L298N H-bridge module |
| Motors | 2 × GA25-370 (JGA25-371) geared DC motor with quadrature encoder, 12 V, 280 RPM |
| Wheels | 65 mm diameter × 27 mm wide rubber tyres; 20 mm ball caster |
| Sensors | MPU-6050 IMU, YDLIDAR G4 2D LiDAR, Intel RealSense D435 depth camera |
| Battery | 11.1 V, 3500 mAh Li-ion; about 1.5–2 h runtime at full load |

## Hardware Design
### Mechanical
- **Frame:** 3 plates (bottom, middle, top) plus a battery holder, FDM 3D-printed in CPE — chosen for impact resistance, flexibility, and dimensional stability. Plates are spaced with 55 mm brass standoffs.
  - **Bottom plate** — mounts the drive motors, caster wheel, and power electronics, with reinforced ribs at the motor mounts.
  - **Middle plate** — hosts the Jetson module and camera, with routed cable-management slots.
  - **Top plate** — carries the 2D LiDAR on a raised standoff mount, plus ventilation/wiring cutouts.
- **Validation:** structure was checked under a 2 kg payload via FEA, confirming minimal deformation at the reinforced motor-mount regions.
- **Assembly:** modular sandwich construction held together with M3 screws and brass standoffs for easy maintenance and component swaps. The battery holder screws onto the bottom plate so the battery can be swapped without dismantling the robot.

![Chassis CAD](images/Chassis_CAD_1.png)

- **Chassis drawing:** [`docs/Chassis_Plates.pdf`](docs/Chassis_Plates.pdf)
- **Technical description:** [`docs/Technical_Description.pdf`](docs/Technical_Description.pdf) (also as `.docx`)

## Electronics
Schematic and PCB project designed in Altium (`hardware/altium/`): 

A two-wheel drive-controller board (`Main.SchDoc`) alongside the system block diagram (`Block_Diagram.SchDoc`). 
- **Electronics_Schematics:** [`docs/Schematic.pdf`](docs/Schematic.pdf)
- **Electronics_BOM:** BOM is available in [`docs/Robotic_Car_BOM.xlsx`](docs/Robotic_Car_BOM.xlsx)
- **Electronics_Wiring:** Hardware wiring is available in [`docs/Robotic_Car_Hardware_Wiring.xlsx`](docs/Robotic_Car_Hardware_Wiring.xlsx)

**Control architecture:**

The Mini Swarm Vehicle divides the system into two sections:
1. **Power distribution:** 11.1 V battery pack → motor driver shield, regulating down to drive two independent DC gear-motor channels directly from the pack.
2. **Motor control (Arduino + driver shield):** An Arduino handles low-level motor control, interfacing with the L298N driver over `IN1–IN4`: the Arduino outputs PWM on the four input pins (D5, D6, D9, D10) for speed and direction, while `ENA`/`ENB` are kept enabled (jumpered to 5 V / driven HIGH by the firmware).
    - Each drive motor is a DC geared motor with an integrated quadrature encoder (`Motor+`/`Motor−` for drive, `+5V`/`GND` for the encoder supply, `SIG_FBK1`/`SIG_FBK2` for A/B encoder channels), feeding wheel speed/position back to the Arduino.
    - The firmware's PID-based differential-drive controller (`diff_controller.h` in `firmware/ROSArduinoBridge/`) closes the loop on encoder feedback and exposes a serial command interface, keeping real-time motor control on the Arduino, decoupled from trajectory/motion planning on the Jetson.

![Electronics_Block_Diagram](images/Electronics_Block_Diagram.png)


## Firmware

`firmware/ROSArduinoBridge/` is the Arduino-side motor/encoder driver, based on the open-source [ROSArduinoBridge](https://github.com/joshnewans/ros_arduino_bridge) library (included largely unmodified; see `UPSTREAM_README.md` for the original project). It is configured for the L298 motor driver and talks to the high-level computer over USB serial at 57600 baud, so a computer can drive the motors and read back encoder data.

The upstream project is a ROS 1 stack, but the Arduino sketch itself only speaks this serial protocol and does not depend on a ROS version. A ROS 2 driver node on the Jetson/PC side is not part of this repository.

## Vehicle Images
All assembly and finished-vehicle photos are in [`images/Vehicle_Images`](images/Vehicle_Images).

![Vehicle_Built1](images/Vehicle_Images/Vehicle_Built1.jpg)
![Vehicle_Built2](images/Vehicle_Images/Vehicle_Built2.jpg)
![Vehicle_Built3](images/Vehicle_Images/Vehicle_Built3.jpg)
![Vehicle_Built4](images/Vehicle_Images/Vehicle_Built4.jpg)

![Assembly_1](images/Vehicle_Images/Assembly_1.jpg)
![Assembly_2](images/Vehicle_Images/Assembly_2.jpg)
![Assembly_3](images/Vehicle_Images/Assembly_3.jpg)

## Repository Structure

```
Mini_Swarm_vehicle/
├── hardware/altium/       Altium project: block diagram & drive-controller schematic, schematic library
├── firmware/              Arduino motor/encoder controller (ROSArduinoBridge-based, L298 driver)
├── docs/                  Technical description, chassis plate drawings, schematic PDF, BOM, hardware wiring
└── images/                Assembly photos (Vehicle_Images/), built vehicle, chassis CAD renders, system and block diagrams
```

## Author
This project is done by the following team members
- Dr.Muhammad Ahmad Kamran (Team Lead), AITeC Islamabad
- Muhammad Huzaifa Rahim Malik (Electronics Design), AITeC Islamabad
- Muhammad Mohsin Razaqi (Mechanical Design), AITeC Islamabad 
- Muhammad Awais Khan (Algorithm Design), AITeC Islamabad
