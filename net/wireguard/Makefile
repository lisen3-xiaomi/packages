#
# Copyright (C) 2016-2017 Jason A. Donenfeld <Jason@zx2c4.com>
# Copyright (C) 2016 Baptiste Jonglez <openwrt@bitsofnetworks.org>
# Copyright (C) 2016-2017 Dan Luedtke <mail@danrl.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=wireguard

PKG_VERSION:=0.0.20170907
PKG_RELEASE:=1

PKG_SOURCE:=WireGuard-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://git.zx2c4.com/WireGuard/snapshot/
PKG_HASH:=a1ee12d60662607e4c5a19f84b5115e56f083e2600053882e161537f12d963fd

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_BUILD_DIR:=$(BUILD_DIR)/WireGuard-$(PKG_VERSION)
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

# Wireguard's makefile needs this to know where to build the kernel module
export KERNELDIR:=$(LINUX_DIR)

include $(INCLUDE_DIR)/package.mk

define Package/wireguard/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  URL:=https://www.wireguard.io
  MAINTAINER:=Baptiste Jonglez <openwrt@bitsofnetworks.org>, \
              Kevin Darbyshire-Bryant <ldir@darbyshire-bryant.me.uk> \
              Dan Luedtke <mail@danrl.com>, \
              Jason A. Donenfeld <Jason@zx2c4.com>
endef

define Package/wireguard/Default/description
  WireGuard is a novel VPN that runs inside the Linux Kernel and utilizes
  state-of-the-art cryptography. It aims to be faster, simpler, leaner, and
  more useful than IPSec, while avoiding the massive headache. It intends to
  be considerably more performant than OpenVPN.  WireGuard is designed as a
  general purpose VPN for running on embedded interfaces and super computers
  alike, fit for many different circumstances.
  It runs over UDP.
endef

define Package/wireguard
  $(call Package/wireguard/Default)
  TITLE:=Wireguard meta-package
  DEPENDS:=+wireguard-tools +kmod-wireguard
endef

include $(INCLUDE_DIR)/kernel-defaults.mk
include $(INCLUDE_DIR)/package-defaults.mk

# Used by Build/Compile/Default
MAKE_PATH:=src/tools

define Build/Compile
	$(MAKE) $(KERNEL_MAKEOPTS) M="$(PKG_BUILD_DIR)/src" modules
	$(call Build/Compile/Default)
endef

define Package/wireguard/install
  true
endef

define Package/wireguard/description
  $(call Package/wireguard/Default/description)
endef

define Package/wireguard-tools
  $(call Package/wireguard/Default)
  TITLE:=Wireguard userspace control program (wg)
  DEPENDS:=+libmnl
endef

define Package/wireguard-tools/description
  $(call Package/wireguard/Default/description)

  This package provides the userspace control program for wireguard, `wg`,
  and a netifd protocol helper.
endef

define Package/wireguard-tools/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/tools/wg $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/lib/netifd/proto/
	$(INSTALL_BIN) ./files/wireguard.sh $(1)/lib/netifd/proto/
endef

define KernelPackage/wireguard
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  TITLE:=Wireguard kernel module
  DEPENDS:=+IPV6:kmod-udptunnel6 +kmod-udptunnel4
  FILES:= $(PKG_BUILD_DIR)/src/wireguard.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoProbe,wireguard)
endef

define KernelPackage/wireguard/description
  $(call Package/wireguard/Default/description)

  This package provides the kernel module for wireguard.
endef

$(eval $(call BuildPackage,wireguard))
$(eval $(call BuildPackage,wireguard-tools))
$(eval $(call KernelPackage,wireguard))
