#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nettle
PKG_VERSION:=3.9.1
PKG_RELEASE:=1
PKG_USE_MIPS16:=0

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@GNU/nettle
PKG_HASH:=ccfeff981b0ca71bbd6fbcb054f407c60ffb644389a5be80d6716d5b550c6ce3

PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_BUILD_PARALLEL:=1

PKG_CONFIG_DEPENDS := CONFIG_LIBNETTLE_MINI

include $(INCLUDE_DIR)/package.mk

define Package/libnettle
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=GNU crypto library
  URL:=http://www.lysator.liu.se/~nisse/nettle/
  DEPENDS+= +!LIBNETTLE_MINI:libgmp
  ABI_VERSION:=8
endef

define Package/libnettle/config
	source "$(SOURCE)/Config.in"
endef

TARGET_CFLAGS += $(FPIC)

CONFIGURE_ARGS += \
	--enable-shared \
	--enable-fat \
	--disable-openssl \
	--disable-documentation \
	--enable-static

ifeq ($(CONFIG_LIBNETTLE_MINI),y)
CONFIGURE_ARGS += --enable-mini-gmp
endif

ifeq ($(CONFIG_CPU_SUBTYPE),neon)
CONFIGURE_ARGS += \
	--enable-arm-neon
endif

ifeq ($(ARCH),armeb)
CONFIGURE_ARGS += \
	--disable-assembler
endif

define Build/Compile
	$(call Build/Compile/Default, \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CC="$(TARGET_CC)" \
		libnettle.so libhogweed.so
	+$(MAKE) -i $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/nettle
	$(CP) $(PKG_INSTALL_DIR)/usr/include/nettle/*.h $(1)/usr/include/nettle/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnettle.{a,so*} $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libhogweed.{a,so*} $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/pkgconfig/nettle.pc \
		$(PKG_INSTALL_DIR)/usr/lib/pkgconfig/hogweed.pc \
		$(1)/usr/lib/pkgconfig/
endef

define Package/libnettle/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnettle.so.* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libhogweed.so.* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libnettle))
