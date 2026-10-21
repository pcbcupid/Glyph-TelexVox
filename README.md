# Glyph teleVox

This repository walks through building a wireless hardware product from scratch, using teleVox as the example. teleVox is a Glyph-based walkie-talkie carrier board that brings a controller, 2.4 GHz radio, microphone, audio output, OLED, microSD storage, battery holder, and controls together on one PCB.

## Build journey

Follow the stages from the first idea to a working device. Each linked stage opens the matching Instagram video.

1. **Purpose** — decide what the device should do and why it needs to exist.
2. **Brainstorming → Feature selection** — turn the idea into a focused set of features.
3. **[Features → Component selection](https://www.instagram.com/p/DaWQPvEzTmU/)** — Step 1: finalize the components.
4. **[Components → Schematic design](https://www.instagram.com/p/DaY9_ELTk44/)** — Step 2: draw the schematic.
5. **[Schematic design → Footprints](https://www.instagram.com/p/DafersWzC4M/)** — Step 3: choose the physical footprints.
6. **[Schematic validation](https://www.instagram.com/p/DayvqGXznD2/)** — Episode 4: run the electrical rules check (ERC).
7. **[Footprints → PCB placement](https://www.instagram.com/p/Da1kG9mzoH7/)** — Episode 5: place the parts before routing.
8. **[PCB design → Fabrication](https://www.instagram.com/p/Da24SWXz-QB/)** — Episode 6: order the PCB.
9. **PCB → Mechanical case design** — fit the electronics into an enclosure.
10. **Finished hardware + code** — assemble the device and make it work.
11. **Final testing** — test the complete product.

## How the teleVox hardware works

The sections below follow the actual schematic's components and named nets. The tables use the references and pin names shown in KiCad so each connection is easy to trace.

## Design at a glance

| Block | References | What the schematic connects |
| --- | --- | --- |
| Controller carrier | U1 | Glyph module footprint; hosts the radio, audio, display, storage, and control signals |
| Battery and power switch | BT1, U6, C1 | Single 18650 holder; slide switch feeds the BAT net; polarized capacitor across the 3.3 V rail |
| Radio | U2 | nRF24L01 breakout on SPI, with separate CE and CSN controls |
| Microphone | U4 | Two-channel microphone module with SCK, WS, and SD signals |
| Audio output | U3, J2, J3 | GMOD-NAU8325 audio module, with clock/data and I²C connections; two output pairs go to connectors |
| Display | U7 | 0.91-inch, 128 × 32 I²C OLED |
| Storage | U5 | MicroSD module sharing the SPI bus with the radio |
| User controls | SW1, J1, D1, R1 | Push-to-talk button, five-pin encoder header, and an LED with series resistor |

At a system level, the microphone supplies audio samples, the radio carries packets between devices, and the audio module drives playback. The OLED, microSD card, encoder, LED, and push-to-talk button provide display, storage, and control connections. Firmware implements the behavior of those connections.

## Power path

The positive terminal of BT1 connects to the common contact of slide switch U6. One switch throw connects to the BAT net; the other throw has no schematic connection. In the connected position, BAT reaches U1 pin 12 and U3 pin 1. The battery negative terminal connects to GND. This arrangement uses an ON-ON switch as a power switch by leaving one position open.

U3 pin 1 is named **5V** on its symbol, but the schematic connects it to **BAT**, not to a regulated 5 V source. A single 18650 cell does not provide a fixed 5 V output. Check the actual audio module's allowed supply range and the assembled power path before applying power.

The +3V3 net connects U1 pins 14 and 15 to the radio (U2), microphone (U4), microSD module (U5), OLED (U7), and encoder header (J1). C1 sits between +3V3 and GND. The carrier schematic does not show a 3.3 V regulator or battery-charging circuit; those functions, if present, must come from a module or another part of the assembled system. The schematic also gives C1 the generic value **C_Polarized**, without a capacitance or voltage rating.

All of these blocks share GND. In particular, the microphone, radio, storage module, display, U1, battery negative terminal, and control switch return to that common net.

## Radio and storage: shared SPI bus

U2 is an nRF24L01 breakout. U5 is a microSD module. They share the controller's three SPI data/clock signals, while each has its own chip-select signal:

| Signal | U1 connection | Other connections | Purpose |
| --- | --- | --- | --- |
| SCK | Pin 23, SCK | U2 pin 5, U5 pin 3 | Shared SPI clock |
| MO | Pin 24, MO | U2 pin 6 MOSI, U5 pin 4 MO | Data from controller |
| MI | Pin 25, MI | U2 pin 7 MISO, U5 pin 5 MI | Data to controller |
| CE | Pin 2, named SDA on U1 symbol | U2 pin 3 | Radio enable control |
| CSN | Pin 1, named SCL on U1 symbol | U2 pin 4 | Radio chip select |
| MicroSD CS | Pin 26, named RX on U1 symbol | U5 pin 6 | Card chip select |

The names **SDA** and **SCL** on U1 pins 2 and 1 are names printed on the controller symbol. In this design those pins drive the radio's CE and CSN nets. The actual I²C bus is on U1 pins 21 and 22, described below. This distinction matters when assigning pins in firmware.

U2's IRQ pin is unconnected, so firmware would need to poll the radio or use another method that does not require its interrupt output. U5's card-detect pin and D1/D2 pins are also unconnected; the card is wired for the three shared SPI signals plus chip select, power, and ground. Both modules are on +3V3.

## Microphone input

U4 is identified in the design as a Gsense two-channel microphone module. Its clocked digital interface is wired as follows:

| U4 pin | Net | U1 pin |
| --- | --- | --- |
| SCK, pin 4 | MSCK | A2, pin 4 |
| WS, pin 2 | WS | A1, pin 3 |
| SD, pin 3 | MIC_SD | TX, pin 27 |
| 3.3V, pin 5 | +3V3 | Shared supply |
| GND, pin 1 | GND | Shared ground |

The SCK/WS/SD naming indicates a digital audio interface. The schematic defines the electrical connections but does not state sample rate, channel format, or which firmware peripheral should handle them. Although the module is named “two-channel,” it has one data line in this design.

## Audio output

U3 is a GMOD-NAU8325 module. The schematic's own annotation calls this block **Amplifier**. Its digital audio and control connections are:

| U3 pin | Net | U1 pin |
| --- | --- | --- |
| MCLK, pin 6 | NAU_MCLK | D13, pin 17 |
| BCK, pin 7 | BCLK | D14, pin 18 |
| DAT, pin 8 | NAU_DAT | D15, pin 19 |
| LRCK, pin 9 | LRCK | D16, pin 20 |
| SDA, pin 4 | SDA | D17, pin 21 |
| SCL, pin 5 | SCL | D18, pin 22 |
| 5V, pin 1 | BAT | Switched battery rail |
| GND, pin 3 | GND | Shared ground |

The SDA/SCL lines are shared with the OLED. U3 pin 2, named 3V3, is unconnected in the schematic. The design does not document the module's I²C address, audio format, amplifier configuration, or required startup sequence.

The output pairs are kept separate:

| Connector | Pins | Nets from U3 |
| --- | --- | --- |
| J3 | Pin 1 / pin 2 | LN / LP |
| J2 | Pin 1 / pin 2 | RN / RP |

These are labeled left negative/positive and right negative/positive outputs. Neither connector is wired to GND in the schematic. Treat each as a two-wire output pair and verify the audio module's output topology and speaker requirements before connecting a load.

## OLED and user controls

U7 is an SSD1306-style 0.91-inch 128 × 32 OLED module. Its SDA line connects to U1 D17 and U3 SDA; its SCL line connects to U1 D18 and U3 SCL. It also connects to +3V3 and GND. The carrier schematic does not show separate I²C pull-up resistors, so confirm whether the installed modules provide them.

SW1 is the push-to-talk button. It connects U1 A6 (PTT) to GND while pressed. Firmware would need an appropriate pull-up or another defined input bias; none is drawn on the carrier board.

J1 is a five-pin header marked for the rotary encoder area of the schematic. Pins 1, 2, and 3 connect to U1 A8 (ENC_A), A7 (ENC_B), and A9 (ENC_SW). Pin 4 is +3V3 and pin 5 is GND. The exact encoder module and its contact behavior are not specified here.

U1 A10 drives the LED net through R1 to D1, whose cathode is grounded. R1 has the generic value **R**, with no resistance specified. Select a resistor value appropriate for the installed LED and controller output before assembly.

## Controller pin summary

This is a firmware-oriented map of the U1 connections. The labels in the middle column are the schematic symbol's pin names; verify them against the specific Glyph board pinout when assigning GPIOs.

| Function | U1 pins in schematic | Connected device |
| --- | --- | --- |
| Radio CE / CSN | Pin 2 SDA / pin 1 SCL | U2 |
| Shared SPI SCK / MO / MI | Pins 23 / 24 / 25 | U2 and U5 |
| MicroSD CS | Pin 26 RX | U5 |
| Microphone SCK / WS / SD | Pin 4 A2 / pin 3 A1 / pin 27 TX | U4 |
| Audio MCLK / BCLK / DAT / LRCK | Pins 17 D13 / 18 D14 / 19 D15 / 20 D16 | U3 |
| I²C SDA / SCL | Pin 21 D17 / pin 22 D18 | U3 and U7 |
| Encoder A / B / switch | Pin 7 A8 / pin 6 A7 / pin 8 A9 | J1 |
| PTT / LED | Pin 5 A6 / pin 9 A10 | SW1 / R1 and D1 |
| Battery input | Pin 12 BAT | Switched BT1 positive |
| 3.3 V / GND | Pins 14 and 15 / pins 16 and 28 | Shared rails |

U1's symbol is named **GLYPHC6**, while the schematic annotation says **Glyph S3** and the assigned footprint is **glyph-s3**. Confirm the actual board's pinout against the symbol and PCB footprint before assembling or writing firmware. U1's two RST pins and USB pin are unconnected on this carrier.

## Project files and fabrication outputs

| Path | Contents |
| --- | --- |
| [walkietalkie.kicad_pro](walkietalkie.kicad_pro) | KiCad project settings |
| [walkietalkie.kicad_sch](walkietalkie.kicad_sch) | Editable schematic and embedded symbol copies |
| [walkietalkie.kicad_pcb](walkietalkie.kicad_pcb) | Editable PCB layout |
| [fp-lib-table](fp-lib-table), [sym-lib-table](sym-lib-table) | Project library references |
| [project-assets/](project-assets/) | Custom footprint, symbol, and 3D-model files |
| [the final gerber/](the%20final%20gerber/) | Front/back copper, mask, paste, silkscreen, board outline, plated-hole drill, job file, and walkietalkie.zip package |

Open the project file in KiCad 10 to inspect the design. Keep project-assets beside the KiCad files because the PCB refers to 3D models and footprints through project-relative paths. The Gerber zip contains the fabrication files; the loose files in the same directory match the archive exactly.

The symbol library table points to the **project-assets/symbols directory** rather than an individual KiCad symbol-library file, and the schematic also refers to a **singles** library that the table does not define. KiCad can read the symbols embedded in the schematic, but editing or updating them through the library browser requires fixing those references.
