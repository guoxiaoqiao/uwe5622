# UWE5622 (AW859A) Driver

[中文版本](README.zh.md)

## Overview

Out-of-tree driver stack for the Unisoc UWE5622 wireless combo module (marketed as AW859A). The code base originated from the Linux 5.15 vendor release and has since been updated with Armbian enhancements for newer kernels.

## Kernel Compatibility

- Verified on Orange Pi Zero 3 boards.
- Linux 6.6 support imported from Armbian v25.5.1 (H616/H618 patch stack).
- Linux 6.12 updates pulled from the same Armbian v25.5.1 release (SDIO, cfg80211/mac80211 API adjustments, flex-array fixes).

## Quick Start

1. Enable the desired components in `Kconfig` (`CONFIG_AW_WIFI_DEVICE_UWE5622`, `CONFIG_WLAN_UWE5622`, `CONFIG_TTY_OVERY_SDIO`).
2. Build as an external module against your kernel headers:
   ```bash
   make -C /lib/modules/$(uname -r)/build M=$(pwd) modules
   ```
3. Install the resulting modules and ensure firmware is available under `/lib/firmware/uwe5622/`.

See the Chinese version for a translated guide and additional deployment notes.

## OpenWrt Packaging

- The `openwrt/` directory mirrors the packaging used in an OpenWrt 24.10 downstream tree (commit 78798e8973) and is **not** intended for earlier releases or master snapshots.
- `openwrt/Makefile` is tailored to OpenWrt 24.10; its sed-based adjustments depend on that release's backports layout and will fail on other branches.
- To build inside an OpenWrt buildroot, copy the directory to `package/kernel/uwe5622/` and run `make package/kernel/uwe5622/{clean,compile} V=s`.

## Runtime Notes (OpenWrt 24.10)

- Test environment: OpenWrt 24.10 image (kernel 6.6.110) on Orange Pi Zero 3.
- `sprdwl_ng` triggers two `ieee80211_set_bitrate_flags` warnings during wiphy registration, hinting at incomplete 2.4/5 GHz capability metadata.
- Bluetooth HCI reset (`opcode 0x080f`) returns `-22`, so the bundled init script still needs protocol fallbacks for this hardware.
- Running `iwinfo` from the system shell hangs without output, indicating the Wi-Fi stack remains unstable on this build.
