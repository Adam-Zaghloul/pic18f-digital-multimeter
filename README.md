# PIC18F Digital Multimeter

> Interrupt-driven embedded multimeter · DC Voltage (0–10 V) · Resistance (0–99.9 kΩ) · 3-digit 7-segment display  
> **La Cité collégiale — Embedded Systems · Winter 2026**

---

## Photo

![PIC18F Multimeter](../images/multimeter.jpg)

---

## Overview

A fully **interrupt-driven digital multimeter** built on the **PIC18F46K22** microcontroller. All measurement modes, display multiplexing, and button handling are implemented exclusively through hardware interrupts — no polling anywhere in the firmware.

The project was developed using **MPLAB X IDE** with the **MCC (MPLAB Code Configurator)** peripheral setup tool and compiled with the **XC8** compiler. A custom `MultiM.h` library encapsulates all measurement logic.

---

## Specifications

| Parameter | Value |
|-----------|-------|
| Microcontroller | PIC18F46K22 |
| Voltage range | 0 V – 10 V DC |
| Resistance range | 0 Ω – 99.9 kΩ |
| Display | 3-digit multiplexed 7-segment |
| Operating modes | Voltmeter · Ohmmeter · Hold |
| Mode switching | External interrupts (3 push-buttons, ISR only) |
| Display refresh | Timer interrupt (TMR0) |
| Hold duration | 3 seconds |
| IDE | MPLAB X IDE |
| Compiler | XC8 v3.10 |
| Peripheral config | MCC (PIC18F-K DFP v1.14.301) |

---

## Firmware Architecture

```
EXAM.X/
├── Header Files/
│   └── MCC Generated Files/
│       ├── adc.h
│       ├── ext_int.h
│       ├── interrupt_manager.h
│       ├── mcc.h
│       ├── MultiM.h          ← Custom multimeter library
│       ├── pin_manager.h
│       └── tmr0.h
└── Source Files/
    ├── main.c
    └── MCC Generated Files/
        ├── adc.c
        ├── ext_int.c
        ├── interrupt_manager.c
        ├── mcc.c
        ├── MultiM.c          ← Custom multimeter logic
        ├── pin_manager.c
        └── tmr0.c
```

---

## How It Works

### ADC — Voltage Measurement
The PIC18F46K22's 10-bit ADC samples the input voltage. The raw ADC result is scaled to the 0–10 V range and formatted as a 3-digit BCD value for display.

### Resistance Measurement
A known reference resistor forms a voltage divider with the unknown resistance. The ADC measures the divider output; firmware calculates the unknown resistance using the divider equation.

### Interrupt-Driven Mode Switching
Three external push-buttons trigger **external interrupt ISRs** (INT0, INT1, INT2):
- **Button 1 (INT0)** → Voltmeter mode
- **Button 2 (INT1)** → Ohmmeter mode  
- **Button 3 (INT2)** → Hold mode (freezes displayed value for 3 seconds)

Software debouncing is implemented inside each ISR.

### Display Multiplexing (TMR0)
**TMR0** fires a periodic interrupt that cycles through the 3 display digits, driving the appropriate segment and digit select lines. This happens transparently in the background while measurement ISRs run.

### Hold Mode
On Hold activation, the last measured value is latched in a variable. A 3-second timer (software counter incremented by TMR0 ISR) automatically releases Hold and returns to the active measurement mode.

### Sleep / Wake Logic
The MCU enters sleep between display refresh cycles to reduce power consumption, waking on the next TMR0 or external interrupt event.

### ADC Stability
Multiple ADC samples are averaged per reading to reduce noise, and a software settling delay is applied after each channel switch.

---

## Key Peripherals Configured via MCC

| Peripheral | Function |
|-----------|----------|
| ADC | 10-bit, single channel, Fosc/64 clock |
| TMR0 | 8-bit timer, periodic overflow → display multiplex ISR |
| INT0 / INT1 / INT2 | External edge-triggered interrupts → mode switching ISRs |
| Pin Manager | Segment outputs, digit selects, button inputs |

---

## Skills Demonstrated

`PIC18F46K22` `MPLAB X IDE` `MCC` `XC8 compiler` `Embedded C` `ADC` `External interrupts (ISR)` `Timer interrupts` `7-segment multiplexing` `Software debouncing` `Modular firmware architecture` `Sleep/wake logic`

---

*Adam Zaghloul · La Cité collégiale · Winter 2026 · [adamzaghloul07@gmail.com](mailto:adamzaghloul07@gmail.com)*
