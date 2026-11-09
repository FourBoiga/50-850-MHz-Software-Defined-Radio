# Wideband Software Defined Radio — Development Logs

Original project: [Weather Satellite and FM Radio — Hack Club Stardance](https://stardance.hackclub.com/projects/43286)  
Author: [@Boiga](https://stardance.hackclub.com/@Boiga)  

> Archived from Stardance, in chronological order. These entries describe the design as it evolved; later decisions supersede earlier plans. Images currently use the original Stardance URLs and should be downloaded into the repository before the website goes offline.

## Devlog 1 — Wideband Software Defined Radio

**Time logged:** 17m 16s  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/33973)**

I started this project about three weeks before I found out about Hack Club Stardance, so I’m jumping in a little late with the documentation. Instead of trying to recreate everything I’ve already done, I’m going to use these devlogs to document the project from where it is now and everything going forward.

### Project Goal

The goal of this project is to build a custom Software Defined Radio (SDR) capable of receiving signals from 50 MHz to 800 MHz. Instead of relying on a single integrated tuner chip, I’m designing much of the radio myself so I can better understand how modern radio hardware actually works.

### Current Progress

So far I have finished most of the system architecture and decided on the overall signal chain, including:

- Bandpass filter bank
- Low-noise amplifier
- Quadrature receiver
- FPGA-based digital signal processing

Right now I’m working on one of the most important parts of the design: the local oscillator. This circuit generates the precise frequencies needed for the receiver to tune across the spectrum.

### Next Steps

- Finalize the local oscillator design
- Select the frequency synthesizer and reference oscillator
- Finish the receiver front-end
- Begin schematic capture
- Start PCB layout

---

## Devlog 2 — Finishing the Schematic

**Time logged:** 4h 33m 33s  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/35598)**

I finally reached a pretty big milestone: the entire schematic is finished. I still need to add footprints for all the parts and start PCB layout, but the entire radio is now connected together electrically.

### Getting Too Focused on RF

One funny thing that happened while designing this was that I got so focused on the RF side of the project that I basically forgot about something kinda important: power.

I was spending so much time looking at filters, amplifiers, mixers, and oscillators that I completely forgot that none of those parts work without proper power rails.

When I went back through everything, I also realized I had made another beginner mistake. Earlier in the project I was labeling rails like VDD and VDDA, but at this level that isn’t really enough anymore.

Different chips need different voltages, and some have separate analog and digital supplies, so I need to clearly label exactly what voltage each rail is and what it powers.

### Simplifying the Local Oscillator

The last part of the schematic that gave me trouble was the IF local oscillator for the internal mixer in the CMX992.

At first I thought I would have to design a bunch of extra filtering and signal conditioning around it, which was making that section way more complicated than I wanted for a V1.

After reading through the datasheet more, I realized I could just use another CMOS oscillator to generate the signal I needed.

### Changing the RF Design

I also made a few big changes to the RF front end.

Originally the plan was for the SDR to cover 50 MHz–450 MHz, but I expanded that to 50 MHz–850 MHz.

This lets the radio cover a much larger range of signals, including:

- FM radio
- Aircraft communications
- NOAA weather satellites

I also changed the filtering strategy:

- The pre-LNA filters are now 3rd-order Butterworth filters. Since these are before the LNA, keeping insertion loss low is extremely important because any loss here directly hurts sensitivity.
- The post-LNA filters are now 5th-order Elliptic filters. Since the signal has already been amplified, I can focus more on getting stronger rejection of unwanted signals.

Basically, the first filters are focused on keeping weak signals alive, while the second filters are focused on cleaning up the signal afterward.

### No USB Data

Another design decision I made was removing USB data from the USB-C connector.

The board is not meant to directly communicate with a computer. The SDR will communicate through an FPGA and STM32, which handle the digital processing and control.

Because of that, the USB-C port is only there for power. This simplifies the board and removes unnecessary routing.

### What’s Next?

- Add footprints to all components
- Verify every footprint against the datasheets
- Start PCB layout
- Begin designing the physical board

![Devlog 2 schematic screenshot](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6MjU3NjY5LCJwdXIiOiJibG9iX2lkIn19--583e483e8d09afb949dbb8973ecebfc135e95a03/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260806_183732.png)

---

## Devlog 3 — Footprints Complete

**Time logged:** 4h 2m  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/36252)**

I know I just posted a devlog yesterday but I hit a huge milestone today.

Today I did a huge session to add all the footprints to all **260** parts! I am now ready to actually start laying out the board and doing the actual sick RF stuff.

Also I added a couple things for future debugging such as LEDs, test points, jumpers, and 0-ohm resistors.

![Devlog 3 PCB footprints](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6MjYzMDM3LCJwdXIiOiJibG9iX2lkIn19--a951ae1eca553fd7766c75e6d848134cd00dc6b6/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260808_003559.png)

---

## Devlog 4 — Starting PCB Layout

**Time logged:** 5h 2m 1s  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/38299)**

I finally started laying out the PCB for the SDR. This has honestly been a lot more difficult than I expected, especially with the filters.

I couldn’t find many good examples of how to actually lay out lumped component Butterworth filters. There are tons of examples of how to design them and calculate the component values, but not many showing how you are supposed to physically place all the inductors and capacitors on the board. So I’m mostly figuring it out as I go.

I also added a 2-way RF switch after the first filter bank that goes to an SMA connector. This means I can connect a VNA directly to the filters and actually see how well I made them instead of having to guess what is wrong if the SDR doesn’t work.

The control pins for the switches were also annoying because they are right in the middle of the filter bank. I had to figure out how to route them without having their return currents interfere with the RF return currents from the filters. I ended up moving the filters around a bit, which also let me shorten some of the RF traces.

I also had to add another connector for the Nucleo board because I had to flip the orientation of the second RF switch. Because of that, the second switch needs to be controlled separately from the first one.

Right now I’m just adding the stitching vias around the first filter bank. After that I’ll move on to the LNA and the second filter bank.

![Devlog 4 RF filter layout](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6Mjc5Mzg3LCJwdXIiOiJibG9iX2lkIn19--9d1336c24d766624a6f9c851c026d711eadd457a/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260811_214417.png)

---

## Devlog 5 — Architecture Change

**Time logged:** 4h 19m  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/41407)**

Not much has been added since the previous devlog but I felt like it was necessary because a significant architecture change has been made.

### Filters

After getting feedback in the comments on my previous devlog and thinking about the project’s deadline, I decided it was better to remove the post-LNA filters from the design. I reviewed all the powerful transmitting stations near me and came to the conclusion that the removal of the post-LNA filters would not be detrimental.

However, the main reasoning behind removing the filters was that they added unnecessary simulation and design time that was wearing down on me. This also led to me having to take a 2-day break and work on my personal flight computer side project rather than forcing myself to push through and potentially burn out.

### Tuner Chip

I finished laying out and wiring the balun that turns the single-wire RF signal into a differential signal. I am currently trying to connect it to the CMX992 receiver chip without ruining its matching as well as blocking other important pins on the chip. I have found this to be very difficult.

Also, completely unrelated, the network kinda looks like a rose now, which is cool.

### Thanks

Also I just wanted to thank [@spheresva](https://stardance.hackclub.com/@spheresva) again for their input as it helped me a lot.

![Devlog 5 balun and receiver layout](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6MzA0MzgwLCJwdXIiOiJibG9iX2lkIn19--5708e0a2545d302291a9ee9d2ee94e81a73bd46e/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260817_223822.png)

---

## Devlog 6 — Routing and Final Circuits

**Time logged:** 5h 58m 1s  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/44211)**

Got a ton of work done in a single session today.

Finished the local oscillator (LO) for the intermediary mixer, synthesizer, LO for the IF mixer, routed I and Q differentials to the ADC, wired up the ADC, added the debug SMA port for the filters, and added the 50-ohm test line to the edge of the board.

### Stuff That Changed

I discovered that the + and − connections for the Q differential pair were opposite to what the ADC expects. After thoroughly reviewing the ADC datasheet, I decided that I can intentionally swap the differential inputs. This will invert the polarity of the Q component, which I plan to correct in the FPGA by negating the Q samples.

### What Is Left

All that is left is for me to create the USB-C circuit as well as set up the LDOs for the different power rails, add the remaining test points and the debug LED, and connect the pin headers for the STM Nucleo controller board.

### Thoughts

Debating on how I should connect the FPGA dev board. I know that the frequency on the SPI clock will be running at 105 MHz (rise time is 4 ns) as per the ADC’s datasheet. That is pretty high, I feel, for standard Arduino jumper wires, so I chose to solder wires; however, this still scares me a little.

I am unsure of soldering the FPGA’s header pins directly to the board either because of how difficult it will be to remove it afterwards. If anyone has any suggestions or ideas I would be really thankful. Thank you.

![Devlog 6 ADC and LO routing](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6MzI2NTA0LCJwdXIiOiJibG9iX2lkIn19--d46929093e7b57800787ff63ed4250ef0f62e123/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260822_231025.png)

---

## Devlog 7 — Done!!!

**Time logged:** 1h 40m  
**[Original post](https://stardance.hackclub.com/projects/43286/devlogs/45386)**

Lets gooo, I finished the board (I think, I am gonna do a second final pass), I just need to finalize the BOM and submit for funding.

![Devlog 7 completed PCB layout](https://stardance.hackclub.com/rails/active_storage/representations/proxy/eyJfcmFpbHMiOnsiZGF0YSI6MzM2MjQzLCJwdXIiOiJibG9iX2lkIn19--3a7f2c11e29f38bff7ab537248e951fe7af4f007/eyJfcmFpbHMiOnsiZGF0YSI6eyJmb3JtYXQiOiJ3ZWJwIiwicmVzaXplX3RvX2xpbWl0IjpbMTYwMCw5MDBdLCJzYXZlciI6eyJzdHJpcCI6dHJ1ZSwicXVhbGl0eSI6NzV9fSwicHVyIjoidmFyaWF0aW9uIn19--5394ecd620f1b8ee9be71be3f37cd22b8a88953c/Screenshot_20260824_214906.png)
