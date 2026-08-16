---
title: "Humanoid Robot"
excerpt: Joint module, robotic hands, and overall humanoid robot solution.
layout: single
classes: wide
order: 2
math: true
---

This is the major work I did at STMicroelectronics, which aims to promote new technologies based on the ST ecosystem.

As a System Application Engineer at ST, I focus on developing solutions for the **Smart Industrial** area using ST products.

One key area we have recently focused on is the **Humanoid Robot**, including the **Joint Module**, **Robotic Hands**, and **Sensors**.

# Joint Module

Humanoid robotics is entering a scale-up phase, and the joint module is one of the most decisive parts for performance, cost, and reliability. A single humanoid robot typically requires dozens of joint modules distributed across the neck, shoulders, elbows, wrists, hips, knees, and ankles.

More importantly, a “joint module” is not a simple motor. It is a tightly integrated mechatronic system that combines a high-torque density motor, a precision reducer (often a harmonic drive), dual encoders (rotor and output-axis), a brake for safe holding, and frequently a torque sensor for compliant control—along with the embedded servo drive electronics that manage power conversion, sensing, protection, real-time control, and communication in a compact, thermally constrained package.

![Humanoid robot joint](https://pic.dwgan.top/img/20260816134006.png)

Our primary focus is the servo drive (drive board) inside the joint module—delivering high-performance motor control electronics and firmware that enable stable torque/position control, robust protection, and scalable integration for next-generation humanoid actuators.

## Joint classes and requirements

A humanoid robot mixes very different joint profiles. Following the framework of ST application note AN6569, we group them into three classes:

- **High-power joints** (hips, knees, ankles, torso, high-load shoulders): typically 48–60 V, from about 500 W to several kilowatts, with peak current that can exceed 100 A. The main design priority is current capability, thermal margin, and fast fault reaction.
- **Compact mid-power joints** (elbows, wrists, load-bearing neck axes): typically 48–60 V, about 50–500 W, often 5–20 A. The main design priority is power density, efficiency, and control response.
- **Auxiliary / low-power joints** (neck or head adjustment, small local servos): usually below 50 W. The main design priority is compact integration, quiet motion, and reduced wiring.

These classes use different power-stage integration levels, but they can share common STM32 control, sensing, communication, and software concepts.

![Typical humanoid joint locations and motor control classes](https://pic.dwgan.top/img/20260816135749.png)

## Joint-control node signal chain

A joint performs well only when the complete control and power signal chain is designed together:

- **PWM and current control:** deterministic FOC execution with PWM-synchronized ADC sampling, using one-, two-, or three-shunt current sensing.
- **Position feedback and calibration:** a motor-side encoder for commutation, an optional output-side encoder for joint-angle accuracy and diagnostics, and nonvolatile memory for encoder offsets, motor parameters, and calibration.
- **Power stage:** integrated, semi-discrete, or discrete inverter selected by bus voltage, current, switching frequency, cooling, and mechanical volume.
- **Communication:** CAN FD for local actuator nodes; EtherCAT or Ethernet TSN where tighter synchronization and higher bandwidth are required; RS-485 for robust field links.
- **Protection, safety, and security:** independent current, voltage, and temperature supervision, hardware shutdown paths, safe torque off (STO) options, secure boot, and authenticated updates.

![Example of an actuator system architecture](https://pic.dwgan.top/img/20260816135738.png)

## ST reference architectures

Our joint-motor hardware and software solution is documented in the ST application note [AN6569 - Motor control architectures for humanoid robot joints](https://www.st.com/resource/en/application_note/an6569-motor-control-architectures-for-humanoid-robot-joints-stmicroelectronics.pdf). I participated in designing the joint motor hardware and software solution described in that note.

The note describes two complementary reference architectures that share common STM32 control, sensing, communication, and software concepts:

### Compact 48 V GaN joint-control

- Targets compact mid-power joints such as elbows, wrists, and selected neck or shoulder axes.
- Combines an STM32 motor-control MCU with an integrated GaN power stage to reduce the inverter footprint and support higher switching frequencies.
- Includes PWM-synchronized current and position acquisition, CAN FD local communication, and local hardware protection.
- Representative building blocks include STM32G4, GaNSPIN integrated GaN drivers, TSC2xx current sensing, and IMU or motion sensors.

![Example of a compact 48 V GaN joint-control node](https://pic.dwgan.top/img/20260816135729.png)

### High-current discrete joint-control

- Targets high-power joints such as hips, knees, ankles, and load-bearing shoulder axes.
- Separates the MCU, gate driver, and external GaN or silicon power devices for better current margin, heat spreading, and protection.
- Supports one-, two-, or three-shunt current measurement, motor-side and output-side position feedback, and independent hardware fault reaction.
- Representative building blocks include STM32G4, STDRIVE gate drivers, external 100 V-class GaN or MOSFET devices, and TSC240 current sensing.

## GaN technology selection

Silicon MOSFETs remain cost-effective for many 48–60 V drives. GaN is attractive where reduced switching loss, higher PWM frequency, smaller power-stage volume, or compact thermal design creates system value. The decision also depends on conduction loss, RDS(on), EMI, layout, thermal design, reliability, and cost.

### Why GaN can be better

#### Structural differences → loss differences

For silicon power MOSFETs, the P-body plus N-drift region creates a PN body diode. During commutation, stored minority-carrier charge must be removed, producing reverse-recovery loss.

For GaN e-mode HEMTs, conduction is dominated by the 2DEG channel and reverse conduction is largely channel-based, with practically zero reverse-recovery charge. This removes the hard-switching penalty associated with Qrr, which is especially valuable at high voltage and high frequency.

Typical reverse-recovery loss share of total loss:

- Low frequency (≤50 kHz) and low bus (≤24–48 V): about 5–20%.
- Mid/high frequency (100–500 kHz) or higher bus (≥48–100 V): about 20–40%.
- High-voltage, high-frequency hard switching: can reach 40–60%+.

Engineering estimate:
$$
P_{rr}\approx Q_{rr}\cdot V_{bus}\cdot f_{sw}\cdot N
$$

#### Why commutation loss is large in silicon

1. During deadtime or freewheeling, the silicon body diode becomes forward-biased and stores minority carriers.
2. At commutation, the opposite switch turns on and forces the diode into reverse bias, producing a reverse-recovery current spike.
3. The spike overlaps with high voltage, so the energy loss is approximately:

$$
E_{rr}=\int v(t)\,i_{rr}(t)\,dt \approx Q_{rr}\cdot V_{bus}
$$

#### Smaller Miller capacitance → lower gate-drive loss

Gate-drive energy per switching event is:
$$
E_{gate}\approx Q_g\cdot V_{drive}
$$
and average gate-drive power is:
$$
P_{gate}=Q_g\cdot V_{drive}\cdot f_{sw}
$$
A smaller Miller charge reduces total gate charge and therefore lowers gate-drive loss at a given frequency, or allows a higher switching frequency for the same drive-loss budget.

In a 48 V laboratory configuration, the GANSPIN1105 package temperature rises with phase current and PWM frequency; one operating point (200 W, 4.2 A RMS, 20 kHz) reached 63.3 °C without a heatsink.

![GANSPIN1105 temperature as a function of current and frequency](https://pic.dwgan.top/img/20260816134033.png)

## STM32 5-series positioning

The STM32 5-series (G5, C5, H5) is the product line ST is promoting this year, with some parts not yet publicly released. The 5-series is built on a 40 nm process, while earlier families are based on 90 nm. ST is shifting manufacturing capacity toward 40 nm and is also cooperating with domestic fabs, which helps secure supply for these devices.

## Software enablement

We provide MCSDK (X-CUBE-MCSDK), an ST motor-control software expansion package. It supports FOC in sensored and sensorless configurations, STM32CubeMX-based project generation, live parameter tuning, and standard protection and fault handling. Compared with Simulink, MCSDK is hardware-oriented and ready for deployment, whereas Simulink is more model-oriented and simulation-focused.

## Challenges

- **Hardware design:** GaN requires careful PCB layout and gate-driver design to minimize parasitic inductance, has a narrow gate-voltage margin, shorter short-circuit robustness than silicon, and higher dV/dt and EMI sensitivity.
- **Application cost:** GaN devices and dedicated drivers are more expensive, making them less suitable for low-cost platforms such as consumer or robotic-dog products.

# Robotic Hands

[Bilibili link](https://www.bilibili.com/video/BV1cfh1zbEnB)

# Overall humanoid robot

[Humanoid robot with gesture recognition and voice control](https://www.bilibili.com/video/BV1gX28BpEWm)

[Humanoid robot follow gesture using STM32N6](https://www.bilibili.com/video/BV1MGCuBEEzo)

# Past work

In the past, we have developed many solutions for smart homes, especially for **KNX**. Which is a leading international protocol in smart home automation that ensures seamless compatibility and plug-and-play functionality across devices. We’ve developed various demos to showcase the integration of ST’s products. During the development process of those demo solutions, I got more familiar with ST products, such as STM32 MCU/MPU, chips, KNX protocol chips, IO-Link protocol chips, edge AI sensors, DC-DC converters, and energy harvesting chips. Our demos include smart charging stations, mini IoT houses, photovoltaic energy storage and harvesting, IoT sensors, KNX-based smart building management systems, and solutions for remote network monitoring and intranet penetration.
