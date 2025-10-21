# UWE5622（AW859A）驱动

[English Version](README.md)

## 概述

该仓库提供紫光展锐 UWE5622（又名 AW859A）无线模组的内核外驱动。代码最初来源于 Linux 5.15 版本的厂商发布，在此基础上合入了 Armbian 针对新内核的改动。

## 内核兼容性

- 已在 Orange Pi Zero 3 开发板上验证。
- Linux 6.6 支持来源于 Armbian v25.5.1（H616/H618 补丁集）。
- Linux 6.12 更新同样来自 Armbian v25.5.1（SDIO 与 cfg80211/mac80211 接口调整、柔性数组修复等）。

## 快速上手

1. 在 `Kconfig` 中启用所需项（`CONFIG_AW_WIFI_DEVICE_UWE5622`、`CONFIG_WLAN_UWE5622`、`CONFIG_TTY_OVERY_SDIO`）。
2. 基于当前内核头文件编译模块：
   ```bash
   make -C /lib/modules/$(uname -r)/build M=$(pwd) modules
   ```
3. 安装生成的模块，并确认固件已放置在 `/lib/firmware/uwe5622/`。

如需更多部署细节，可在英文版中查看指引并与本地环境对照。

## OpenWrt 打包

- `openwrt/` 目录镜像了某 OpenWrt 24.10 下游树的打包方案（提交 78798e8973），**不适用于**更早版本或 master 分支。
- `openwrt/Makefile` 仅针对 OpenWrt 24.10，内部的 sed 调整依赖该版本自带的 backports；换用其他分支会失败。
- 若需在 OpenWrt 编译环境中使用，请将本目录复制到 `package/kernel/uwe5622/`，然后执行 `make package/kernel/uwe5622/{clean,compile} V=s`。

## 运行情况（OpenWrt 24.10）

- 测试环境：Orange Pi Zero 3，内核 6.6.110 的 OpenWrt 24.10 固件。
- `sprdwl_ng` 在注册 wiphy 阶段出现两次 `ieee80211_set_bitrate_flags` 警告，提示 2.4/5 GHz 频段能力描述仍需完善。
- 蓝牙 HCI Reset（opcode 0x080f）返回 `-22`，说明当前初始化脚本仍需增加协议回退。
- 在系统 shell 中运行 `iwinfo` 无输出且无法中断，显示当前 Wi-Fi 协议栈仍不稳定。
