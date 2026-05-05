# PIC18F46K22 Interrupt-Driven Digital Multimeter

**Domain:** Embedded Systems · PIC18F Microcontroller  
**Institution:** La Cité collégiale  
**Date:** January – April 2026  
**Author:** Adam Zaghloul  
**Toolchain:** MPLAB X IDE · MCC (MPLAB Code Configurator) · XC8 v3.10 · PIC18F-K_DFP 1.14.301

---

## Overview

A fully interrupt-driven digital multimeter implemented on a PIC18F46K22 microcontroller. The DMM measures DC voltage (0–10 V) and resistance (0–99.9 kΩ), displays three-digit results on a multiplexed 7-segment display, and supports a 3-second hold mode. All button inputs are handled exclusively through external interrupt service routines — no polling anywhere in the design.

The firmware was developed with MCC-generated peripheral drivers and a custom `MultiM.h` library for modular architecture.

---

## Features

- **Voltmeter mode** — DC voltage measurement, 0–10 V range
- **Ohmmeter mode** — Resistance measurement, 0–99.9 kΩ range  
- **Hold mode** — Freezes display for 3 seconds on button press
- **Sleep/wake logic** — Reduces power consumption when idle
- **3-digit multiplexed 7-segment display** — Timer interrupt drives digit scanning
- **3 mode buttons** — Each handled exclusively in ISR (no polling)
- **ADC stability measures** — Averaging and settling time for accurate readings

---

## Hardware

| Component | Part | Function |
|-----------|------|----------|
| Microcontroller | PIC18F46K22 | Main processor |
| Display | 3-digit 7-segment (common cathode) | Numeric output |
| Programmer | PICkit (snap module) | ICSP programming |
| Mode Switch 1 | Push-button (INT0) | Voltmeter mode |
| Mode Switch 2 | Push-button (INT1) | Ohmmeter mode |
| Mode Switch 3 | Push-button (INT2) | Hold mode |
| Resistor network | Various | Current limiting for segments |
| NPN transistors | 2N3904 × 3 | Digit enable switching |

---

## Firmware Architecture

```
main.c
├── MCC_Initialize()         — Configures all peripherals via MCC
├── ADC_GetConversion()      — 10-bit ADC reading
├── TMR0_ISR()               — 7-segment display multiplexing (Timer0)
├── INT0_ISR()               — Voltmeter mode button handler
├── INT1_ISR()               — Ohmmeter mode button handler
├── INT2_ISR()               — Hold mode button handler
└── MultiM.h / MultiM.c      — Custom library: conversion, display logic, hold timer

MCC Generated Files:
├── adc.c / adc.h
├── ext_int.c / ext_int.h
├── tmr0.c / tmr0.h
├── interrupt_manager.c / interrupt_manager.h
├── pin_manager.c / pin_manager.h
└── device_config.c / device_config.h
```

**Key design decisions:**
- All button events handled in ISRs — zero polling in `main()` loop
- Hold mode implemented with a timer-based 3-second countdown in ISR context
- ADC readings averaged over multiple samples for display stability
- Sleep entered automatically after inactivity; any button wakes the MCU

---

## Photos

| View | Description |
|------|-------------|
| ![MPLAB project](images/mplab-project-tree.png) | MPLAB X IDE project structure |
| ![Hardware build](images/multimeter-breadboard.jpg) | Breadboard — PIC18F46K22, 7-segment display, push-buttons, PICkit |
| ![Compiler](images/compiler-version.png) | XC8 v3.10 compiler configuration |

---

## Project Structure

```
EXAM.X/
├── Header Files/
│   └── MCC Generated Files/
│       ├── adc.h
│       ├── device_config.h
│       ├── ext_int.h
│       ├── interrupt_manager.h
│       ├── mcc.h
│       ├── MultiM.h          ← Custom library
│       ├── pin_manager.h
│       └── tmr0.h
└── Source Files/
    ├── main.c
    └── MCC Generated Files/
        ├── adc.c
        ├── device_config.c
        ├── ext_int.c
        ├── interrupt_manager.c
        ├── mcc.c
        ├── MultiM.c          ← Custom library
        ├── pin_manager.c
        └── tmr0.c
```

---

## How to Build

**Requirements:**
- MPLAB X IDE (tested with project at `C:\Users\...\EXAM.X`)
- XC8 Compiler v3.10
- PIC18F-K_DFP pack v1.14.301
- PICkit 3/4 or SNAP programmer

**Steps:**
1. Clone this repository
2. Open `EXAM.X` in MPLAB X IDE
3. Set compiler to XC8 v3.10
4. Connect PICkit to PIC18F46K22 ICSP header
5. Build and program

---

## Key Skills Demonstrated

- PIC18F peripheral configuration via MCC (ADC, Timer0, External Interrupts)
- Fully interrupt-driven embedded firmware architecture
- Multiplexed 7-segment display driving via Timer ISR
- Custom modular library development (`MultiM.h`)
- ADC signal conditioning and averaging
- Sleep/wake power management on PIC18F
- MPLAB X IDE project structure and XC8 compiler toolchain
