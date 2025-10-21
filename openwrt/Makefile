include $(TOPDIR)/rules.mk

PKG_NAME:=uwe5622
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/guoxiaoqiao/uwe5622.git
PKG_SOURCE_VERSION:=63e33ac4cabf831a3fd45fbf35d0e4c4683e680e
PKG_MIRROR_HASH:=skip

PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/uwe5622
  SUBMENU:=Wireless Drivers
  TITLE:=Unisoc UWE5622 Wi-Fi/Bluetooth support
  DEPENDS:=+kmod-mac80211 +kmod-cfg80211 +kmod-crypto-hash +kmod-crypto-manager +kmod-lib-crc32c +kmod-lib-crc8 +bluez-daemon
  FILES:= \
	$(PKG_BUILD_DIR)/unisocwifi/sprdwl_ng.ko \
	$(PKG_BUILD_DIR)/unisocwcn/uwe5622_bsp_sdio.ko \
	$(PKG_BUILD_DIR)/tty-sdio/sprdbt_tty.ko
  AUTOLOAD:=$(call AutoProbe,uwe5622_bsp_sdio sprdbt_tty sprdwl_ng)
  PROVIDES:=kmod-uwe5622
endef

define KernelPackage/uwe5622/description
 Kernel modules for the Unisoc UWE5622 (AW859A) combo chip, providing
 SDIO Wi-Fi (sprdwl_ng) and Bluetooth (sprdbt_tty) support as used on
 boards such as the Orange Pi Zero3.
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(SED) 's/struct device_node \*np = NULL;/struct device_node *np __maybe_unused = NULL;/' \
		$(PKG_BUILD_DIR)/unisocwcn/platform/wcn_boot.c
	$(SED) 's/static void marlin_remove/static int marlin_remove/' \
		$(PKG_BUILD_DIR)/unisocwcn/platform/wcn_boot.c
	$(SED) '/WCN_INFO("marlin_remove ok!\\n");/{n;/./!{n;s/^[ \t]*return;/\treturn 0;/;}}' \
		$(PKG_BUILD_DIR)/unisocwcn/platform/wcn_boot.c
	$(SED) 's/static struct ieee80211_supported_band sprdwl_band_2ghz = {/static struct ieee80211_supported_band sprdwl_band_2ghz = {\n\t.band = NL80211_BAND_2GHZ,/' \
		$(PKG_BUILD_DIR)/unisocwifi/cfg80211.c
	$(SED) 's/static struct ieee80211_supported_band sprdwl_band_5ghz = {/static struct ieee80211_supported_band sprdwl_band_5ghz = {\n\t.band = NL80211_BAND_5GHZ,/' \
		$(PKG_BUILD_DIR)/unisocwifi/cfg80211.c
	$(SED) 's/memcpy(dev->dev_addr, sa->sa_data, ETH_ALEN);/eth_hw_addr_set(dev, sa->sa_data);/' \
		$(PKG_BUILD_DIR)/unisocwifi/main.c
	$(SED) 's/memcpy(dev->dev_addr, vif->mac, ETH_ALEN);/eth_hw_addr_set(dev, vif->mac);/' \
		$(PKG_BUILD_DIR)/unisocwifi/main.c
	$(SED) 's/addr\[4\] \^= 0x80;/addr[4] ^= 0x80;\n\t\tfallthrough;/' \
		$(PKG_BUILD_DIR)/unisocwifi/main.c
	$(SED) 's/mac = vif->ndev->dev_addr;/mac = (u8 *)vif->ndev->dev_addr;/' \
		$(PKG_BUILD_DIR)/unisocwifi/cfg80211.c
	if ! grep -q 'Wno-error=vla' $(PKG_BUILD_DIR)/unisocwifi/Makefile; then \
		echo 'ccflags-y += -Wno-error=vla -Wno-attribute-warning -Wno-error=unused-variable -Wno-error=enum-conversion -Wno-error=implicit-fallthrough' >> $(PKG_BUILD_DIR)/unisocwifi/Makefile; \
	fi
	$(SED) 's/static void sprdwl_remove/static int sprdwl_remove/' \
		$(PKG_BUILD_DIR)/unisocwifi/wl_core.c
	$(SED) '/wl_info("%s\\n", __func__);/{n;/./!{n;s/^[ \t]*return;/\treturn 0;/;}}' \
		$(PKG_BUILD_DIR)/unisocwifi/wl_core.c
	$(SED) 's/static void mtty_remove/static int mtty_remove/' \
		$(PKG_BUILD_DIR)/tty-sdio/tty.c
	$(SED) '/bluesleep_exit();/{n;/./!{n;s/^[ \t]*return;/\treturn 0;/;}}' \
		$(PKG_BUILD_DIR)/tty-sdio/tty.c
	$(SED) 's/rd->state = PREAMBLE;/rd->state = PREAMBLE;\n\t\tfallthrough;/' \
		$(PKG_BUILD_DIR)/tty-sdio/alignment/sitm.c
	$(SED) 's/cfg80211_get_bss(/__sprd_stub_cfg80211_get_bss(/g' \
		$(PKG_BUILD_DIR)/unisocwifi/cfg80211.c \
		$(PKG_BUILD_DIR)/unisocwifi/rtt.c
	$(SED) '/#include "rx_msg.h"/a\\static inline struct cfg80211_bss * __sprd_stub_cfg80211_get_bss(struct wiphy *wiphy, struct ieee80211_channel *channel, const u8 *bssid, const u8 *ssid, size_t ssid_len, enum ieee80211_bss_type bss_type, enum ieee80211_privacy privacy) { return NULL; }' \
		$(PKG_BUILD_DIR)/unisocwifi/cfg80211.c
	$(SED) '/#include "rtt.h"/a\\static inline struct cfg80211_bss * __sprd_stub_cfg80211_get_bss(struct wiphy *wiphy, struct ieee80211_channel *channel, const u8 *bssid, const u8 *ssid, size_t ssid_len, enum ieee80211_bss_type bss_type, enum ieee80211_privacy privacy) { return NULL; }' \
		$(PKG_BUILD_DIR)/unisocwifi/rtt.c
	$(SED) '/#include <linux\/rtc.h>/a\#include <linux/time64.h>' \
		$(PKG_BUILD_DIR)/tty-sdio/dump.h
	$(SED) 's/#define rtc_time_to_tm rtc_time64_to_tm/#define rtc_time_to_tm(secs, rtc_tm_ptr) time64_to_tm((secs), 0, (struct tm *)(rtc_tm_ptr))/' \
		$(PKG_BUILD_DIR)/tty-sdio/dump.h
	$(SED) '/#include "dump.h"/a\\#include <linux/time64.h>\n#ifndef CONFIG_RTC_LIB\nvoid rtc_time64_to_tm(time64_t t, struct rtc_time *res) { time64_to_tm(t, 0, (struct tm *)res); }\n#endif' \
		$(PKG_BUILD_DIR)/tty-sdio/dump.c
endef

define Build/Configure
endef

define Build/Compile
	+$(KERNEL_MAKE) \
		M="$(PKG_BUILD_DIR)" \
		UNISOC_WIFI_MAC_FILE="/lib/firmware/uwe5622/wifi_mac.txt" \
		UNISOC_FW_PATH_CONFIG="/lib/firmware/uwe5622/" \
		UNISOC_WIFI_CUS_CONFIG="/lib/firmware/uwe5622" \
		CONFIG_SPARD_WLAN_SUPPORT=m \
		CONFIG_AW_WIFI_DEVICE_UWE5622=y \
		CONFIG_WLAN_UWE5622=m \
		CONFIG_TTY_OVERY_SDIO=m \
		modules
endef

define KernelPackage/uwe5622/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/uwe5622-bt.init $(1)/etc/init.d/uwe5622-bt
endef

$(eval $(call KernelPackage,uwe5622))
