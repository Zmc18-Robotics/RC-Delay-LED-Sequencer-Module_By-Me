# RC Delay LED Sequencer

A circuit that turns LEDs on and off in a staggered sequence using resistors, capacitors, and NPN transistors — no microcontroller or digital IC required. Each LED has its own RC delay stage, so when power is applied the LEDs turn on one after another, and when power is removed they turn off one after another in the same order.

![status](https://img.shields.io/badge/status-active-brightgreen)
![platform](https://img.shields.io/badge/PCB-KiCad-orange)
![license](https://img.shields.io/badge/license-MIT-blue)

## Circuit
<img width="469" height="341" alt="Breadboard" src="https://github.com/user-attachments/assets/b9006f62-0787-4af6-8d6d-7f3020b1ff6a" />

## Electrical Circuit
<img width="550" height="173" alt="Electrical1" src="https://github.com/user-attachments/assets/254a3df9-032c-46cc-913a-9ee0541e534b" />

## Gerber
<img width="247" height="350" alt="Gerber1" src="https://github.com/user-attachments/assets/416a6162-f8e4-46ba-89c9-d7bbe4b07e56" />

## PCB
<img width="772" height="416" alt="PCB2" src="https://github.com/user-attachments/assets/10d67e46-8c20-4325-a846-0d238a0efbb9" />


## Table of contents

- [How it works](#how-it-works)
- [Features](#features)
- [Schematic](#schematic)
- [Transistor pinout](#transistor-pinout)
- [Bill of materials (BOM)](#bill-of-materials-bom)
- [Repository structure](#repository-structure)
- [PCB (KiCad)](#pcb-kicad)
- [Build instructions](#build-instructions)
- [Customizing the delay](#customizing-the-delay)
- [Roadmap](#roadmap)
- [License](#license)

## How it works

Each stage consists of an RC network (resistor + capacitor) that charges the base voltage of an NPN transistor. The time constant tau = R x C determines how quickly that transistor "turns on" and lights the LED connected to its collector.

- **On power-up**: the capacitor starts charging through the resistor. The stage with the smallest tau reaches the base threshold (~0.7 V) first, so its LED lights up first.
- **On power-down**: the capacitor discharges through the same resistor. The stage with the smallest tau also empties first, so its LED turns off first.

Because charging and discharging use the same RC path, the turn-on order and turn-off order stay consistent (1, 2, 3, ... N for both) with no extra logic needed.

## Features

- Fully analog, no microcontroller or digital IC
- Consistent turn-on and turn-off order (unlike a typical 555+4017 chaser, which only sequences turn-on)
- Per-stage delay easily tuned via the capacitor value
- Scales from a few LEDs to dozens of stages
- Ready-to-fabricate KiCad PCB module included

## Schematic

Each stage consists of:

```
Vcc ── R_bias ──┬── C (to GND)
                └── Base (Q NPN)

Vcc ── R_LED ── LED ── Collector (Q)

Emitter (Q) ── GND
```

All stages are wired in parallel across the same Vcc and GND rails; only R_bias and/or C differ per stage to set the delay order.

Full schematic file: [`hardware/kicad/rc-led-sequencer.kicad_sch`](hardware/kicad/rc-led-sequencer.kicad_sch)

> Export a schematic image (PNG/SVG) from KiCad to `docs/images/schematic.png` and display it here:
>
> `![schematic](docs/images/schematic.png)`

## Transistor pinout

For a TO-92 NPN transistor (e.g. BC547, 2N2222) with the flat side facing you and the leads pointing down:

| Pin | Function | Connects to |
|---|---|---|
| Base (B) | Control signal from the RC node | R_bias + C |
| Collector (C) | Current path from the LED | R_LED + LED (cathode) |
| Emitter (E) | Current path to ground | GND |

> Physical pin order (E-B-C or otherwise) varies by transistor type — always check the datasheet for the exact part you're using before soldering.

## Bill of materials (BOM)

Example values for 10 stages (10 LEDs). R_bias and R_LED are kept constant; only the capacitor value increases per stage.

| Stage | C (µF) | R_bias | R_LED | Transistor | LED |
|---|---|---|---|---|---|
| 1 | 100 | 10 kΩ | 330 Ω | BC547 | 5 mm, any color |
| 2 | 300 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 3 | 500 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 4 | 700 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 5 | 900 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 6 | 1100 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 7 | 1300 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 8 | 1700 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 9 | 1900 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 10 | 2100 | 10 kΩ | 330 Ω | BC547 | 5 mm |
...

Additional components:

| Component | Qty | Notes |
|---|---|---|
| Electrolytic capacitors (values above) | 10 | Minimum 16 V rated |
| 10 kΩ resistor | 10 | R_bias, 1/4 W |
| 330 Ω resistor | 10 | R_LED, 1/4 W |
| NPN transistor BC547 / 2N2222 | 10 | Equivalents can be substituted |
| 5 mm LED | 10 | Color to taste |
| Power header/connector | 1 | Vcc + GND input |
| PCB | 1 | See `hardware/` folder |

> Update this table to match the final BOM exported from KiCad (`Tools > Generate BOM`), or add an exported `hardware/bom.csv` file.

## Repository structure

```
.
├── hardware/
│   └── kicad/
│       ├── rc-led-sequencer.kicad_pro
│       ├── rc-led-sequencer.kicad_sch
│       ├── rc-led-sequencer.kicad_pcb
│       └── gerbers/
├── docs/
│   └── images/
│       ├── schematic.png
│       ├── pcb-top.png
│       ├── pcb-bottom.png
│       └── demo.gif
├── LICENSE
└── README.md
```

> Adjust this structure to match your actual KiCad file names.

## PCB (KiCad)

This module has been fully laid out as a single board in KiCad, covering the schematic and PCB layout for all stages together (not separate per-LED modules).

To open it:

1. Install [KiCad](https://www.kicad.org/) version 7 or later.
2. Clone this repo, then open `hardware/kicad/rc-led-sequencer.kicad_pro`.
3. Open `rc-led-sequencer.kicad_sch` to view the full schematic.
4. Open `rc-led-sequencer.kicad_pcb` to view the PCB layout and 3D view (`View > 3D Viewer`).

For fabrication:

1. Open the `.kicad_pcb` file.
2. `File > Fabrication Outputs > Gerbers` to generate the gerber files.
3. Also generate the drill file (`.drl`).
4. Send the generated files to your preferred PCB fabricator (JLCPCB, PCBWay, etc.).

> Add board specs here once finalized: board size, layer count, copper thickness, and any other design notes.

## Build instructions

1. Solder components per the BOM, starting with the shortest (resistors) and working up to the tallest (electrolytic caps, transistors, LEDs).
2. Watch the polarity of the electrolytic capacitors (the negative lead is usually marked with a stripe on the body) and the LED orientation (longer lead = anode).
3. Double-check transistor orientation against the datasheet before soldering.
4. Connect a power source (5-9 V DC) to the power header, observing polarity.
5. Power on and watch the LEDs light up in sequence; power off and watch them turn off in the same order.

## Customizing the delay

- Each stage's delay roughly follows `t ≈ 0.7 x R_bias x C` (an approximation — actual timing is also affected by Vcc and transistor characteristics).
- To speed up the whole sequence: lower R_bias proportionally across all stages.
- To slow it down: increase the capacitor values.
- The gap between stages is set by how much the capacitor value increases from one stage to the next — a bigger ratio makes the delay between LEDs more noticeable.

## Roadmap

- [ ] Add photos and demo video
- [ ] Auto-export BOM from KiCad
- [ ] Add a variant with a potentiometer for adjustable delay
- [ ] Test with more than 10 stages

## License

This project is released under the MIT License — see the [LICENSE](LICENSE) file for details. Swap this for an open hardware license like CERN-OHL if that fits your project better.
