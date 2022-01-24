#
# Copyright (c) 2014 The Linux Foundation. All rights reserved.
# Permission to use, copy, modify, and/or distribute this software for
# any purpose with or without fee is hereby granted, provided that the
# above copyright notice and this permission notice appear in all copies.
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
# WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
# MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
# ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
# WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
# ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT
# OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=shortcut-fe
PKG_RELEASE:=1

include $(INCLUDE_DIR)/package.mk

define KernelPackage/shortcut-fe
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  DEPENDS:=@IPV6 +kmod-nf-conntrack
  TITLE:=Kernel driver for SFE
  FILES:= \
	   $(PKG_BUILD_DIR)/shortcut-fe.ko \
	   $(PKG_BUILD_DIR)/shortcut-fe-ipv6.ko
  KCONFIG:= \
	   CONFIG_NF_CONNTRACK_EVENTS=y \
	   CONFIG_NF_CONNTRACK_TIMEOUT=y \
	   CONFIG_SHORTCUT_FE=y \
	   CONFIG_XFRM=y
  AUTOLOAD:=$(call AutoLoad,09,shortcut-fe shortcut-fe-ipv6)
endef

define KernelPackage/shortcut-fe/Description
Shortcut is an in-Linux-kernel IP packet forwarding engine.
endef

define KernelPackage/shortcut-fe/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/etc/init.d/sfe $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/etc/config/sfe $(1)/etc/config/sfe
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) ./files/usr/bin/sfe_dump $(1)/usr/bin
endef

define KernelPackage/shortcut-fe-cm
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  DEPENDS:=@IPV6 +kmod-ipt-conntrack +kmod-shortcut-fe
  TITLE:=Kernel driver for SFE
  FILES:=$(PKG_BUILD_DIR)/shortcut-fe-cm.ko
  KCONFIG:= \
	   CONFIG_NF_CONNTRACK_CHAIN_EVENTS=y \
	   CONFIG_NF_CONNTRACK_EVENTS=y \
	   CONFIG_XFRM=y
endef

define KernelPackage/shortcut-fe-cm/Description
Simple connection manager for the Shortcut forwarding engine.
endef

TARGET_CFLAGS := $(filter-out -O%,$(TARGET_CFLAGS)) -O3
SFE_MAKE_OPTS := SFE_SUPPORT_IPV6=y

define Build/Compile
	$(MAKE) -C "$(LINUX_DIR)" \
		$(strip $(SFE_MAKE_OPTS)) \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		SUBDIRS="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		modules
endef

ifneq ($(CONFIG_PACKAGE_kmod-shortcut-fe)$(CONFIG_PACKAGE_kmod-shortcut-fe-cm),)
define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/shortcut-fe
	$(CP) -rf $(PKG_BUILD_DIR)/sfe.h $(1)/usr/include/shortcut-fe
endef
endif

$(eval $(call KernelPackage,shortcut-fe))
$(eval $(call KernelPackage,shortcut-fe-cm))