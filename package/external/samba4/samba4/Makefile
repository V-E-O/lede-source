#
# Copyright (C) 2007-2015 wongsyrone
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=samba4
PKG_VERSION:=4.6.4
PKG_RELEASE:=1

PKG_SOURCE_URL:= \
    https://download.samba.org/pub/samba/stable \
    https://download.samba.org/pub/samba \
    https://download.samba.org/pub/samba/old-versions

PKG_SOURCE:=samba-$(PKG_VERSION).tar.gz
PKG_BUILD_DIR:=$(BUILD_DIR)/samba-$(PKG_VERSION)
PKG_MD5SUM:=2ae8ce2862d8bfa95e4681371ebb1623


PKG_LICENSE:=GPLv3
PKG_LICENSE_FILES:=COPYING

#PKG_BUILD_DEPENDS:=python-base heimdal
PKG_BUILD_DEPENDS:=python-light heimdal

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
# for "uname release type" check
include $(INCLUDE_DIR)/kernel.mk


define Package/samba4-server
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Samba $(PKG_VERSION) SMB/CIFS server
  URL:=http://www.samba.org/
  DEPENDS:=+python-light +zlib +libpthread +libpopt +e2fsprogs
#DEPENDS:=+python-base +zlib +libpthread +libpopt +e2fsprogs +PACKAGE_samba43-server_asyncio:libaio
# DEPENDS:=+python-light +zlib +libpthread +libpopt +e2fsprogs +PACKAGE_samba43-server_asyncio:libaio
#DEPENDS+= +libreadline +libuuid +libtirpc libtirpc
endef

define Package/samba4-server/config
    select BUSYBOX_CONFIG_RENICE
endef

define Package/samba4-server/description
 The Samba software suite is a collection of programs that implements the
 SMB protocol for UNIX systems, allowing you to serve files and printers to
 Windows, NT, OS/2 and DOS clients. This protocol is sometimes also referred
 to as the LanManager or Netbios protocol.
endef

# fix include "dbwrap/dbwrap_open.h" not found in auth/credentials/credentials_secrets.c -I$(PKG_BUILD_DIR)/source3/lib
EXTRA_CFLAGS += \
    -ffunction-sections -fdata-sections \
    -Wno-deprecated-declarations
EXTRA_LDFLAGS += -Wl,--gc-sections

# Use host compiled python2 as interpreter
# Use target python2-config to retrive cflags, ldflags, libs, etc.
# Must have python2 patched to make python2-config works

#   _python_sysroot="$(STAGING_DIR)" \
    _python_prefix="/usr" \
    _python_exec_prefix="/usr/bin" \
    PYTHON="$(STAGING_DIR_HOST)/bin/python2"
    
CONFIGURE_VARS += \
    PYTHON_CONFIG="$(STAGING_DIR)/usr/bin/python2-config" \
    python_LDFLAGS="" \
    python_LIBDIR=""

# Taken from package-defaults.mk
SAMBA_CONFIGURE_ARGS += \
        --target=$(GNU_TARGET_NAME) \
        --program-prefix="" \
        --prefix=$(CONFIGURE_PREFIX) \
        --exec-prefix=$(CONFIGURE_PREFIX) \
        --bindir=$(CONFIGURE_PREFIX)/bin \
        --sbindir=$(CONFIGURE_PREFIX)/sbin \
        --libexecdir=$(CONFIGURE_PREFIX)/lib \
        --sysconfdir=/etc \
        --datadir=$(CONFIGURE_PREFIX)/share \
        --localstatedir=/var \
        --mandir=$(CONFIGURE_PREFIX)/man \
        --infodir=$(CONFIGURE_PREFIX)/info

# Should have a look before compiling.
SAMBA4_CACHE := " \
    \nChecking simple C program: OK \
    \nrpath library support: OK \
    \n-Wl,--version-script support: OK  \
    \nChecking getconf LFS_CFLAGS: NO   \
    \nChecking correct behavior of strtoll: NO  \
    \nChecking for working strptime: OK \
    \nChecking for C99 vsnprintf: \"1\" \
    \nChecking for HAVE_SHARED_MMAP: OK \
    \nChecking for HAVE_MREMAP: OK  \
    \nChecking for HAVE_SECURE_MKSTEMP: OK  \
    \nChecking for HAVE_IFACE_GETIFADDRS: NO    \
    \nChecking for HAVE_IFACE_IFCONF: NO    \
    \nChecking for HAVE_IFACE_IFREQ: NO \
    \nChecking for large file support without additional flags: NO  \
    \nChecking for -D_FILE_OFFSET_BITS=64: OK   \
    \nChecking for HAVE_INCOHERENT_MMAP: NO \
    \nChecking value of NSIG: \"65\"    \
    \nChecking value of _NSIG: \"65\"   \
    \nChecking value of SIGRTMAX: \"64\"    \
    \nChecking value of SIGRTMIN: \"34\"    \
    \nChecking whether the WRFILE -keytab is supported: OK  \
    \nChecking for kernel change notify support: OK \
    \nChecking for Linux kernel oplocks: OK \
    \nChecking for kernel share modes: OK   \
    \nChecking whether POSIX capabilities are available: OK \
    \nChecking if can we convert from CP850 to UCS-2LE: OK  \
    \nChecking if can we convert from UTF-8 to UCS-2LE: OK  \
    \nvfs_fileid checking for statfs() and struct statfs.f_fsid: OK \
    \nChecking whether we can use Linux thread-specific credentials with 32-bit system calls: OK    \
    \nChecking whether fcntl locking is available: OK   \
    \nChecking for the maximum value of the 'time_t' type: NO   \
    \nChecking whether the realpath function allows a NULL argument: OK \
    \nChecking for ftruncate extend: OK \
    \ngetcwd takes a NULL argument: OK  \
    \nChecking for small off_t: NO  \
    \nChecking whether blkcnt_t is 32 bit: NO   \
    \nChecking whether blkcnt_t is 64 bit: OK   \
    \nChecking whether fcntl lock supports open file description locks: NO \
    \n"


define Build/Configure
    (cd $(PKG_BUILD_DIR); \
        echo -e >cache.txt $(SAMBA4_CACHE) " \
            \nChecking uname release type: \"$(LINUX_VERSION)\" \
            \nChecking uname machine type: \"$(ARCH)\" \
            \nChecking uname version type: \"#1 Tue Nov 1 12:34:56 UTC 2016\" \
            \nChecking uname sysname type: \"Linux\" \
            \n" ; \
        $(CONFIGURE_VARS) \
        ./buildtools/bin/waf configure -j$(shell nproc) \
            --cross-compile \
            --cross-answers=$(PKG_BUILD_DIR)/cache.txt \
            --hostcc="$(HOSTCC)" \
            $(SAMBA_CONFIGURE_ARGS) \
            --with-lockdir=/var/lock \
            --with-logfilebase=/var/log \
            --with-piddir=/var/run \
            --with-privatedir=/etc/samba \
            --enable-fhs \
            --without-dmapi \
            --disable-glusterfs \
            --disable-rpath \
            --disable-rpath-install \
            --disable-avahi \
            --disable-cups \
            --disable-iprint \
            --disable-gnutls \
            --with-relro \
            --with-sendfile-support \
            --without-acl-support \
            --without-ads \
            --without-ad-dc \
            --without-automount \
            --without-cluster-support \
            --without-dnsupdate \
            --without-fam \
            --without-gettext \
            --without-iconv \
            --without-ldap \
            --without-pam \
            --without-pie \
            --without-regedit \
            --without-systemd \
            --without-utmp \
            --without-winbind \
            --enable-auto-reconfigure \
            --bundled-libraries='!asn1_compile,!compile_et' \
            --without-quotas \
            --with-syslog \
            --nopyc \
            --nopyo \
    )
endef

#--builtin-libraries='heimdal,zlib,popt,talloc,pytalloc,pytalloc-util'
#--bundled-libraries='!asn1_compile,!compile_et,heimdal,zlib,popt,talloc,pytalloc,pytalloc-util'
#--with-shared-modules='pdb_smbpasswd,pdb_tdbsam,auth_sam,auth_unix,auth_builtin,vfs_default'
#--private-libraries=ALL


define Build/Compile
    $(call Build/Compile/Default,-j$(shell nproc)) 
endef

define Package/samba4-server/install
    # remove some large unwanted packages first
    rm -f $(PKG_INSTALL_DIR)/usr/bin/smbtorture
    rm -f $(PKG_INSTALL_DIR)/usr/bin/rpcclient
    rm -f $(PKG_INSTALL_DIR)/usr/bin/net
    #rm -f -R $(PKG_INSTALL_DIR)/usr/lib/python2.7
    $(INSTALL_DIR) $(1)/etc/config $(1)/etc/samba $(1)/etc/init.d $(1)/usr
    $(INSTALL_DATA) ./files/$(PKG_NAME).config $(1)/etc/config/samba4
    $(INSTALL_DATA) ./files/smb.conf.template $(1)/etc/samba
    $(INSTALL_BIN) ./files/$(PKG_NAME).init $(1)/etc/init.d/samba4
    $(CP) $(PKG_INSTALL_DIR)/usr/{lib,bin,sbin} $(1)/usr/
endef


define Package/samba4-server/conffiles
    /etc/config/samba4
    /etc/samba/smb.conf.template
    /etc/samba/smbpasswd
    /etc/samba/secrets.tdb
    /etc/samba/passdb.tdb
endef


$(eval $(call BuildPackage,$(PKG_NAME)-server))
