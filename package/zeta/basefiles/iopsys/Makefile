#
# Copyright (C) 2007-2014 OpenWrt.org
# Copyright (C) 2010 Vertical Communications
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/version.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=inteno-base
PKG_RELEASE:=156

#PKG_FILE_DEPENDS:=$(PLATFORM_DIR)/ 
#$(GENERIC_PLATFORM_DIR)/inteno-base/
PKG_BUILD_DEPENDS:=opkg/host

include $(INCLUDE_DIR)/package.mk

#+ice-client +ice-client-guest
define Package/inteno-base
  SECTION:=base
  CATEGORY:=Base system
  DEPENDS:=+netifd +minidlna +miniupnpd +libc +procd +jsonfilter +port-management
  TITLE:=Base filesystem for OpenWrt and IOPSYS
  URL:=http://openwrt.org/
  VERSION:=$(PKG_RELEASE)-$(REVISION)
endef

define Package/inteno-base/conffiles
/etc/hosts
/etc/inittab
/etc/group
/etc/passwd
/etc/shadow
/etc/profile
/etc/shells
/etc/sysctl.conf
/etc/rc.local
/etc/sysupgrade.conf
/etc/config/
/etc/dropbear/
/etc/crontabs/
$(call $(TARGET)/conffiles)
endef

define Package/inteno-base/description
 This package contains a base filesystem and system scripts for OpenWrt.
endef

ifneq ($(CONFIG_PREINITOPT),)
define ImageConfigOptions
	mkdir -p $(1)/lib/preinit
	echo 'pi_suppress_stderr="$(CONFIG_TARGET_PREINIT_SUPPRESS_STDERR)"' >$(1)/lib/preinit/00_preinit.conf
	echo 'fs_failsafe_wait_timeout=$(if $(CONFIG_TARGET_PREINIT_TIMEOUT),$(CONFIG_TARGET_PREINIT_TIMEOUT),2)' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_init_path=$(if $(CONFIG_TARGET_INIT_PATH),$(CONFIG_TARGET_INIT_PATH),"/bin:/sbin:/usr/bin:/usr/sbin")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_init_env=$(if $(CONFIG_TARGET_INIT_ENV),$(CONFIG_TARGET_INIT_ENV),"")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_init_cmd=$(if $(CONFIG_TARGET_INIT_CMD),$(CONFIG_TARGET_INIT_CMD),"/sbin/init")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_init_suppress_stderr="$(CONFIG_TARGET_INIT_SUPPRESS_STDERR)"' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_ifname=$(if $(CONFIG_TARGET_PREINIT_IFNAME),$(CONFIG_TARGET_PREINIT_IFNAME),"")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_ip=$(if $(CONFIG_TARGET_PREINIT_IP),$(CONFIG_TARGET_PREINIT_IP),"192.168.1.1")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_netmask=$(if $(CONFIG_TARGET_PREINIT_NETMASK),$(CONFIG_TARGET_PREINIT_NETMASK),"255.255.255.0")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_broadcast=$(if $(CONFIG_TARGET_PREINIT_BROADCAST),$(CONFIG_TARGET_PREINIT_BROADCAST),"192.168.1.255")' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_preinit_net_messages="$(CONFIG_TARGET_PREINIT_SHOW_NETMSG)"' >>$(1)/lib/preinit/00_preinit.conf
	echo 'pi_preinit_no_failsafe_netmsg="$(CONFIG_TARGET_PREINIT_SUPPRESS_FAILSAFE_NETMSG)"' >>$(1)/lib/preinit/00_preinit.conf
endef
endif

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	touch $(PKG_BUILD_DIR)/test
endef

define Build/Compile/Default

endef

Build/Compile = $(Build/Compile/Default)

define Package/inteno-base/install
	$(CP) ./base-files-openwrt/* $(1)/
	$(CP) ./base-files-iopsys/* $(1)/
	
	# setup root filesystem
	mkdir -p $(1)/CONTROL
	mkdir -p $(1)/dev
	mkdir -p $(1)/etc/crontabs
	mkdir -p $(1)/etc/rc.d
	mkdir -p $(1)/overlay
	mkdir -p $(1)/lib/firmware
	$(if $(LIB_SUFFIX),-ln -s lib $(1)/lib$(LIB_SUFFIX))
	mkdir -p $(1)/mnt
	mkdir -p $(1)/proc
	mkdir -p $(1)/tmp
	mkdir -p $(1)/usr/lib
	$(if $(LIB_SUFFIX),-ln -s lib $(1)/usr/lib$(LIB_SUFFIX))
	mkdir -p $(1)/usr/bin
	mkdir -p $(1)/sys
	mkdir -p $(1)/www
	mkdir -p $(1)/root
	ln -sf /proc/mounts $(1)/etc/mtab
	rm -f $(1)/var
	ln -sf /tmp $(1)/var
	mkdir -p $(1)/etc
	ln -sf /tmp/resolv.conf /tmp/fstab /tmp/TZ $(1)/etc/

	chmod 0600 $(1)/etc/shadow
	chmod 1777 $(1)/tmp

	$(call ImageConfigOptions,$(1))
	$(call Package/inteno-base/install-target,$(1))
	for conffile in $(1)/etc/config/*; do \
		if [ -f "$$$$conffile" ]; then \
			grep "$$$${conffile##$(1)}" $(1)/CONTROL/conffiles || \
				echo "$$$${conffile##$(1)}" >> $(1)/CONTROL/conffiles; \
		fi \
	done
endef

$(eval $(call BuildPackage,inteno-base))


define Package/inteno-asterisk-modules
  CATEGORY:=Config
  DEPENDS:=+asterisk18-mod +asterisk18-mod-app-playtones +asterisk18-mod-app-read +asterisk18-mod-app-transfer +asterisk18-mod-cdr +asterisk18-mod-chan-brcm +asterisk18-mod-codec-a-mu +asterisk18-mod-codec-alaw +asterisk18-mod-codec-g722 +asterisk18-mod-codec-g726 +asterisk18-mod-codec-ulaw +asterisk18-mod-format-g726 +asterisk18-mod-format-g729 +asterisk18-mod-format-sln +asterisk18-mod-format-sln16 +asterisk18-mod-func-channel +asterisk18-mod-func-db +asterisk18-mod-func-shell +asterisk18-mod-pbx-spool +asterisk18-mod-res-stun +asterisk18-mod-res-voice +asterisk18-mod-sounds +voice-client
  TITLE:=Preselected modules for standard asterisk setup
endef
define Package/inteno-asterisk-modules/description
 Inteno asterisk modules preselection package
endef
$(eval $(call BuildPackage,inteno-asterisk-modules))

define Package/bice-client
	CATEGORY:=Config
	DEPENDS:=+ice-client +ice-client-camerawatch +ice-client-guest +ice-client-iperf +ice-client-logreader +ice-client-monitor +ice-client-netrounds +ice-client-rshell +ice-client-torrent 
	TITLE:=ICE client base
endef
$(eval $(call BuildPackage,bice-client))
define Package/bice-client/description
	Base package for ice client
endef

define Package/inteno-base-voice
  CATEGORY:=Config
  DEPENDS:=+asterisk18 +inteno-asterisk-modules +voice-client +endptcfg +alsa-lib 
  TITLE:=Preselected packages for voice
endef
$(eval $(call BuildPackage,inteno-base-voice))
define Package/inteno-base-voice/description
 Inteno voice base packages
endef

define Package/inteno-base-dect
  CATEGORY:=Config
  DEPENDS:=+inteno-base-voice +natalie-dect +natalie-dect-h +dectmngr 
  TITLE:=Preselected packages for dect
endef
$(eval $(call BuildPackage,inteno-base-dect))
define Package/inteno-base-dect/description
 Inteno dect default packages
endef

define Package/inteno-base-dect-external
  CATEGORY:=Config
  DEPENDS:=+inteno-base-voice +natalie-dect-h +dectmngr2
  TITLE:=Preselected packages for external dect
endef
$(eval $(call BuildPackage,inteno-base-dect-external))
define Package/inteno-base-dect-external/description
 Inteno dect default packages for external dect
endef

define Package/inteno-base-juci
	CATEGORY:=Config
	DEPENDS:=+juci \
		+PACKAGE_asterisk18-mod:juci-asterisk \
		+PACKAGE_bcmkernel:juci-broadcom-dsl \
		+PACKAGE_bcmkernel:juci-broadcom-ethernet \
		+PACKAGE_bcmkernel:juci-broadcom-wl \
		+PACKAGE_bcmkernel:juci-broadcom-vlan \
		+juci-ddns \
		+juci-diagnostics \
		+PACKAGE_dnsmasq:juci-dnsmasq-dhcp \
		+juci-dropbear \
		+juci-event \
		+PACKAGE_firewall:juci-firewall-fw3 \
		+juci-igmpinfo \
		+juci-inteno-router \
		+juci-jquery-console \
		+juci-macdb \
		+PACKAGE_minidlna:juci-minidlna \
		+juci-mod-status \
		+juci-mod-system \
		+juci-ethernet \
		+juci-netmode \
		+juci-natalie-dect \
		+PACKAGE_netifd:juci-network-netifd \
		+PACKAGE_samba36-server:juci-samba \
		+PACKAGE_inteno-base:juci-sysupgrade \
		+PACKAGE_miniupnpd:juci-upnp \
		+USB_SUPPORT:juci-usb 
	TITLE:=Preselected packages for juci
endef
$(eval $(call BuildPackage,inteno-base-juci))
define Package/inteno-base-juci/description
 Inteno juci packages
endef

#+lighttpd-mod-setenv +lighttpd-mod-userdir +lighttpd-mod-webdav 
define Package/inteno-base-luci
	CATEGORY:=Config
	DEPENDS:= +lighttpd +lighttpd-mod-cgi +luci +luci-mod-admin-core +luci-mod-admin-full +luci-lib-core +luci-lib-core_source +luci-lib-ipkg +luci-lib-json +luci-lib-nixio +luci-lib-nixio_notls +luci-lib-sys +luci-lib-web +luci-theme-base +luci-theme-bootstrap +luci-i18n-english +luci-proto-3g +luci-proto-4g +luci-proto-core +luci-proto-ipv6 +luci-proto-ppp +luci-sgi-cgi +luci-app-firewall +luci-app-iup +luci-app-mcpd +luci-app-parental-control +luci-app-port-management +luci-app-power-mgmt +luci-app-speedtest +luci-app-minidlna +luci-app-multiwan +luci-app-qos +luci-app-samba +luci-app-snmp +luci-app-upnp
	TITLE:=Inteno luci base libraries
endef 
$(eval $(call BuildPackage,inteno-base-luci))
define Package/inteno-base-luci/description
 Inteno luci base package
endef
