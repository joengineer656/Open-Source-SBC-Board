# Open-Source-SBC-Board

A custom single-board computer built around the Rockchip RK3576 SoC, designed as a learning project to explore modern SBC architecture.

> **Status: Work in Progress** — schematic complete, PCB layout in progress. Not yet fabricated.

![KiCad 3D render placeholder](docs/board-render.png)

## Overview

This board is a learning project exploring the design of a modern, feature-rich SBC. It combines a high-performance RK3576 application processor with an RP2350A companion MCU for IO offloading, targeting a balance between compute power and real-time IO capability.

## Key Specifications

| Feature | Specification |
|---------|--------------|
| **SoC** | Rockchip RK3576 |
| **Companion MCU** | Raspberry Pi RP2350A (IO co-processor) |
| **RAM** | Up to 16 GB LPDDR5 |
| **Storage** | UFS 3.1 (up to 128 GB), eMMC, µSD card |
| **Video Output** | HDMI, MIPI DSI |
| **Camera** | MIPI CSI |
| **Networking** | Gigabit Ethernet, WiFi + Bluetooth (AP6256) |
| **USB** | USB 3.2 Host, USB 3.2 OTG, USB 2.0 hub |
| **Expansion** | 40-pin RPi-compatible header, 20-pin header, PCIe |
| **Display** | 0.96" TFT OLED (ST7735S, 80×160) |
| **RTC** | RV-3028-C7 |
| **PMIC** | RK806S-5 |
| **ADC** | SARADC (on SoC) |

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    RK3576 SoC                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │  CPU     │  │  GPU     │  │  Memory Ctrl     │  │
│  │  (ARM)   │  │          │  │  (LPDDR5)        │  │
│  └──────────┘  ──────────┘  └──────────────────  │
│  ┌──────────  ┌──────────┐  ┌──────────────────┐  │
│  │  PCIe    │  │  USB 3.2 │  │  MIPI CSI/DSI    │  │
│  └──────────  └──────────┘  └──────────────────┘  │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │  HDMI    │  │  Ethernet│  │  UFS/eMMC/SD     │  │
│  └──────────┘  └──────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────┘
         │                    │
         │ SPI/I2C/UART       │ USB
         ▼                    ▼
─────────────────┐   ┌─────────────────┐
│   RP2350A MCU   │   │   USB Hub       │
│  (IO offload)   │   │  (4× USB 2.0)   │
└─────────────────┘   └─────────────────┘
         │
         ▼
┌─────────────────┐
│  40-pin Header  │
│  (RPi compat)   │
└─────────────────┘
```

## Design Goals

1. **Learn modern SBC design** — power delivery, high-speed interfaces (LPDDR5, UFS, PCIe, USB 3.2), mixed-signal layout
2. **Real-time IO capability** — RP2350A handles time-critical tasks (GPIO, PWM, ADC) independently of the Linux-running SoC
3. **Rich connectivity** — Ethernet, WiFi/BT, USB 3.2, PCIe, MIPI, HDMI in a compact form factor
4. **Open source** — full schematic and PCB released under CERN-OHL-P

## Block Diagram

### Power
- **PMIC**: RK806S-5 (10-channel buck + 5 LDOs)
- **Input**: USB-C PD or barrel jack (TBD)
- **Rails**: VDD_CPU, VDD_GPU, VDD_LOGIC, VDD_DDR, VCC_3V3, VCC_1V8, etc.

### Memory & Storage
- **RAM**: LPDDR5 (up to 16 GB, 64-bit bus)
- **Primary storage**: UFS 3.1 (up to 128 GB)
- **Secondary**: eMMC (onboard), µSD card slot
- **Boot**: Configurable (UFS / eMMC / SD / USB)

### Connectivity
- **Ethernet**: Gigabit (RTL8211F PHY, LPJG0926HENL integrated magnetics jack)
- **WiFi/BT**: AP6256 (SDIO + UART + PCM)
- **USB**: 1× USB 3.2 OTG, 1× USB 3.2 Host (via hub), 4× USB 2.0
- **PCIe**: 1× PCIe 2.1 lane (M.2 or edge connector)

### Video & Camera
- **HDMI**: 4K output
- **MIPI DSI**: for additional display
- **MIPI CSI**: camera input
- **Onboard OLED**: 0.96" TFT (ST7735S, SPI) for status/debug

### Expansion
- **40-pin header**: RPi-compatible GPIO, I2C, SPI, UART, PWM, ADC
- **20-pin header**: additional IO / debug
- **SARADC**: SoC-integrated ADC channels

### Companion MCU
- **RP2350A**: dual-core Arm Cortex-M33 + Hazard3 RISC-V
- **Role**: IO co-processor — handles real-time GPIO, PWM, ADC, sensor polling
- **Interface**: SPI / UART / USB to RK3576
- **Benefits**: offloads time-critical tasks from Linux, enables bare-metal IO

## Schematic Sheets

| Sheet | Description |
|-------|-------------|
| `RK3576.kicad_sch` | Top-level hierarchical sheet |
| `rk3576_power.kicad_sch` | RK3576 power rails |
| `rk3576_emmc.kicad_sch` | eMMC interface |
| `rk3576_ufs.kicad_sch` | UFS 3.1 interface |
| `rk3576_usb2.kicad_sch` | USB 2.0 PHY |
| `rk3576_usb3_dp1.4.kicad_sch` | USB 3.2 / DP 1.4 |
| `rk3576_pcie.kicad_sch` | PCIe interface |
| `rk3576_usd_card.kicad_sch` | µSD card slot |
| `power.kicad_sch` | PMIC (RK806S-5) and power distribution |
| `lpddr5.kicad_sch` | LPDDR5 memory |
| `lpddr_phy.kicad_sch` | LPDDR5 PHY |
| `hdmi.kicad_sch` | HDMI output |
| `hdmi_phy.kicad_sch` | HDMI PHY |
| `mipi.kicad_sch` | MIPI DSI/CSI |
| `mipi_phy.kicad_sch` | MIPI PHY |
| `ethernet.kicad_sch` | Gigabit Ethernet |
| `wifi&blutooth.kicad_sch` | WiFi + Bluetooth (AP6256) |
| `usb3.2_host.kicad_sch` | USB 3.2 host |
| `usb3.2_otg.kicad_sch` | USB 3.2 OTG |
| `usb_hub.kicad_sch` | USB 2.0 hub |
| `usb_mcu.kicad_sch` | RP2350A USB interface |
| `usb_power.kicad_sch` | USB power delivery |
| `pcie.kicad_sch` | PCIe lane |
| `emmc.kicad_sch` | eMMC storage |
| `usd_card.kicad_sch` | µSD card |
| `rtc.kicad_sch` | RTC (RV-3028-C7) |
| `saradc.kicad_sch` | SARADC |
| `oled_screen.kicad_sch` | 0.96" OLED (ST7735S) |
| `gpio0.kicad_sch` – `gpio4.kicad_sch` | GPIO banks |
| `40pin_header.kicad_sch` | RPi-compatible header |
| `20pin_header.kicad_sch` | Secondary header |
| `connectors.kicad_sch` | Power / debug connectors |
| `digital.kicad_sch` | Digital IO |
| `comm.kicad_sch` | Communication interfaces |
| `mechanical.kicad_sch` | Board outline, mounting holes |
| `fixtures.kicad_sch` | Test points, fiducials |
| `ex_boards.kicad_sch` | External board interfaces |
| `RP2350A.kicad_sch` | RP2350A companion MCU |

## Repository Structure

```
Open-Source-SBC-Board/
├── RK3576 SBC.kicad_pro       # KiCad project file
├── RK3576 SBC.kicad_sch       # Top-level schematic
── RK3576 SBC.kicad_pcb       # PCB layout
├── *.kicad_sch                # Hierarchical schematic sheets
├── fp-lib-table               # Footprint library table
├── docs/                      # Documentation (renders, diagrams)
├── LICENSE                    # CERN-OHL-P v2
└── README.md                  # This file
```

## License

This hardware design is licensed under the **CERN Open Hardware Licence Version 2 — Permissive** (CERN-OHL-P v2).

You may use, modify, and distribute this design for any purpose, including commercial use, provided you retain the license notice and attribution.

See [LICENSE](LICENSE) for the full text.

## Acknowledgments

- [Rockchip](https://www.rock-chips.com/) for the RK3576 SoC
- [Raspberry Pi](https://www.raspberrypi.com/) for the RP2350A
- [KiCad](https://www.kicad.org/) for the EDA tools
- [CERN](https://ohwr.org/project/cernohl/) for the OHL license

## Contact

Questions, feedback, or contributions welcome. Open an issue or pull request on GitHub.

---

*This is a learning project. The design has not been fabricated or tested yet. Use at your own risk.*
