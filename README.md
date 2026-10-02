# Open-Source-SBC-Board

A custom single-board computer built around the Rockchip RK3576 SoC, designed as a learning project to explore modern SBC architecture.

> **Status: Work in Progress — v2 current.** Schematic in progress. Not yet fabricated. V2 schematics are not final – expect minor errors.

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

V2 is a cleaned-up / upgraded version of the V1 design (ref: [devmeb0/RK3576-SBC](https://github.com/devmeb0/RK3576-SBC)). Main V2 deltas: **second Gigabit Ethernet port, M.2 Key-E socket for wireless modules (no FCC cert needed for the base board), onboard CR2032 RTC backup battery (not external), schematic cleanup + notes throughout.**

## Key Specifications (v2)

| Feature | Specification |
|---------|--------------|
| **SoC** | Rockchip RK3576 – 4x Cortex-A72 @ 2.2GHz + 4x Cortex-A53 @ 1.8GHz, Mali-G52 MC3, 6 TOPS NPU |
| **Companion MCU** | Raspberry Pi RP2350A (IO co-processor, USB-C, SWD/JTAG) |
| **RAM** | 32-bit LPDDR5 (Micron MT62F1G32D4DR-031) |
| **Storage** | UFS 2.0, eMMC 5.1 modular, µSD card, SPI flash (W25Q128 for MCU) |
| **Video Output** | HDMI 2.1 / eDP 1.3, USB-C DP1.4 Alt-Mode, 4-lane MIPI DSI + 0.96" OLED header |
| **Camera** | MIPI CSI 4-lane (or split 2+2) |
| **Networking** | Dual Gigabit Ethernet (2x RTL8211F, PoE optional), M.2 Key-E 67-pin for WiFi/BT |
| **USB** | 4x USB 3.2 Host (RTS5411 hub), 1x USB-C OTG/PD (FUSB302), 1x USB-C for MCU |
| **Expansion** | 40-pin RPi-compatible header, PCIe 2.1 x1 + SATA3 via FPC |
| **Display** | 0.96" OLED header (8-pin) |
| **RTC** | RV-3028-C7 + FRAM, onboard CR2032 holder (BAT-HLD-001) |
| **PMIC** | RK806S-5 + SY8388 / SY8089 DC/DCs |
| **Power In** | Barrel jack, USB-C 5V, USB-C PD, PoE option, 40-pin header |

## Features (v2)

**1. SoC – Rockchip RK3576 (FCCSP698)**
Octa-core big.LITTLE, Cortex-M0 for user code, Mali-G52 MC3 (OpenGL ES 3.2 / Vulkan 1.2 / OpenCL 2.1), 6 TOPS NPU (INT4/8/16, FP16/BF16/TF32), 16MP ISP with HDR/3DNR, 8K@30 / 4K@120 decode, 4K@60 encode. 2x RGMII, PCIe2.1/SATA3/USB3 combo lanes, 2x CAN-FD on-chip. OS: Linux / Android 14 / Yocto / Buildroot.

**2. Memory – 32-bit LPDDR5**
`LPDDR5.kicad_sch`: Micron MT62F1G32D4DR-031, 32-bit dual-channel layout per RK3576 reference.

**3. Storage – eMMC + UFS + microSD + SPI**
- `eMMC/UFS.kicad_sch` + `UFS/eMMC.kicad_sch`: eMMC 5.1 modular footprint + UFS 2.0 HS-G3.
- `uSD_Card.kicad_sch`: push-push microSD with AP2553 load-switch power + TPD4E ESD.
- `RP2350A.kicad_sch`: 2x W25Q128JVS SPI flash for the co-MCU.

**4. Display – 3x concurrent**
- `HDMI.kicad_sch`: HDMI 2.1 / eDP 1.3 combo, TPD4E05U06 ESD, TXB0104 translators, AP2553 5V switch.
- `USBC_SOC.kicad_sch`: USB-C Alt-Mode DP1.4 + USB3 combo, FUSB302 PD controller, TPS22953 switches.
- `MIPI.kicad_sch`: 4-lane MIPI DSI TX via 30-pin FPC, TXS0108 level-shifted.
- `oled_screen.kicad_sch`: 0.96" OLED 8-pin header for status/debug.

**5. Network – Dual GbE + M.2 Key-E (V2 highlight)**
- `Ethernet.kicad_sch`: 2x RTL8211F-CG PHYs (U25/U26, 2x 25 MHz crystals), LPJG0926HENL magjacks, PoE input headers (PoE optional).
- `Wireless.kicad_sch`: M.2 Key-E 67-pin socket APCI0108-P001A (LCSC C7498141), 0.5 mm right-angle, for off-the-shelf WiFi/BT modules – replaces soldered wireless, so the base board itself needs no FCC certification.

**6. USB**
- `USBA_Host.kicad_sch`: 4x USB3.2 Host via 2x RTS5411S-GR hubs, SY6280 current-limit per port, TPD4E ESD.
- `USBC_SOC.kicad_sch`: 1x USB-C OTG/Host Alt-Mode with PD negotiation – can also be power input.
- `RP2350A.kicad_sch`: separate USB-C 2.0 16P for the MCU.

**7. Camera**
`MIPI.kicad_sch`: MIPI CSI RX – 4-lane or split 2+2. Feeds RK3576 triple CSI + 16MP ISP.

**8. Power – RK806S-5 + discretes**
`Power.kicad_sch`: RK806S-5 PMIC, 2x SY8388ARHC + SY8089AAAC DC/DCs, AO3401 FETs, SMBJ TVS. Inputs: barrel-jack, USB-C 5V/3A-5A with UVLO/OVLO, USB-C PD in, PoE option, or 40-pin header 5V.

**9. RTC – onboard battery (V2 change)**
`RTC.kicad_sch`: RV-3028-C7 RTC + FM24C64/M24C64 FRAM/EEPROM, AO3401 OR-ing, fuse + SMBJ. CR2032 coin holder Linx BAT-HLD-001 **on-board** – V1 used an external JST battery.

**10. Expansion**
- `PCIe.kicad_sch` + `connectors.kicad_sch`: PCIe 2.1 x1 + SATA3 via TE 1-2199230-5 FPCs.
- `40pin_GPIO.kicad_sch`: Raspberry-Pi-compatible 40-pin header, 3x AP2553 load switches, SMBJ ESD: 28x GPIO / 13x PWM, 5x UART, 5x I2C, 1x I3C, 2x SPI, CAN, PDM, SDIO, 12-bit SAR-ADC 1MS/s, 3.3V out / 5V in-out.

**11. Co-MCU – RP2350A**
`RP2350A.kicad_sch`: RP2350A with ETA7014 regulators, W25Q128 flash, USB-C, SWD/JTAG header, tactile buttons. For real-time IO / housekeeping offload.

**12. Bring-up / mechanical**
`Fixtures.kicad_sch`: mounting holes, fiducials, test-point probes, UART/SWD debug header, boot DIP, maskrom + power tactiles, dual power/activity LEDs, active fan header.

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
| `RK3576.kicad_sch` | RK3576 SoC, decoupling, strapping |
| `RP2350A.kicad_sch` | RP2350A companion MCU, USB-C, SWD |
| `Power.kicad_sch` | RK806S-5 PMIC and power distribution |
| `LPDDR5.kicad_sch` | LPDDR5 memory (Micron MT62F) |
| `Ethernet.kicad_sch` | Dual Gigabit Ethernet (2x RTL8211F, PoE) |
| `Wireless.kicad_sch` | M.2 Key-E 67-pin WiFi/BT socket |
| `HDMI.kicad_sch` | HDMI 2.1 output |
| `MIPI.kicad_sch` | MIPI DSI/CSI via FPC |
| `PCIe.kicad_sch` | PCIe 2.1 / SATA via FPC |
| `USBA_Host.kicad_sch` | 4x USB-A host (RTS5411 hub) |
| `USBC_SOC.kicad_sch` | USB-C to SoC, PD (FUSB302) |
| `uSD_Card.kicad_sch` | µSD card slot |
| `eMMC/UFS.kicad_sch` + `UFS/eMMC.kicad_sch` | eMMC / UFS storage |
| `RTC.kicad_sch` | RV-3028 RTC + onboard CR2032 |
| `oled_screen.kicad_sch` | 0.96" OLED header |
| `40pin_GPIO.kicad_sch` | RPi-compatible header |
| `connectors.kicad_sch`, `dc_power_soket.kicad_sch` | Power / debug connectors |
| `Fixtures.kicad_sch` | Test points, fiducials |
| `directive_labels.kicad_sch` | Netclass directives |

> Note: `eMMC/UFS.kicad_sch` and `UFS/eMMC.kicad_sch` names are swapped upstream — kept as-is to avoid breaking sheet references. Excluded from repo (per `.gitignore`): `.history/`, `Resources/`, `*.bak`, `*.lck`, `project.kicad_dru`, `.hs_rules.json`, lowercase dup placeholders (`fixtures`/`power`), empty orphans (`digital`, `mechanical`, `output`, `USBC_MCU`, `eMMC_UFS`).

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
3. **Rich connectivity** — Dual Ethernet, M.2 Key-E WiFi/BT, USB, PCIe, MIPI, HDMI in a compact form factor
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
- [devmeb0/RK3576-SBC](https://github.com/devmeb0/RK3576-SBC) V1 reference design

---

*This is a learning project. The design has not been fabricated or tested yet. Use at your own risk.*
