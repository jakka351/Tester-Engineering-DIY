# Guide 03 — Recommended hardware & shopping list

Parts for both DIY paths, with Australian and international vendors. **Prices are indicative
(Oct 2026) and move around — check the live listing.** AUD unless noted.

---

## Path ① — Raspberry Pi + CAN HAT (recommended for shop use)

| # | Item | Recommended product | Approx price | Where |
|---|------|---------------------|-------------:|-------|
| 1 | Single-board computer | **Raspberry Pi 4 Model B** (2 GB is plenty; 4 GB nicer) | $75–130 | [Core Electronics](https://core-electronics.com.au/) (AU), [Little Bird](https://littlebirdelectronics.com.au/) (AU) |
| 2 | **CAN HAT with 12 V SMPS** | **PiCAN3 — CAN-Bus board for Pi 4 with 3 A SMPS + RTC** (MCP2515, 16 MHz, DB9 + screw terminal) | ~£64 ex VAT (~$130–160 landed) | [SK Pang](https://www.skpang.co.uk/products/pican3-can-bus-board-for-raspberry-pi-4-with-3a-smps-rtc) (UK, ships AU), [Copperhill](https://copperhilltech.com/pican3-can-bus-board-for-raspberry-pi-4-with-3a-smps-and-rtc/) (US) |
| 3 | **OBD-II → DB9 cable** | OBD-II to DB9 (SparkFun SF-CAB-10087) | ~$32 | [Core Electronics](https://core-electronics.com.au/obd-ii-to-db9-cable.html) (AU), [Little Bird](https://littlebirdelectronics.com.au/products/obd-ii-to-db9-cable) (AU) |
| 4 | **Case** | PiCAN2/3 enclosure (DB9 cut-out) or a Pi 4 case with HAT clearance | $15–30 | SK Pang / Copperhill (matched case), generic AU |
| 5 | microSD card | 32 GB, Class 10 / A1 | $12–18 | any |
| 6 | RTC battery *(optional)* | **CR1225** coin cell for the PiCAN3 RTC | $3 | any |
| 7 | Bench power *(optional)* | Official Pi 4 USB-C PSU (5 V 3 A) for bench use off the car | $15–20 | Core Electronics / Little Bird |

**Powering from the vehicle:** the PiCAN3's onboard **3 A switch-mode supply accepts ~6–24 V** and
makes 5 V for the Pi, so OBD **pin 16 (+12 V) → DB9 pin 9** powers the whole rig off the OBD port. No
separate supply needed in the car. (Pin 16 is always-live — unplug between jobs; see the README.)

### Australian alternative to the PiCAN3 (no import)

Core Electronics stocks **Waveshare** CAN HATs locally. They work identically for SocketCAN but
have **no onboard 12 V SMPS** and a **different crystal** — so you power the Pi separately and change
the overlay:

| Item | Price | Notes |
|------|------:|-------|
| [Waveshare RS485 CAN HAT](https://core-electronics.com.au/) | ~$23 | 1× MCP2515, **12 MHz** crystal → `oscillator=12000000`. Cheapest. Also has RS485 you can ignore. |
| [Waveshare 2-Channel Isolated CAN HAT](https://core-electronics.com.au/2-channel-isolated-can-bus-expansion-hat-raspberry-pi-waveshare.html) | ~$33–42 | Two isolated channels (galvanic isolation is nice near a vehicle). 12 MHz. |
| Automotive 12 V→5 V USB-C buck (3 A) | ~$15–30 | Needed to power the Pi from the car if the HAT has no SMPS. |

> With a Waveshare HAT, set `dtoverlay=mcp2515-can0,oscillator=12000000,interrupt=25` (verify the INT
> GPIO for your board) — otherwise `can0` comes up dead. See [Guide 01 §5.2](01-RaspberryPi-SocketCAN-socketcand.md#52-the-overlay-line--oscillator-is-the-1-gotcha).

> **Pi 5 note:** the PiCAN3 is designed for the **Pi 4**. On a Pi 5 the RTC/overlay and some HATs
> need changes — stick with a **Pi 4** for the known-good build, or check SK Pang for a Pi-5 board.

**Typical Path ① cost:** ~$250–320 landed with a genuine PiCAN3, or ~$140–180 all-AU with a Waveshare HAT.

---

## Path ② — Arduino + MCP2515 (cheapest)

| # | Item | Recommended product | Approx price | Where |
|---|------|---------------------|-------------:|-------|
| 1 | Microcontroller | **Arduino UNO R3** (or Nano, or a clone) | $15–45 | [Core Electronics](https://core-electronics.com.au/), [Little Bird](https://littlebirdelectronics.com.au/) |
| 2 | **CAN module** | MCP2515 + TJA1050 "blue board" (**check 8 MHz** crystal) | $8–15 | Core Electronics / Little Bird / eBay |
| 3 | Jumper wires | Female–female Dupont | $5 | any |
| 4 | OBD-II → DB9 cable *or* OBD-II breakout | as Path ① #3, or a bare OBD-II pigtail | $15–32 | Core Electronics / Little Bird |
| 5 | USB cable | Arduino → laptop (often included) | — | — |

**All-in-one shield alternative:** a **Seeed Studio CAN-BUS Shield v2** plugs straight onto the UNO
(no wiring) — ~$40. Its chip-select is **D9** (set `SPI_CS_PIN = 9`).

**Typical Path ② cost:** ~$30–70.

### Arduino → turnkey slcan

If you'd rather buy a finished slcan dongle than build one, any of these are slcan/Lawicel out of
the box and plug straight into the TES **Serial-CAN** transport:

| Product | Approx price | Notes |
|---------|-------------:|-------|
| **CANable 2.0 / 2.5** | ~$30–50 USD | STM32, slcan **and** candlelight firmware, well supported. [canable.io](https://canable.io/) |
| **SH-C30L USB-to-CAN** | ~$20–30 USD | USB slcan adapter, works with can-utils/TES. |
| **meatPi mpUSB-CAN** (AU) | ~AUD $60 | Australian-made USB-CAN. [meatpi.com](https://www.meatpi.com/products/mpusb-can) |

---

## Which path, by budget and use

| You want… | Build |
|-----------|-------|
| A proper wireless shop tool, powered off the car | **Path ① + genuine PiCAN3** |
| Same, cheapest, all bought in Australia | **Path ① + Waveshare HAT** + a 12 V→5 V buck |
| A $40 USB dongle for the bench / learning | **Path ② (Arduino + MCP2515)** |
| A finished dongle with zero soldering | **CANable 2.0** (turnkey slcan) |

---

## The one table to get right — oscillator per board

Wrong oscillator = `can0`/the dongle comes up but the bus reads dead. **Read the crystal can.**

| Board | Crystal marking | Setting |
|-------|-----------------|---------|
| PiCAN2 / PiCAN3 | `16.000` | `oscillator=16000000` (Pi) |
| Waveshare RS485 / 2-CH CAN HAT | `12.000` | `oscillator=12000000` (Pi) |
| Blue MCP2515 module (Arduino) | `8.000` | 8 MHz option in the sketch |
| Blue MCP2515 module (16 MHz variant) | `16.000` | 16 MHz option |

---

**Back to:** [README](README.md) · [Guide 01 — Raspberry Pi / SocketCAN](01-RaspberryPi-SocketCAN-socketcand.md) · [Guide 02 — Arduino / slcan](02-Arduino-slcan-MCP2515.md)

### Sources
- [SK Pang PiCAN3](https://www.skpang.co.uk/products/pican3-can-bus-board-for-raspberry-pi-4-with-3a-smps-rtc) · [Copperhill PiCAN3](https://copperhilltech.com/pican3-can-bus-board-for-raspberry-pi-4-with-3a-smps-and-rtc/)
- [OBD-II → DB9 cable (Core Electronics)](https://core-electronics.com.au/obd-ii-to-db9-cable.html) · [Little Bird](https://littlebirdelectronics.com.au/products/obd-ii-to-db9-cable)
- [Waveshare 2-CH Isolated CAN HAT (Core Electronics)](https://core-electronics.com.au/2-channel-isolated-can-bus-expansion-hat-raspberry-pi-waveshare.html)
- [CANable](https://canable.io/) · [meatPi mpUSB-CAN](https://www.meatpi.com/products/mpusb-can) · [Seeed CAN-BUS Shield](https://github.com/Seeed-Studio/Seeed_Arduino_CAN)
