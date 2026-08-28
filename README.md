# STM32 Field Node

A compact STM32G031-based microcontroller board with USB-C power, 3.3 V regulation, SWD debugging, and a 2-layer PCB.

## Status

**Hardware revision:** Rev A  
**Fabrication:** Gerber and drill files generated  
**Assembly:** Not assembled  
**Validation:** Not electrically validated

KiCad ERC passes cleanly. PCB DRC has no connectivity or manufacturing-rule violations; the intentional local USB-C footprint override is excluded from the library-mismatch check.

## Hardware

### MCU

- STM32G031K8T6
- ARM Cortex-M0+
- LQFP-32
- 3.3 V operation

### Power

Power is supplied through USB-C.

```text
USB-C 5 V
    |
    v
TLV75533 LDO
    |
    v
3.3 V
    |
    v
STM32G031
```

The board uses:

- TLV75533 3.3 V LDO
- 1 µF input capacitor
- 4.7 µF output capacitor
- 100 nF MCU decoupling
- 4.7 µF local bulk capacitance

### USB-C

USB-C is used for 5 V power input only. Firmware programming and debugging are provided through the SWD header.

- 5 V VBUS
- 5.1 kΩ Rd resistors on CC1 and CC2
- D+ and D− not connected
- TVS protection on VBUS
- Shield connected to GND

### Debug

The board exposes an SWD programming/debug header:

| Pin | Signal |
| ---: | --- |
| 1 | VTREF (3V3) |
| 2 | SWDIO |
| 3 | SWCLK |
| 4 | NRST |
| 5 | GND |

This can be connected to an ST-Link or compatible SWD debugger.

## Key Components

| Ref | Part | Description |
| --- | --- | --- |
| U1 | STM32G031K8T6 | 32-bit Arm Cortex-M0+ microcontroller |
| U2 | TLV75533PDBVR | 3.3 V LDO regulator |
| J1 | GCT USB4105 | USB-C receptacle |
| D1 | PESD5V0S1UA | VBUS TVS protection |

## PCB

- 2-layer PCB
- Components placed on the front side
- F.Cu used primarily for signal and power routing
- B.Cu maintained primarily as a continuous GND plane
- SMD passive components
- LQFP-32 MCU
- USB-C receptacle
- Dedicated GND vias for local decoupling and power circuitry

The PCB includes custom front silkscreen artwork and board identification.

## Design Notes

### Decoupling

The STM32 supply uses a local 100 nF decoupling capacitor placed close to the MCU VDD/VSS pins.

Additional 4.7 µF bulk capacitance is provided on the 3.3 V rail.

### LDO

The TLV75533 converts USB VBUS from 5 V to 3.3 V.

Input and output capacitors are placed close to the regulator to minimize local power-loop impedance.

### Ground

The bottom copper layer is used as a GND plane.

Critical ground connections, including MCU decoupling, regulator bypass, and VBUS transient protection, use short connections to nearby vias into the ground plane.

### USB-C Footprint

The GCT USB4105 footprint is locally overridden to increase NPTH-to-copper clearance.

The four affected GND pads were reduced from `0.60 mm × 1.15 mm` to `0.60 mm × 1.00 mm`.

The modification applies to `A1`, `A12`, `B1`, and `B12`.

This local override intentionally differs from the KiCad library footprint, and the corresponding library-mismatch DRC warning is excluded.

## Repository Layout

```text
hardware/
├── fab/
│   ├── drill/
│   │   ├── stm32-field-node-NPTH.drl
│   │   └── stm32-field-node-PTH.drl
│   └── gerber/
│       ├── stm32-field-node-B_Cu.gbr
│       ├── stm32-field-node-B_Mask.gbr
│       ├── stm32-field-node-Edge_Cuts.gbr
│       ├── stm32-field-node-F_Cu.gbr
│       ├── stm32-field-node-F_Mask.gbr
│       ├── stm32-field-node-F_Silkscreen.gbr
│       └── stm32-field-node-job.gbrjob
├── reports/
│   └── DRC.rpt
├── stm32-field-node.kicad_pcb
├── stm32-field-node.kicad_pro
└── stm32-field-node.kicad_sch
```

## Fabrication

Gerber and Excellon drill files are available under `hardware/fab/`.

The fabrication package contains:

- Front copper
- Back copper
- Front solder mask
- Back solder mask
- Front silkscreen
- Board outline
- Plated through-hole drill data
- Non-plated through-hole drill data
- Gerber job file

The generated files have been inspected using KiCad Gerber Viewer.

Before ordering a new revision, regenerate the fabrication files from the current PCB source and run DRC again.

## Bring-Up

Recommended first power-up procedure:

1. Check resistance between 3V3 and GND.
2. Check resistance between USB_5V and GND.
3. Apply USB power with current limiting if available.
4. Verify USB_5V.
5. Verify +3V3.
6. Check the LDO for unexpected heating.
7. Connect ST-Link.
8. Verify that the MCU can be detected over SWD.
9. Read the MCU device ID.
10. Flash minimal test firmware.
11. Verify reset operation.
12. Verify basic GPIO operation.

## Revision History

### Rev A

Initial hardware revision.

- STM32G031K8T6 MCU
- USB-C 5 V power input
- TLV75533 3.3 V regulation
- VBUS transient protection
- SWD programming and debugging interface
- 2-layer PCB with bottom GND plane
- Initial fabrication package
