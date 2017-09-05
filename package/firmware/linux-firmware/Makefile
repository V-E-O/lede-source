#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=linux-firmware
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git
PKG_SOURCE_DATE:=2017-08-29
PKG_SOURCE_VERSION:=9d40a17beaf271e6ad47a5e714a296100eef4692
#PKG_MIRROR_HASH:=

PKG_MAINTAINER:=Felix Fietkau <nbd@nbd.name>
#PKG_GIT_OPTS:= --depth 1

SCAN_DEPS = *.mk

include $(INCLUDE_DIR)/package.mk

define Package/firmware-default
  SECTION:=firmware
  CATEGORY:=Firmware
  URL:=http://git.kernel.org/cgit/linux/kernel/git/firmware/linux-firmware.git
  TITLE:=$(1)
endef

define Build/Compile

endef

include $(wildcard ./*.mk)
#$(eval $(call BuildPackage,linux-firmware))
