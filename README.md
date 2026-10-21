# Glyph teleVox

teleVox is a DIY walkie-talkie carrier board for the Glyph ecosystem. The PCB brings a Glyph S3 board, radio module, audio codec, microphone, display, battery holder, and supporting components together in a handheld design. It grew out of a breadboard prototype and was designed in KiCad as a first PCB project.

The source project's July 2026 README says the first board revision was sent for manufacturing. Assembly, bring-up, firmware, enclosure fitting, and field testing were still planned at that point. This repository contains the hardware design and fabrication files; it does not include firmware or a verified build guide.

## Files

| Path | Purpose |
| --- | --- |
| [`walkietalkie.kicad_pro`](walkietalkie.kicad_pro) | KiCad project settings |
| [`walkietalkie.kicad_sch`](walkietalkie.kicad_sch) | Schematic |
| [`walkietalkie.kicad_pcb`](walkietalkie.kicad_pcb) | PCB layout |
| [`fp-lib-table`](fp-lib-table), [`sym-lib-table`](sym-lib-table) | Project library references |
| [`project-assets/`](project-assets/) | Custom symbols, footprints, and 3D models used by the design |
| [`the final gerber/`](the%20final%20gerber/) | Fabrication plots, drill file, job file, and the original `walkietalkie.zip` fabrication archive |

Open `walkietalkie.kicad_pro` in KiCad 10 to inspect or edit the design. Keep `project-assets` beside the project files: the PCB and library tables use paths relative to the project directory. For fabrication, use `the final gerber/walkietalkie.zip`; its extracted files are also included for inspection. Review the design and fabrication outputs against your manufacturer's requirements before ordering.

## Origin

The hardware files come from [iammaniron/talkiewalkie](https://github.com/iammaniron/talkiewalkie) at commit [`23a049c`](https://github.com/iammaniron/talkiewalkie/commit/23a049c176a4f149fa0cb47be465b1cddc30b9c2). The source README credits ManIron and the mentors who helped review the design. Its original project title is *teleVox — A DIY Walkie Talkie*.

The source README describes the project as open-source hardware, but the source repository does not contain a license file. Check with the original author before relying on a particular reuse license.
