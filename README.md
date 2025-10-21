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
