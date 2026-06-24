# Line Following Robot — Analog Logic Circuit

A hardware robotics project that implements autonomous line-following behavior using **pure analog logic circuits** — no microcontroller, no Arduino, no programming. The robot detects a black line on a white surface using infrared sensors and drives its motors accordingly using comparator-based switching logic built entirely on a breadboard.

Built as a course project for **CSE260 – Digital Logic Design**.

---

## Project Overview

![Line Following Robot](images/line-follower-robot.png)

The robot follows a black line drawn on a white surface using two IR sensor modules. Each sensor feeds a binary signal (0 or 1) into a comparator (LM393), which drives the corresponding motor through an L293D motor driver IC. The entire decision-making logic — *"is there a line? which motor runs?"* — is implemented as a hardware combinational circuit, not software.

This is the physical embodiment of the combinational logic circuits studied in CSE260.

---

## How It Works

### Core Logic

| Left Sensor | Right Sensor | Left Motor | Right Motor | Behavior |
|:-----------:|:------------:|:----------:|:-----------:|----------|
| 0 (on line) | 0 (on line)  | ON         | ON          | Move forward |
| 0 (on line) | 1 (off line) | OFF        | ON          | Turn right (steer back) |
| 1 (off line)| 0 (on line)  | ON         | OFF         | Turn left (steer back) |
| 1 (off line)| 1 (off line) | OFF        | OFF         | Stop |

This is a 2-input, 2-output combinational logic circuit. The truth table above is the complete behavioral specification of the robot's "brain."

### Signal Flow

```
IR Sensor (Left)  ──→  LM393 Comparator  ──→  L293D Input A  ──→  Left Motor
IR Sensor (Right) ──→  LM393 Comparator  ──→  L293D Input B  ──→  Right Motor
                              ↑
                    Threshold set by
                    resistor voltage divider
```

### Why No Microcontroller?

Standard line-follower robots use an Arduino to read sensor values and write motor commands in code. This project implements the **exact same logic in hardware** — demonstrating that simple combinational logic can replace a microprocessor for fixed-function decision making. This is the core lesson of Digital Logic Design: logic gates perform computation without a CPU.

---

## Components

### Motion

| Component | Quantity | Purpose |
|-----------|----------|---------|
| Robot Chassis Plate (Acrylic/Plastic) | 1 | Main structural body |
| BO DC Gear Motors (150–300 RPM) | 2 | Drives left and right wheels independently |
| Plastic Wheels | 2 | Provides traction for movement |
| Caster Wheel | 1 | Front omnidirectional wheel for balance and turning |

### Sensing

| Component | Quantity | Purpose |
|-----------|----------|---------|
| IR Sensor Modules with LM393 Comparator | 2 | Detects contrast between black and white surfaces |
| LM393 Comparator IC | 1 (built into sensor) | Converts analog IR reading to digital HIGH/LOW signal |

### Motor Driving

| Component | Quantity | Purpose |
|-----------|----------|---------|
| L293D Motor Driver IC | 1 | H-bridge IC — acts as high-current switch to power motors from low-current logic signals |
| 1N4007 Flyback Diodes | 4 | Protects the circuit from voltage spikes caused by motor inductance |

### Circuit Assembly

| Component | Quantity | Purpose |
|-----------|----------|---------|
| Half-Size or Full-Size Breadboard | 1 | Hosts all circuit connections without soldering |
| Jumper Wires (M-M and F-M) | 1 pack | Routes power and signals across the board |

### Power Supply

| Component | Quantity | Purpose |
|-----------|----------|---------|
| 18650 Lithium-Ion Batteries (or 4× AA) | 2 (or 4) | Provides 6V–7.4V to power motors and logic |
| Battery Holder with bare wire leads | 1 | Secures batteries to the chassis |
| SPST Toggle/Slide Switch | 1 | Master ON/OFF power switch |

---

## Circuit Logic Explanation

### IR Sensor Operation
Each IR sensor module emits infrared light downward. White surfaces reflect IR strongly (sensor outputs HIGH = 1); black surfaces absorb IR and reflect very little (sensor outputs LOW = 0). The LM393 comparator inside the module converts the analog photodiode reading into a clean digital signal using a preset threshold resistor.

### LM393 Comparator
The comparator compares the IR photodiode voltage against a reference voltage set by an on-board potentiometer. If the reflected signal is below the threshold (black line detected), it outputs LOW (0). This is the hardware equivalent of an `if` statement.

### L293D Motor Driver (H-Bridge)
DC motors require more current than logic circuits can supply directly. The L293D is a dual H-bridge IC that takes low-current logic signals (from the comparators) and switches high-current motor power accordingly. It can also reverse motor direction, though this project uses it in single-direction mode.

### Flyback Diode Protection
DC motors are inductive loads — when power is cut, they generate a reverse voltage spike (back-EMF) that can damage the driver IC. The 1N4007 diodes are placed across each motor terminal to safely clamp and absorb this spike.

---

## Boolean Expressions

Let L = Left sensor output, R = Right sensor output (1 = off line, 0 = on line):

```
Left Motor  = NOT L  (runs when left sensor is ON the line)
Right Motor = NOT R  (runs when right sensor is ON the line)
```

These two single-gate NOT operations, implemented physically by the LM393 comparator's active-low output, are the complete "program" of this robot.

---

## CSE260 Concepts Demonstrated

| Course Topic | Physical Implementation |
|---|---|
| Combinational Logic | The sensor-to-motor mapping is a pure combinational truth table |
| Logic Gate (NOT/Inverter) | LM393 comparator active-low output acts as an inverter |
| H-Bridge Circuit | L293D motor driver implements bidirectional switching |
| Boolean Minimization | The two-variable truth table reduces to two independent NOT gates |
| Digital Signal Levels | IR sensors output clean digital HIGH/LOW, not analog values |
| Fan-out & Current Drive | L293D demonstrates the need for buffering when driving high-current loads from low-current logic |

---

## Technical Architecture

- **Implementation:** Pure analog/digital hardware — no microcontroller, no firmware, no programming language
- **Logic Type:** Combinational (stateless) — output depends only on current input, no memory or clock
- **Power:** 6V–7.4V from lithium-ion or AA batteries
- **Interface:** Physical — the robot responds to real-world IR contrast in real time

## Core Engineering Practices Demonstrated

- **Hardware Logic Implementation:** The robot's entire decision-making behavior is encoded as a combinational circuit, demonstrating that digital logic gates can perform computation without a processor
- **Signal Conditioning:** Raw analog photodiode output is converted to clean digital HIGH/LOW by the LM393 comparator, showing the role of thresholding in real digital systems
- **Motor Driver Architecture:** The L293D H-bridge IC shows how logic-level signals (5V, ~10mA) can safely switch high-current motor loads (6–7V, ~500mA) — a fundamental interface challenge in embedded hardware
- **Protection Circuit Design:** Flyback diodes across motor terminals demonstrate practical knowledge of inductive load protection, preventing back-EMF damage to the driver IC
- **No-Code Autonomy:** The robot achieves closed-loop feedback behavior (detect → correct → detect) entirely through hardware, with zero software — illustrating the boundary between digital logic and programmable systems

## Author

**Md. Tanvirul Islam Rifat**

* **GitHub:** [@tanvirul-islam-rifat](https://github.com/tanvirul-islam-rifat)
* **LinkedIn:** [Tanvirul Islam Rifat](https://www.linkedin.com/in/tanvirul-islam-rifat)
