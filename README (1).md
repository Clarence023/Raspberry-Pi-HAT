# Pi-HAT WiFi Monitor

A custom Raspberry Pi HAT that pairs an onboard **Wemos D1 Mini (ESP8266)** and a **0.96" SSD1306 OLED** to give a Raspberry Pi a live, standalone WiFi monitoring readout — channel, packet count, deauth frame count, and packet rate — driven over **UART**.

![WiFi Monitor board, side view](images/wifi-monitor-side.jpg)

> Board silkscreen: `Clarence®` — designed and hand-assembled by [Clarence J (CJ)](https://github.com/Clarence023).

---

## Overview

This project is a two-tier hardware stack:

1. **Custom Raspberry Pi HAT (green PCB)** a carrier board that mounts directly on the Pi's 40-pin GPIO header and breaks out power, UART, and mounting for the daughterboard below.
2. **ESP8266 + OLED daughterboard** — a Wemos D1 Mini running the WiFi monitoring firmware, paired with an SSD1306 128x64 I2C OLED that renders live stats. This talks to the Raspberry Pi over UART (TX/RX), so the Pi can log, relay, or act on the data the ESP8266 collects.

The D1 Mini puts its WiFi radio into monitor/promiscuous mode and continuously samples 802.11 traffic, auto-hopping channels, while streaming a live summary to its own OLED **and** to the Pi over serial.

| | |
|---|---|
| **Host** | Raspberry Pi (via custom HAT) |
| **Sensor / radio node** | Wemos D1 Mini (ESP8266) |
| **Display** | SSD1306 0.96" 128x64 OLED, I2C |
| **Link** | UART (TX/RX) between Pi HAT and D1 Mini |
| **PCB** | Custom 2-layer HAT, silkscreened "Clarence®" |

---

## Features

- 📡 **Live WiFi monitor mode** — passively captures 802.11 frames in the ESP8266's monitor mode (no association required)
- 🔁 **Auto channel hopping** — cycles through channels to sweep the full 2.4 GHz band (`WiFi Monitor (AUTO)` mode shown on-screen)
- 🎯 **Deauth frame detection** — tallies deauthentication frames separately from general traffic for quick anomaly spotting
- 📊 **On-device OLED readout** — channel, total packets, deauth count, and rolling packet rate (pkt/s) updated in real time
- 🔌 **UART bridge to Raspberry Pi** — the same stats stream to the Pi for logging, alerting, or further processing
- 🧩 **HAT form factor** — stacks directly onto the Pi's GPIO header, no jumper wires needed for power/ground
- 🛠️ **Breadboard prototype included** — an earlier perfboard revision (ESP8266 + OLED) documents the bring-up path before the final PCB

---

## Hardware

### Pi HAT (carrier board)

- Full 40-pin GPIO passthrough header
- Mounts the D1 Mini + OLED daughterboard directly on top
- Routes UART (and power) between the Pi and the ESP8266
- Custom silkscreen branding (`Clarence®`)

### D1 Mini (ESP8266) pinout in use

| D1 Mini pin | Function |
|---|---|
| `3V3` | 3.3V rail |
| `5V` | 5V input |
| `G` | Ground |
| `RX` / `TX` | UART link to Raspberry Pi |
| `D1` / `D2` (SDA/SCL) | I2C to SSD1306 OLED |
| `D3`–`D8`, `A0`, `RST` | Available / reserved |

### OLED (SSD1306, I2C)

| OLED pin | Connects to |
|---|---|
| `GND` | GND |
| `VCC` | 3V3 |
| `SCL` | D1 Mini SCL |
| `SDA` | D1 Mini SDA |

![Front view of D1 Mini and OLED daughterboard on the HAT](images/wifi-monitor-front.jpg)

![Perfboard prototype next to the final Pi HAT](images/hat-and-daughterboard.jpg)

---

## Repository Structure

```
pi-hat-wifi-monitor/
├── README.md
├── firmware/           # ESP8266 (Arduino/PlatformIO) monitor-mode + OLED + UART code
├── pi/                 # Raspberry Pi-side UART listener / logger scripts
├── hardware/           # KiCad/Altium source, gerbers, schematic, BOM
├── docs/               # Additional notes, wiring diagrams, bring-up log
└── images/             # Board photos (this README's assets)
```

> This repo is being organized incrementally — firmware, PCB source, and Pi-side scripts will be added as the project is cleaned up for publication.

---

## How It Works

1. On boot, the ESP8266 initializes the SSD1306 over I2C and enters WiFi monitor (promiscuous) mode.
2. It auto-hops across channels, sniffing beacon/data/management frames.
3. Deauthentication frames are counted separately from general packet traffic.
4. Every cycle, the OLED refreshes with the current channel, cumulative packet count, deauth count, and a smoothed packet rate (pkt/s).
5. The same summary is written out over UART (TX/RX) to the Raspberry Pi, which can log it, forward it, or trigger alerts (e.g., on a deauth spike — a common indicator of a WiFi deauth attack).

---

## Status

- [x] Perfboard prototype (ESP8266 + OLED) validated
- [x] Custom Pi HAT PCB fabricated and assembled
- [x] WiFi monitor mode with channel/packet/deauth/rate stats working on-device
- [ ] Pi-side UART logger/dashboard
- [ ] Publish firmware source
- [ ] Publish PCB design files (KiCad/Altium + gerbers)
- [ ] Enclosure / case design

---

## Author

**Clarence J (CJ)** — Final-year ECE undergraduate
- GitHub: [@Clarence023](https://github.com/Clarence023)
- LinkedIn: [clarence-j](https://linkedin.com/in/clarence-j)

## License

*(Choose a license — e.g. MIT for firmware/software, CERN-OHL for hardware — and add a `LICENSE` file.)*
