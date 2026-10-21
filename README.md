# Glyph teleVox

teleVox is a DIY, battery-powered walkie-talkie built around the Glyph ecosystem. Its carrier PCB brings the controller, radio, audio, display, storage, battery, and controls together in one handheld hardware project.

This repository follows the journey of building a wireless hardware product from an idea to a finished device.

## Build journey

1. **Purpose** — decide what the product should do.
2. **Brainstorming → Feature selection** — choose the features that matter.
3. **[Features → Component selection](https://www.instagram.com/p/DaWQPvEzTmU/)** — finalize the components.
4. **[Components → Schematic design](https://www.instagram.com/p/DaY9_ELTk44/)** — turn the component choices into a circuit.
5. **[Schematic design → Footprints](https://www.instagram.com/p/DafersWzC4M/)** — choose the physical parts for the PCB.
6. **[Schematic validation](https://www.instagram.com/p/DayvqGXznD2/)** — run the electrical rules check (ERC).
7. **[Footprints → PCB placement](https://www.instagram.com/p/Da1kG9mzoH7/)** — arrange the parts on the board.
8. **[PCB design → Fabrication](https://www.instagram.com/p/Da24SWXz-QB/)** — order the PCB.
9. **PCB → Mechanical case design**
10. **Finished hardware + code**
11. **Final testing**

## What is on the board?

| Module or part | Role |
| --- | --- |
| Glyph controller module | Coordinates the radio, audio, display, storage, and controls |
| nRF24L01 radio module | Provides the 2.4 GHz wireless link |
| Two-channel microphone module | Captures voice input |
| GMOD-NAU8325 audio module | Handles audio output to the speaker connectors |
| 0.91-inch OLED | Shows information on the device |
| MicroSD module | Provides removable storage |
| 18650 battery holder and slide switch | Supply battery power and provide a power switch |
| Push-to-talk button | Lets the user start transmitting |
| Rotary encoder header and LED | Provide an input connection and visual indicator |

## Project files

| Path | What it contains |
| --- | --- |
| [walkietalkie.kicad_pro](walkietalkie.kicad_pro) | KiCad project |
| [walkietalkie.kicad_sch](walkietalkie.kicad_sch) | Schematic |
| [walkietalkie.kicad_pcb](walkietalkie.kicad_pcb) | PCB layout |
| [project-assets/](project-assets/) | Custom symbols, footprints, and 3D models |
| [the final gerber/](the%20final%20gerber/) | Gerber files, drill file, and fabrication ZIP |

Open the KiCad project to explore the hardware design. Keep project-assets next to the project files so the custom parts and 3D models remain available.
