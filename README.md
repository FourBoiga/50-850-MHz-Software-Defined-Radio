
# Wideband SDR (50–800 MHz)

## Overview

This project is a custom software-defined radio (SDR) designed to receive signals from **50 MHz to 800 MHz**. The goal is to build a modular, low-cost SDR while learning RF engineering, analog design, FPGA development, and digital signal processing from the ground up.

Unlike many hobby SDRs that rely on integrated tuner chips, this design emphasizes a discrete RF front-end consisting of filter banks, RF switching, amplification, and an I/Q receiver architecture.

> **Project Status:** Early Design Phase

---

## Goals

* Receive signals from **50 MHz to 800 MHz**
* Modular RF front-end
* Good dynamic range
* Low noise figure
* I/Q baseband output
* USB interface to a host PC
* Learn professional RF design techniques

---

## Planned Architecture

```text
Antenna
    │
    ▼
RF Protection
    │
    ▼
Bandpass Filter Bank
    │
    ▼
RF Switch
    │
    ▼
Low Noise Amplifier (LNA)
    │
    ▼
Quadrature Mixer
          ▲
          │
      Local Oscillator
          │
      PLL Synthesizer
          ▲
          │
      Reference TCXO
    │
    ▼
I/Q Baseband Filters
    │
    ▼
ADC
    │
    ▼
FPGA
    │
    ▼
USB
    │
    ▼
PC
```

---

## Planned Specifications

| Parameter       |                          Target |
| --------------- | ------------------------------: |
| Frequency Range |                      50–800 MHz |
| Architecture    | Superheterodyne / Zero-IF (I/Q) |
| Output          |                    I/Q Baseband |
| Interface       |                             USB |
| FPGA            |            Sipeed Tang Nano 20K |
| MCU             |   STM32 (configuration/control) |

---

## Current Design Decisions

### RF Front End

* Switched bandpass filter bank
* RF switch for band selection
* Low Noise Amplifier after filtering
* Quadrature mixer for I/Q generation

### Local Oscillator

Planned architecture:

* Stable TCXO reference
* PLL frequency synthesizer
* Buffered LO output
* Software controlled frequency tuning

---

## Major Design Challenges

* Wideband filter design
* RF PCB layout
* Mixer performance
* Local oscillator phase noise
* Dynamic range
* Image rejection
* ADC selection
* FPGA DSP implementation

---

## Software

Planned software components include:

* FPGA signal processing
* USB communication
* PC application
* FFT visualization
* Spectrum analyzer
* Waterfall display
* Demodulation experiments

---

## Current Progress

* [x] Initial system architecture
* [x] Frequency range defined
* [x] Filter bank concept
* [ ] Local oscillator implementation
* [ ] Mixer selection finalized
* [ ] RF PCB layout
* [ ] FPGA firmware
* [ ] USB interface
* [ ] DSP implementation
* [ ] PC software

---

## Long-Term Goals

This project is intended to serve as a platform for learning and experimentation in:

* RF Engineering
* Microwave PCB Design
* Analog Electronics
* FPGA Development
* Digital Signal Processing
* SDR Software
* Communication Systems

Future versions may expand beyond 800 MHz and incorporate additional features as experience is gained.

---

## Disclaimer

This is an educational project. Designs, specifications, and architecture may change significantly as testing and development progress.
