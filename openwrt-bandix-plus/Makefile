include $(TOPDIR)/rules.mk

PKG_NAME:=bandix-plus
PKG_VERSION:=0.1.2
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_PLUS_VERSION:=0.1.2
RUST_BINARY_FILENAME:=bandix-plus-$(RUST_BANDIX_PLUS_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/timsaya/bandix-plus/releases/download/v$(RUST_BANDIX_PLUS_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=eb4ae61a224f36b4e836d53e23b74d9b5d746ed9f0f25839b10e464fe5445f59
else ifeq ($(ARCH),arm) # update.sh skip this tag, do not delete this comment
	# Ref https://github.com/openwrt/packages/blob/master/lang/rust/rust-values.mk

	ARM_CPU_TYPE:=arm-armel
	ifeq ($(CONFIG_arm_v6)$(CONFIG_arm_v7),)
		ARM_CPU_TYPE:=$(subst arm-,armv5te-,$(ARM_CPU_TYPE))
	else ifeq ($(CONFIG_arm_v7),y)
		ARM_CPU_TYPE:=$(subst arm-,armv7-,$(ARM_CPU_TYPE))
	endif

	ifeq ($(CONFIG_HAS_FPU),y)
		ARM_CPU_TYPE:=$(subst -armel,-armhf,$(ARM_CPU_TYPE))
	endif

	ifeq ($(ARM_CPU_TYPE),arm-armel)
		PKG_HASH:=54ad332d3f5bf62eafeae6e0396cc3db8cd8413b674437a79f7f1fa4cf1282e7
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=f793a5d3069c8bffdfc72a044e097a6edf4326e0207628465da8686a51dfb052
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=85fbe6f1da86e2ab1ba446738018da7e332e8a638e460736c84ab1e89490fffb
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=4912b40e6def4facb5bb9a9d2f60d7fb00c80099c58169ce10588d718ee9c07b
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=eef012ae1fe55463dded8061089d1746b9332daccaff3abf3567531ffb889da6
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=5ac144223a11b9e691c486d22a95af4a2104566c1be191bcfb51942d5b40d6a0
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=b1274df63584ede7636306b5a4960ff2c775a1bc73689bf5ef6d8e89afd397ec
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=0a7801f5befe6d61eee98a0c788739ea1f287d03c8b2fac1d752dba9449f9f61
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=f025ebd37f7a958870aef3fcee1389b294b8c0d6a79c79cfa912715b80eb5449
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=799c65a830329589992703be0198b2ad06635eba059d94d53cee400a6d588db8
else
	PKG_HASH:=dummy
endif

define Package/$(PKG_NAME)
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Bandix Plus - Network traffic monitoring tool
	DEPENDS:=+zoneinfo-all +conntrack +iw
endef

define Package/$(PKG_NAME)/description
	Bandix Plus core service for network traffic monitoring
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)/bin
	gzip -dc "$(DL_DIR)/$(PKG_SOURCE)" | tar -C $(PKG_BUILD_DIR)/bin --strip-components=1 -xf -
	$(Build/Patch)
endef

define Build/Compile
	# nothing to do
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/bandix-plus $(1)/usr/bin/

	# 添加启动脚本和配置文件
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_BIN) ./files/bandix-plus.init $(1)/etc/init.d/bandix-plus

	# 安装配置文件
	$(INSTALL_DIR) $(1)/usr/share/bandix-plus
	$(INSTALL_CONF) ./files/bandix_plus.config $(1)/etc/config/bandix_plus

	# 安装 UCI defaults（仅补齐缺失配置项，不覆盖用户已有值）
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/99-bandix-plus-defaults $(1)/etc/uci-defaults/99-bandix-plus-defaults
	
	# 配置 sysupgrade 保留列表
	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_DATA) ./files/bandix-plus.keep $(1)/lib/upgrade/keep.d/bandix-plus
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/bandix_plus
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] && exit 0

DEFAULTS_SCRIPT="/etc/uci-defaults/99-bandix-plus-defaults"
if [ -x "$$DEFAULTS_SCRIPT" ]; then
	"$$DEFAULTS_SCRIPT" && rm -f "$$DEFAULTS_SCRIPT"
fi

exit 0
endef

$(eval $(call BuildPackage,bandix-plus))
