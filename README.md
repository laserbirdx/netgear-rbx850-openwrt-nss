# Netgear Orbi RBx850 (RBR850 & RBS850) — OpenWrt EDMA-NSS Firmware (WIP)

![Netgear Orbi OpenWrt Banner](./assets/banner.jpg)

<p align="center">
  <img src="https://img.shields.io/badge/Status-WIP%20(1%20NSS%20Core%20Active)-orange" alt="Status: WIP (1 NSS Core Active)" />
  <img src="https://img.shields.io/badge/OpenWrt-SNAPSHOT%20(Linux%206.18.55)-00B5E2?logo=openwrt&logoColor=white" alt="OpenWrt SNAPSHOT (Linux 6.18.55)" />
  <img src="https://img.shields.io/badge/Target-qualcommax%2Fipq807x-informational" alt="Target: qualcommax/ipq807x" />
  <img src="https://img.shields.io/badge/NSS%20Firmware-12.5--210--HK.R-success" alt="NSS Firmware: 12.5-210-HK.R" />
  <a href="https://github.com/laserbirdx/netgear-rbr850-openwrt"><img src="https://img.shields.io/badge/Guide-RBR850%20Router-blue" alt="RBR850 Guide" /></a>
  <a href="https://github.com/laserbirdx/netgear-rbs850-openwrt"><img src="https://img.shields.io/badge/Guide-RBS850%20Satellite-blue" alt="RBS850 Guide" /></a>
  <a href="./LICENSE"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License: MIT" /></a>
</p>

Experimental **OpenWrt SNAPSHOT (Linux 6.18.55)** firmware builds with **Qualcomm EDMA-NSS hardware offloading** (`qca-ppe-nss`, `qca-nss-drv`, `qca-nss-ecm`, `ath11k` Wi-Fi NSS offload, and `sqm-scripts-nss`) for the **Netgear Orbi RBR850 Router** and **Netgear Orbi RBS850 Satellite** (`RBK850` / `RBK852` / `RBK853` / `RBK854` AX6000 Tri-Band Wi-Fi 6 series).

> ⚠️ **Work in Progress (WIP) — Single NSS Core Active (`nss0`)**
> This build is currently a **Work in Progress (WIP)**:
> - **1 NSS core (`nss0` / Core 0 @ `1.6896 GHz`) is active** and handling wired (`lan1`–`lan4`, `wan`), bridge, PPPoE, ECM, and `ath11k` Wi-Fi (`wifili`) hardware offloading.
> - The second UBI32 NSS core (`nss1` / Core 1) is currently marked `disabled` in the Device Tree (DTS) while dual-core stability on the `edma-nss` rework branch is being finalized.
> - Flash at your own risk. Always keep [`nmrpflash`](https://github.com/jclehner/nmrpflash) ready for recovery.

---

## 1. Build & NSS Architecture Details

| Parameter | Details |
| :--- | :--- |
| **Supported Devices** | **Netgear Orbi RBR850** (Router) & **Netgear Orbi RBS850** (Satellite) |
| **SoC & Memory** | Qualcomm IPQ8074A (4x ARM Cortex-A53 @ 2.2 GHz) + 1 GiB DDR4 RAM |
| **Target / Subtarget** | `qualcommax/ipq807x` (`aarch64_cortex-a53`) |
| **OpenWrt Revision** | `OpenWrt SNAPSHOT r36880+281-bb6df54d9d` |
| **Linux Kernel** | `6.18.55` |
| **OpenWrt Source Tree** | [`kuncy7/openwrt-nss-edma`](https://github.com/kuncy7/openwrt-nss-edma) (`julius-archive/nss-edma-rework` @ `65a94490a5`) |
| **NSS Packages Feed** | [`kuncy7/nss-packages`](https://github.com/kuncy7/nss-packages) (`julius-archive/edma-nss`) |
| **NSS Firmware** | `NSS.FW.12.5-210-HK.R` (`CONFIG_NSS_FIRMWARE_VERSION_12_5=y`) |
| **NSS Memory Profile** | `CONFIG_NSS_MEM_PROFILE_HIGH=y` (1 GiB RAM profile) |
| **Active NSS Cores** | **1 Core (`nss0` / Core 0 @ 1.6896 GHz)** — `nss1` (Core 1) disabled in DTS *(WIP)* |
| **Web UI** | Pre-installed **LuCI** (`luci`, `luci-app-nss`, `luci-app-firewall`, `luci-app-package-manager`) |

### Pre-Installed NSS & Offload Packages

- **Core NSS Datapath:** `kmod-qca-ppe-nss`, `kmod-qca-nss-drv`, `kmod-qca-nss-ecm`, `nss-firmware-ipq807x`
- **Protocol & Bridge Managers:**
  - `kmod-qca-nss-drv-bridge-mgr` (L2 bridge hardware acceleration)
  - `kmod-qca-nss-drv-pppoe` (PPPoE session hardware acceleration)
  - `kmod-qca-nss-drv-igs` (IGMP snooping offload)
  - `kmod-qca-nss-drv-qdisc` + `sqm-scripts-nss` (`nssfq_codel` / `nsstbl` hardware QoS / SQM)
- **Wi-Fi Offload:** `kmod-ath11k` built with `ATH11K_NSS_SUPPORT` (`wifili` NSS radio offload across all 3 tri-band radios)
- **Diagnostics & UI:** `nss-tools`, `luci-app-nss`

---

## 2. Firmware Downloads & SHA256 Checksums

All compiled images are available on the **[GitHub Releases](../../releases/latest)** page.

### Netgear Orbi RBR850 (Router)

| Image Type | Filename | SHA256 Checksum |
| :--- | :--- | :--- |
| **Sysupgrade** *(from OpenWrt)* | `openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-sysupgrade.bin` | `f3f81c10a57a9daa2424c0ee8cd10e9601d85569c0765071c7eeac7295fde67f` |
| **Factory NMRP** *(from Stock)* | `openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-factory.chk` | `8f47b5884a870a3946930c1357647b9c0d67dea299e7cb08a3c30c71992226fa` |
| **Factory UBI** | `openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-factory.ubi` | `3edccfcbbd3f742ae1c18c226d7b53f8518d1c4d1e0e26b00dea8359c8388b96` |
| **Initramfs** *(TFTP / RAM boot)* | `openwrt-qualcommax-ipq807x-netgear_rbr850-initramfs-uImage.itb` | `9a5e7b79257e7589f8f88365712a1b5c8a96182c96b6def06cd0ab32aa102cfd` |

### Netgear Orbi RBS850 (Satellite)

| Image Type | Filename | SHA256 Checksum |
| :--- | :--- | :--- |
| **Sysupgrade** *(from OpenWrt)* | `openwrt-qualcommax-ipq807x-netgear_rbs850-squashfs-sysupgrade.bin` | `7ad8043432a6c5daafab525a64978228b24f69c748e75138a7046f1867de0185` |
| **Factory NMRP** *(from Stock)* | `openwrt-qualcommax-ipq807x-netgear_rbs850-squashfs-factory.chk` | `2ff0dc27c7a9bc512d6478d63eb98e750bdf1756461b56d69717aa0e786175fd` |
| **Factory UBI** | `openwrt-qualcommax-ipq807x-netgear_rbs850-squashfs-factory.ubi` | `9d2175790625629e950758c7f92a0ff238ede435ae5296e85ac47693087deaa1` |
| **Initramfs** *(TFTP / RAM boot)* | `openwrt-qualcommax-ipq807x-netgear_rbs850-initramfs-uImage.itb` | `e06908277f1e5e699f4261fe19a3f80dd1b879381aa99ceb815a5a97859e76dc` |

Full package list: [`openwrt-qualcommax-ipq807x.manifest`](./openwrt-qualcommax-ipq807x.manifest) · Checksums file: [`sha256sums`](./sha256sums)

---

## 3. Flashing Instructions

For detailed first-time installation instructions from Netgear stock firmware (including `nmrpflash` usage and RBS850 satellite setup), refer to the companion guides:
- **[laserbirdx/netgear-rbr850-openwrt](https://github.com/laserbirdx/netgear-rbr850-openwrt)** — Netgear Orbi RBR850 Router Installation Guide
- **[laserbirdx/netgear-rbs850-openwrt](https://github.com/laserbirdx/netgear-rbs850-openwrt)** — Netgear Orbi RBS850 Satellite Installation & Standalone Router Conversion Guide

### Option A: Upgrading from Existing OpenWrt (`sysupgrade`)

If your RBR850 or RBS850 is already running mainline OpenWrt, upload the corresponding `*-squashfs-sysupgrade.bin` to `/tmp/` and flash cleanly (`-n` is recommended when switching between standard DSA/EDMA and EDMA-NSS builds):

```bash
# Example for RBR850 Router:
scp -O openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-sysupgrade.bin root@192.168.1.1:/tmp/
ssh root@192.168.1.1 "sysupgrade -n /tmp/openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-sysupgrade.bin"
```

### Option B: Flashing from Stock Firmware via `nmrpflash` (`factory.chk`)

1. Connect your computer via Ethernet to one of the **LAN ports** (`lan1`–`lan4`).
2. Power off the device and start `nmrpflash`:
   ```bash
   # For RBR850:
   sudo nmrpflash -i <interface> -f openwrt-qualcommax-ipq807x-netgear_rbr850-squashfs-factory.chk

   # For RBS850:
   sudo nmrpflash -i <interface> -f openwrt-qualcommax-ipq807x-netgear_rbs850-squashfs-factory.chk
   ```
3. Power on the unit and wait for the flash and automatic reboot to complete.

---

## 4. Verification on Hardware (RBR850)

Tested on **Netgear Orbi RBR850** (`192.168.1.1`):

- **NSS Datapath (`nss-status`):** Reports `OFFLOAD ACTIVE`
- **Wired Ports:** `lan1`, `lan2`, `lan3`, `lan4` (1 GbE via `QCA8075`) and `wan` (2.5 GbE via `QCA8081`) all report `up 1` on the NSS datapath
- **Kernel Offload Modules:**
  - `ECM: loaded=1`
  - `bridge-mgr=1`
  - `pppoe-mgr=1`
- **Wireless Offload:** `Wi-Fi: NSS offload active (wifili)` across the `ath11k` radios

You can inspect live NSS statistics via SSH using `nss-status` or via LuCI (**Status → NSS** / **Status → Overview**).

---

## 5. Known Limitations & Roadmap (WIP)

- [x] Single-core NSS (`nss0` @ 1.6896 GHz) boot and firmware load (`NSS.FW.12.5-210-HK.R`)
- [x] PPE-NSS & EDMA-NSS wired port offload (`lan1`–`lan4` + 2.5G `wan`)
- [x] ECM connection tracking, Bridge Manager, PPPoE offload, and NSS SQM (`sqm-scripts-nss`)
- [x] `ath11k` Wi-Fi 6 NSS hardware offload (`wifili`)
- [ ] Enable and stabilize second NSS core (`nss1` / Core 1) in DTS

---

## Credits

- **[kuncy7 / julius-archive](https://github.com/kuncy7/openwrt-nss-edma)** for the `nss-edma-rework` OpenWrt tree and `edma-nss` package feed.
- **[qosmio](https://github.com/qosmio)** and the OpenWrt `qualcommax` NSS community for upstream IPQ807x NSS driver maintenance.

## License

Documentation and build metadata in this repository are released under the [MIT License](./LICENSE). OpenWrt and Linux kernel components are licensed under [GPL-2.0](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html).
