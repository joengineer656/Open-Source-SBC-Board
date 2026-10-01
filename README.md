# Open-Source-SBC-Board

A custom single-board computer built around the Rockchip RK3576 SoC, designed as a learning project to explore modern SBC architecture.

> **Status: Work in Progress — v2 current.** Schematic in progress. Not yet fabricated.

## Which version are you looking at?

**You are viewing `v2` (default, `main` branch).**

- **Want v1 (old)?** 
  - Switch branch: `archive/v1` in the GitHub branch dropdown, or
  - Download ZIP: [Releases → v1.0](https://github.com/joengineer656/Open-Source-SBC-Board/releases/tag/v1.0)
  - Clone specific: `git clone -b archive/v1 https://github.com/joengineer656/Open-Source-SBC-Board.git`
- **Want v2?** You're here. Or [Releases → v2.0](https://github.com/joengineer656/Open-Source-SBC-Board/releases/tag/v2.0) for ZIP.

| Version | Branch | Tag | Status |
|---------|--------|-----|--------|
| v2 (this page) | `main` | `v2.0` | active development, default view |
| v1 (archived) | `archive/v1` | `v1.0` | frozen, read-only |

## Overview

This board is a learning project exploring the design of a modern, feature-rich SBC. It combines a high-performance RK3576 application processor with an RP2350A companion MCU for IO offloading, targeting a balance between compute power and real-time IO capability.

## Key Specifications (v2)

| Feature | Specification |
|---------|--------------|
| **SoC** | Rockchip RK3576 |
| **Companion MCU** | Raspberry Pi RP2350A (IO co-processor) |
| **RAM** | Up to 16 GB LPDDR5 |
| **Storage** | UFS 3.1 (up to 128 GB), eMMC, µSD card |
| **Video Output** | HDMI, MIPI DSI |
| **Camera** | MIPI CSI |
| **Networking** | Gigabit Ethernet, WiFi + Bluetooth |
| **USB** | USB-A Host, USB-C SOC, USB-C MCU |
| **Expansion** | 40-pin RPi-compatible header, PCIe |
| **Display** | 0.96" TFT OLED (ST7735S, 80×160) |
| **RTC** | RV-3028-C7 class |
| **PMIC** | RK806S-5 |

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
│  (IO offload)   │   │  (Host ports)   │
└─────────────────┘   └─────────────────┘
         │
         ▼
┌─────────────────┐
│  40-pin Header  │
│  (RPi compat)   │
└─────────────────┘
```

## Open in KiCad

Project file: `RK3576 SBCv2.kicad_pro` (KiCad 10.0)
Top sheet: `block_diagrame.kicad_sch` → `RK3576.kicad_sch` (SOC) + peripherals.

## Schematic Sheets (v2)

| Sheet | Description |
|-------|-------------|
| `block_diagrame.kicad_sch` | Top-level block diagram |
| `RK3576.kicad_sch` | RK3576 SoC, decoupling,strapping |
| `RP2350A.kicad_sch` | RP2350A companion MCU |
| `Power.kicad_sch` | PMIC and power distribution |
| `LPDDR5.kicad_sch` | LPDDR5 memory |
| `Ethernet.kicad_sch` | Gigabit Ethernet |
| `Wireless.kicad_sch` | WiFi + Bluetooth |
| `HDMI.kicad_sch` | HDMI output |
| `MIPI.kicad_sch` | MIPI DSI/CSI |
| `PCIe.kicad_sch` | PCIe lane |
| `USBA_Host.kicad_sch` | USB-A host ports |
| `USBC_SOC.kicad_sch` | USB-C to SoC |
| `USBC_MCU.kicad_sch` | USB-C to MCU (placeholder) |
| `uSD_Card.kicad_sch` | µSD card slot |
| `eMMC/UFS.kicad_sch` + `UFS/eMMC.kicad_sch` | eMMC / UFS storage |
| `RTC.kicad_sch` | RTC |
| `oled_screen.kicad_sch` | 0.96" OLED |
| `40pin_GPIO.kicad_sch` | RPi-compatible header |
| `connectors.kicad_sch`, `dc_power_soket.kicad_sch` | Power / debug connectors |
| `Fixtures.kicad_sch` | Test points, fiducials |
| `digital.kicad_sch`, `power.kicad_sch`, `mechanical.kicad_sch`, `output.kicad_sch`, `eMMC_UFS.kicad_sch`, `fixtures.kicad_sch`, `directive_labels.kicad_sch` | Placeholders / in progress |

> Note: `eMMC/UFS.kicad_sch` and `UFS/eMMC.kicad_sch` names are swapped upstream — kept as-is to avoid breaking sheet references. Same for `fixtures.kicad_sch` vs `Fixtures.kicad_sch` and `power.kicad_sch` vs `Power.kicad_sch` (case-sensitive duplicates on Linux).

## Repository Structure (v2 on `main`)

```
Open-Source-SBC-Board/ (main = v2)
├── RK3576 SBCv2.kicad_pro       # KiCad project file
├── RK3576 SBCv2.kicad_sch       # Project root sheet
├── RK3576 SBCv2.kicad_pcb       # PCB layout (in progress)
├── RK3576 SBCv2.kicad_prl
├── RK3576 SBCv2.kicad_dru
├── *.kicad_sch                  # Hierarchical schematic sheets
├── eMMC/ UFS/                   # Storage sheets
├── LICENSE                      # CERN-OHL-P v2
├── README.md                     # This file
└── .gitignore                    # KiCad ignores (*.bak, .lck, .history/)
```

v1 lives only on branch `archive/v1` + tag `v1.0`, not in this tree.

## Design Goals

1. **Learn modern SBC design** — power delivery, high-speed interfaces (LPDDR5, UFS, PCIe, USB 3.2), mixed-signal layout
2. **Real-time IO capability** — RP2350A handles time-critical tasks independently of the Linux-running SoC
3. **Rich connectivity** — Ethernet, WiFi/BT, USB, PCIe, MIPI, HDMI in a compact form factor
4. **Open source** — full schematic and PCB released under CERN-OHL-P

## For maintainers: how versions are stored

```bash
# v1 frozen
git branch archive/v1   # from old main
git tag v1.0 archive/v1

# v2 is main
git tag v2.0 main

# push all (needs auth)
git push origin archive/v1 v1.0 main v2.0
gh release create v1.0 --target archive/v1 --title "v1.0 - archived" --notes "Frozen v1. See branch archive/v1."
gh release create v2.0 --target main --title "v2.0 - current" --notes "Current v2. Default branch main."
```

Users get v2 by default, v1 via branch dropdown or Releases ZIP — i.e. “2 repos in 1 repo”.

## License

This hardware design is licensed under the **CERN Open Hardware Licence Version 2 — Permissive** (CERN-OHL-P v2).

See [LICENSE](LICENSE) for the full text.

## Acknowledgments

- [Rockchip](https://www.rock-chips.com/) for the RK3576 SoC
- [Raspberry Pi](https://www.raspberrypi.com/) for the RP2350A
- [KiCad](https://www.kicad.org/) for the EDA tools
- [CERN](https://ohwr.org/project/cernohl/) for the OHL license

---

*This is a learning project. The design has not been fabricated or tested yet. Use at your own risk.*
