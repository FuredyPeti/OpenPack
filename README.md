# OpenPack

**An open-source, field-serviceable USB-C PD power bank built around standard 18650 cells.**

OpenPack is a portable battery pack designed to be repaired, not replaced. Instead of a sealed pouch cell, it holds six user-replaceable 18650 cells in a 2P3S configuration, so when capacity fades you swap cells instead of throwing the whole device away. It's built around a single PCB (`BATMOD_V1p0`) housed in a 3D-printed, military-styled enclosure with a CNC-machined aluminum front panel.

![OpenPack_BATMOD_V1p0](./DOC/PICTURES/3Drender.png)

---

## Highlights

- **6x 18650 cells in swappable holders** — 2P3S pack, use any cell you like
- **100 W USB-C PD port** — sink or source up to 20 V / 5 A
- **Two fixed 5 V / 2 A USB-A ports** for everyday device charging
- **Dedicated hardware balancing and protection** for the 3S pack
- **Single-board design** (`BATMOD_V1p0`) — everything lives on one PCB
- **3D-printed enclosure** with a machined aluminum port plate
- **Fully open source** — schematics, PCB, and mechanical files included

---

## Specifications

| | |
|---|---|
| Cell configuration | 2P3S (6x 18650, user-supplied, socketed in holders) |
| USB-C port | USB PD, sink/source up to 20 V @ 5 A (100 W) |
| USB-A port 1 | Fixed 5 V, up to 2 A |
| USB-A port 2 | Fixed 5 V, up to 2 A |
| Charging input | USB-C only (USB-A ports are output-only) |
| PCB | BATMOD_V1p0 | 94mm x 140mm
| Enclosure | 3D-printed body, CNC-machined aluminum front panel |

---

## Electronics

### USB-C PD port — TPS25751SRSMR

The USB-C port is driven by TI's **TPS25751**, a standalone USB Type-C and Power Delivery controller with integrated power switches. It's USB-IF certified for PD 3.1 and handles the full Type-C/PD negotiation stack on its own, so the port can act as either a sink or a source and negotiate up to 20 V at 5 A (100 W). The chip also includes dead-battery boot support (it can power itself from VBUS with no cell installed) and integrated protection — reverse current, over/undervoltage, and inrush current limiting — on its power paths. It also natively integrates control signaling for external battery-charger ICs like the BQ25798 below, which is how it hands off charging duties to the rest of the board.

### Battery charging — BQ25798RQMR

Charging is managed by the **BQ25798**, a 5 A, I2C-controlled buck-boost charger for 1- to 4-cell Li-ion/Li-poly packs. It runs a wide 3.6 V–24 V input range, is USB PD-compliant, and automatically switches between buck, boost, and buck-boost modes depending on input and battery voltage — useful given how much VBUS can swing across different PD sources. It also includes input current optimization (ICO) and MPPT for oddball or current-limited sources.

### Battery protection & cell balancing — BQ7791500PWT (BQ77915)

Pack-level safety and balancing is handled by the **BQ77915**, a stackable primary protector purpose-built for 3S–5S packs. It runs autonomous passive cell balancing (up to 50 mA) without needing a microcontroller, on top of the usual overvoltage/undervoltage/overcurrent/overtemperature protection suite — matched here to the pack's 3S configuration.

### USB-A output ports — TPS2547RTER

The two USB-A ports are each driven by a **TPS2547**, a USB charging port controller with an integrated 3 A power switch and D+/D− load detection. It presents the correct charging signatures (BC1.2 and common non-BC1.2 legacy schemes) so phones, tablets, and other USB-A devices auto-detect the port and charge at their expected rate. On OpenPack these are configured for a fixed 5 V output, capped at 2 A, and are output-only — they cannot be used to charge the internal pack.

---

## Mechanical design

Everything lives on one board — **BATMOD_V1p0** — with the six 18650 holders mounted on one side of the PCB. The board drops into a 3D-printed enclosure styled after military-grade equipment, and the front plate, where the USB-C and both USB-A ports live, is CNC-machined from aluminum rather than printed, both for durability at the connectors and for the finish.

---

## Repository contents

- `/HW` — schematics and PCB design files for BATMOD_V1p0
- `/MECH` — 3D-printable enclosure and aluminum front-plate machining files
- `/SYS` — assembly notes and reference material

---

## Safety note

This project involves user-configurable Li-ion packs and mains-adjacent charge currents. If you build one, use protected/authentic cells, verify balancing and protection behavior before first full charge, and don't leave a pack charging unattended until you've validated your build.

