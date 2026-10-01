# Stacked PCB layout: initial placement

## Mechanical definition

- Both boards use the same **120 mm x 90 mm** outline.  This is within the
  requested maximum of 90 mm x 120 mm.
- Four aligned M3 plastic-screw clearance holes (3.2 mm NPTH) are at
  `(45, 55)`, `(155, 55)`, `(45, 135)`, and `(155, 135)` mm in KiCad board
  coordinates.  Each hole is 5 mm from its two nearest board edges.
- The lower board carries pin header `J1`; the upper board carries its mating
  socket `J1`.  Both are at `(52, 120)` and use a 2x6, 2.54 mm pitch pattern.

## Board partition

| Board | Contents |
| --- | --- |
| Upper / control | Arduino Nano A2, display U1, display driver U2 and R1-R8, SW1/SW2, error LED D1 with R9, buzzer, and the local reset/start-stop parts. |
| Lower / logic | Clock and debounce ICs U6-U8, input/error logic U3-U5, counters U9-U13, associated passives, and SW3-SW6 with RN1-RN4. |

The four user value selectors remain on the lower board.  They are aligned at
the accessible lower edge so they can be replaced by, or wired to, a thumb
rotary-switch header during the following routing stage.

## J1 pin assignment

| Pin | Net | Purpose |
| ---: | --- | --- |
| 1 | `/VCC` | +5 V supply |
| 2 | `GND` | Return |
| 3 | `/PL` | Parallel-load control |
| 4 | `/CP` | Shift clock |
| 5 | `/D5` | Arduino-to-logic signal |
| 6 | `/D8` | Arduino-to-logic signal |
| 7 | `/A1` | Debounced logic-to-Arduino signal |
| 8 | `reset_btn` | Reset button path |
| 9 | `start-stop_btn` | Start/stop button path |
| 10 | `GND` | Additional return |
| 11-12 | — | Reserved |

## Routing status

- The upper board was subsequently adjusted manually by the user.  It has not
  been changed or re-verified during the lower-board work.
- Lower board: the B.Cu GND plane was restored, connected with local solid
  plane connections where thermal spokes are constrained, and refilled.
- Lower board: all `/VCC` pads, the full J1 signal interface (including
  `/PL`), local clock/debounce links, the U3/U4/U9/U10/U11/U12 logic buses,
  555 branches, reset/start-stop paths, and SW3–SW6 selector wiring are
  routed.
- Final DRC after zone refill reports **0 unconnected items** and **0 errors**.
  The only remaining warnings are eight pre-existing local
  `lib_footprint_mismatch` notices (the four mounting holes, C12–C14, and J1).

The source `timer.kicad_pcb` remains unchanged.
