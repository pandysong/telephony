#
# Copyright (C) 2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=chan-lantiq
PKG_VERSION:=1.0
PKG_RELEASE:=1

PKG_LICENSE:=GPL-2.0
PKG_MAINTAINER:=Jiri Slachta <jiri@slachta.eu>

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

PKG_FLAGS:=nonshared

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)/Default
  SUBMENU:=Telephony
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Lantiq channel driver
  URL:=https://github.com/kochstefan/asterisk_channel_lantiq
  DEPENDS:=@(TARGET_lantiq_falcon||TARGET_lantiq_xway||TARGET_lantiq_xrx200) +kmod-ltq-vmmc
endef

define Package/asterisk13-$(PKG_NAME)
$(call Package/$(PKG_NAME)/Default)
  DEPENDS+=asterisk13
  VARIANT:=asterisk13
endef

define Package/description/Default
An implementation of a Lantiq TAPI channel driver for Asterisk.
endef

Package/asterisk13-$(PKG_NAME)/description = $(Package/description/Default)

define Package/conffiles/Default
/etc/asterisk/lantiq.conf
endef

Package/asterisk13-$(PKG_NAME)/conffiles = $(Package/conffiles/Default)

define Package/Install/Default
	$(INSTALL_DIR) $(1)/etc/asterisk
	$(INSTALL_CONF) $(SOURCE)/files/lantiq-$(BUILD_VARIANT).conf.sample \
		$(1)/etc/asterisk/lantiq.conf
	$(INSTALL_DIR) $(1)/usr/lib/asterisk/modules
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/chan_lantiq.so \
		$(1)/usr/lib/asterisk/modules
endef

Package/asterisk13-$(PKG_NAME)/install = $(Package/Install/Default)

ifeq ($(BUILD_VARIANT),asterisk13)
CHAN_LANTIQ_AST_INCLUDES:=-I$(STAGING_DIR)/usr/include/asterisk-13/include
endif

define Build/Prepare
	$(CP) $(SOURCE)/files/chan_lantiq-$(BUILD_VARIANT).c $(PKG_BUILD_DIR)/chan_lantiq.c
	$(CP) $(SOURCE)/files/default.exports $(PKG_BUILD_DIR)/chan_lantiq.exports
endef

define Build/Configure
endef

define Build/Compile
	cd $(PKG_BUILD_DIR) && \
	$(TARGET_CC) -o chan_lantiq.o -c chan_lantiq.c -MD -MT chan_lantiq.o \
		-MF .chan_lantiq.o.d -MP -pthread \
		$(CHAN_LANTIQ_AST_INCLUDES) \
		$(TARGET_CFLAGS) \
		$(TARGET_CPPFLAGS) \
		-Wall -Wstrict-prototypes -Wmissing-prototypes \
		-Wmissing-declarations $(FPIC) -DAST_MODULE=\"chan_lantiq\" && \
	$(TARGET_CC) -o chan_lantiq.so -pthread $(TARGET_LDFLAGS) -shared \
		-Wl,--version-script,chan_lantiq.exports,--warn-common \
		chan_lantiq.o
endef

$(eval $(call BuildPackage,asterisk13-$(PKG_NAME)))
