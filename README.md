# Wideband SDR (50–850 MHz)

## Overview

This project is a custom software-defined radio (SDR) designed to receive signals from approximately **50 MHz to 850 MHz**. The goal is to build a modular, low-cost SDR while learning RF engineering, analog design, FPGA development, and digital signal processing from the ground up.

Unlike many hobby SDRs that rely heavily on highly integrated tuner/transceiver ICs, this design uses discrete RF building blocks for much of the signal chain, including filtering, switching, amplification, mixing, local oscillator generation, and quadrature I/Q conversion.

> **Kicanvas Schematic:** https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2FFourBoiga%2F50-850-MHz-Software-Defined-Radio%2Fblob%2Fmain%2FSDR.kicad_sch

> **Kicanvas PCB:** https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2FFourBoiga%2F50-850-MHz-Software-Defined-Radio%2Fblob%2Fmain%2FSDR.kicad_pcb

---

## Goals

* Receive signals from approximately 50 MHz to 850 MHz
* Modular RF front-end
* Good dynamic range
* Low noise figure
* Quadrature I/Q output
* FPGA-based digital signal processing
* USB interface to a host PC
* Eventually interface with SDR software such as SDR++
* Learn professional RF and DSP design techniques

---

## Current Architecture

The signal starts at the antenna and passes through the RF protection and switched bandpass filter bank before being amplified by the LNA. From there it goes through the quadrature receiver and intermediate-frequency conversion stages, producing differential I/Q signals for the ADC. The ADC sends the digitized samples to the FPGA, where the DSP will be performed before the data is eventually sent to the host PC over USB.

---

The receiver uses multiple local oscillator paths generated from PLL frequency synthesizers referenced to a stable TCXO.

---

## Specifications

| Parameter           |                    Specification |
| ------------------- | -------------------------------: |
| Frequency Range     |                      ~50–850 MHz |
| Architecture        | Superheterodyne / Quadrature I/Q |
| Target IF Bandwidth |                           ~1 MHz |
| ADC                 |                       LTC2323-12 |
| ADC Resolution      |                           12-bit |
| ADC Sample Rate     |                   5 MSPS/channel |
| ADC Interface       |                              SPI |
| FPGA                |             Sipeed Tang Nano 20K |
| Controller          |                     STM32 Nucleo |
| Host Interface      |                              USB |
| Output              |              Digital I/Q samples |

These are design targets and specifications; final RF performance will be determined through hardware testing and characterization.

---

## RF Front End

The RF front end consists of:

* RF input protection
* Switched bandpass filter bank
* RF switching
* Low-noise amplifier
* Quadrature receiver/mixer
* Intermediate-frequency conversion
* Local oscillator generation
* PLL frequency synthesizers
* TCXO reference

### Filter Architecture

The original design included additional filtering after the LNA. After reviewing the local RF environment and considering the additional simulation and design time required, the post-LNA filters were removed from the architecture.

The remaining pre-LNA filter bank provides the primary out-of-band filtering before amplification.

---

## Local Oscillator

The local oscillator system uses:

* Stable TCXO reference
* PLL frequency synthesizers
* LO generation for the receiver mixer
* LO generation for the IF mixer

The LO circuitry and synthesizers have been fully integrated into the PCB design.

---

## I/Q Receiver

The receiver produces differential I/Q signals which are digitized by the LTC2323-12 ADC.

During PCB design, the polarity of the Q differential pair was found to be opposite to the polarity expected by the ADC. Rather than rerouting the completed RF section, the differential inputs are intentionally swapped.

This produces a 180° phase inversion of the Q component, which will be corrected digitally in the FPGA by negating the Q samples.

---

## ADC

The SDR uses an **LTC2323-12** dual-channel 12-bit SAR ADC.

The planned configuration uses:

* 12-bit resolution
* 5 MSPS per channel
* Differential I/Q inputs
* SPI interface
* Up to 105 MHz SPI clock

The ADC provides the interface between the analog I/Q receiver and the FPGA's digital signal-processing chain.

---

## FPGA and DSP

The next major development phase is the FPGA firmware and digital signal processing.

The FPGA will initially implement a minimal processing chain capable of receiving and processing the raw I/Q samples from the ADC.

Planned processing includes:

```text
ADC I/Q Samples
       │
       ▼
Digital Filtering
       │
       ▼
Frequency Translation
       │
       ▼
Decimation
       │
       ▼
Sample Formatting
       │
       ▼
USB / Host PC
```

The FPGA development will be done using the **Sipeed Tang Nano 20K**.

The initial goal is not to immediately implement complete SDR++ compatibility. The first fallback target is simply to get raw or minimally processed I/Q samples from the FPGA to a computer.

A simple Python-based host program can then perform FFT processing and display the received spectrum. This provides a straightforward way to verify the complete RF → ADC → FPGA → PC signal path before implementing a more complete SDR software interface.

---

## PCB

The PCB design is now complete and is undergoing a final review before manufacturing.

Completed PCB work includes:

* RF protection
* Bandpass filter bank
* RF switching
* LNA
* Receiver/mixer circuitry
* Local oscillator circuitry
* PLL synthesizers
* I/Q differential routing
* ADC interface
* FPGA interface
* STM32 controller interface
* USB-C circuitry
* Debug SMA connector
* 50 Ω test line
* Test points
* Debug interfaces

The next steps are to perform the final PCB review, finalize the BOM, and submit the project for manufacturing/funding.

---

## Development Progress

* [x] Initial system architecture
* [x] Frequency range defined
* [x] Filter bank designed
* [x] RF switching designed
* [x] LNA integrated
* [x] Receiver/mixer circuitry integrated
* [x] Local oscillator designed
* [x] PLL synthesizers integrated
* [x] I/Q differential routing completed
* [x] ADC integrated
* [x] Debug SMA/test infrastructure added
* [x] PCB layout completed
* [x] Final PCB review
* [x] Finalize BOM
* [ ] Submit for manufacturing/funding
* [ ] Manufacture PCB
* [ ] Hardware bring-up
* [ ] FPGA ADC interface
* [ ] Initial DSP implementation
* [ ] I/Q streaming to PC
* [ ] Host-side FFT/spectrum visualization
* [ ] SDR++ integration
* [ ] RF characterization

---

## Software

### FPGA

Planned FPGA functionality includes:

* LTC2323-12 SPI interface
* I/Q sample capture
* Q polarity correction
* Digital filtering
* Digital frequency translation
* Decimation
* I/Q data formatting
* USB data streaming

### Host PC

The initial host software will likely be a simple Python-based development tool capable of:

* Receiving I/Q samples
* Converting ADC samples into complex I/Q data
* Performing FFTs
* Displaying frequency spectra
* Visualizing received signals

The long-term goal is compatibility with existing SDR software such as SDR++.

---

## Major Engineering Challenges

* Wideband RF filter design
* RF PCB layout
* Maintaining controlled RF impedance
* Mixer integration
* Local oscillator synthesis
* LO phase noise
* Dynamic range
* Noise figure
* I/Q signal integrity
* ADC interfacing
* High-speed SPI
* FPGA DSP implementation
* Fixed-point digital signal processing
* Host-side I/Q streaming
* SDR software integration

One of the primary goals of the project is to understand and implement these individual systems rather than relying on a highly integrated SDR transceiver to hide their operation.

---

## Long-Term Goals

This project is intended to serve as a platform for experimentation and learning in:

* RF Engineering
* Analog Electronics
* RF PCB Design
* FPGA Development
* Digital Signal Processing
* Software-Defined Radio
* Communication Systems

Future revisions may improve filtering, dynamic range, instantaneous bandwidth, frequency coverage, DSP capabilities, and host software.

---

## Disclaimer

This is an educational engineering project. Specifications and architecture may change as the hardware is manufactured, tested, and characterized. Unless explicitly stated otherwise, listed specifications are design targets rather than measured performance.
