# Mini Swarm Vehicle

A small, modular ground robot built as a platform for developing and testing swarm-robotics and autonomous-navigation concepts — self-designed mechanical chassis, electronics, and low-level motor control, driven by an NVIDIA Jetson for onboard autonomy.

![Assembled vehicle](images/Image_render.png)

**Features:**
- ROS 2-compatible 2WD mobile platform for indoor applications 
- Robust 3D printed plates with DC encoder motors attached with coupling through plastic rubber tyres
- Differential drive platform
- Supports custom robotic algorithm development, including navigation, SLAM, and perception    
## Overview
The vehicle is a compact two-wheel differential-drive robot (2 rear geared DC motors + 1 passive front caster) with onboard IMU, 2D LiDAR, and a depth camera for perception, intended to operate individually or as part of a coordinated swarm. It's built around a 3-plate 3D-printed chassis, an Arduino-based low-level motor/encoder controller, and a Jetson Nano/Raspberry pi for higher-level compute — the same compute/sensing pattern later reused as a multi-vehicle swarm navigation platform.

![Block_Diagran](images/System_Overview.png)

## Specifications
| Parameter | Value |
|---|---|
| Drive | 2WD, differential  |
| Dimensions (L × W × H) | 212 × 215 × 11 mm |
| Weight | 1.5 kg  |
| Controller | Jetson Nano / Raspberry Pi 5 & Arduino Nano |
| Sensors | 2D LIDAR, Camera, IMU |
| Motor | DC Motor with encoder  |
| Wheel  | 65mm diameter |
| Battery | 11.1V , 3500mah  |
## Hardware Design
### Mechanical
- **Frame:** 3 plates (bottom, middle, top), FDM 3D-printed in CPE (copolyester) — chosen for impact resistance, flexibility, and dimensional stability.
  - **Bottom plate** — mounts the drive motors, caster wheel, and power electronics, with reinforced ribs at the motor mounts.
  - **Middle plate** — hosts the Jetson module and camera, with routed cable-management slots.
  - **Top plate** — carries the 2D LiDAR on a raised standoff mount, plus ventilation/wiring cutouts.
- **Validation:** structure was checked under a 2 kg payload via FEA, confirming minimal deformation at the reinforced motor-mount regions.
- **Assembly:** modular sandwich construction held together with M3 screws/standoffs for easy maintenance and component swaps.

![Chassis CAD](images/Chassis_CAD_1.png)

- **Chassis Drawing:** Chassis plate drawings in [`docs/Chassis_Plates.pdf`](docs/Chassis_Plates.pdf).

## Electronics

Schematic and PCB project designed in Altium (`hardware/altium/`): 

A two-wheel drive-controller board (`Main.SchDoc`) alongside the system block diagram (`Block_Diagram.SchDoc`). PDF export in `docs/Schematic.pdf`; system-level electrical/architecture diagrams in `docs/Electrical_Block_Diagram.vsdx` and `docs/System_Overview.vsdx`.
- **Electronics_Schematics:** [`docs/Schematic.pdf`](docs/Schematic.pdf)

**Core hardware:** Arduino (low-level motor/encoder control), Jetson Nano (onboard compute), encoder DC motors + motor driver shield, IMU, 2D LiDAR, depth camera, Li-ion battery.

![Block_Diagran](images/Electronics_Block_Diagran.png)

- **Electronics_BOM:** BOM is available in[`docs/Robotic_Car_BOM.xlsx`](docs/Robotic_Car_BOM.xlsx)
- **Electronics_Wiring:** Hardware wiring is available in[`docs/Robotic_Car_Hardware_Wiring.xlsx`](docs/Robotic_Car_Hardware_Wiring.xlsx)
## Firmware 

`firmware/ROSArduinoBridge/` is the Arduino-side motor/encoder driver, based on the open-source [ROSArduinoBridge](https://github.com/joshnewans/ros_arduino_bridge) library (included here largely unmodified for completeness; see `UPSTREAM_README.md` for the original project). It exposes a serial command interface so a high-level computer (e.g. running ROS) can drive the motors and read back encoder data.

## Vehicle Images
The complete vehicle assembled images is available in [`images/Vehicle_Images`](images/Vehicle_Images)

![Vehicle_Built1](images/Vehicle_Images/Vehicle_Built1.jpg)

![Vehicle_Built1](images/Vehicle_Images/Vehicle_Built3.jpg)
## Repository Structure

```
hardware/altium/   Altium project — block diagram & drive-controller schematic, schematic library
firmware/          Arduino motor/encoder controller (ROSArduinoBridge-based)
docs/              Technical description, mechanical structure doc, chassis plate drawings, schematic PDF, block/system diagrams
images/            Assembly photos, built vehicle, chassis CAD renders
```

## Author
This project is done by the following team members
- Dr.Muhammad Ahmad Kamran (Team Lead), AITeC Islamabad
- Muhammad Huzaifa Rahim Malik (Electronics Design), AITeC Islamabad
- Muhammad Mohsin Razaqi (Mechanical Design), AITeC Islamabad 
- Muhammad Awais Khan (Algorithm Design), AITeC Islamabad
