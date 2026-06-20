include $(TOPDIR)/rules.mk

PKG_NAME:=bandix-plus
PKG_VERSION:=0.1.1
PKG_RELEASE:=2

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_PLUS_VERSION:=0.1.1
RUST_BINARY_FILENAME:=bandix-plus-$(RUST_BANDIX_PLUS_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/timsaya/bandix-plus/releases/download/v$(RUST_BANDIX_PLUS_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=4cd6a55cc6b8fd5e380ffe9592166f346cb5f0b2189c500772711c11414fadd5
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
		PKG_HASH:=de6f6f676b13fa642a8d0a0dac8b3c5d0665978c23431b1510611ca8ef85e492
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=0c18ff757ac58ba6af5a28c1ba7f4d10ad72b7a5e4a261bef5953c1b4fe5c24b
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=60d952970b0d4fa03d50973ea43b22ec8358f67d1903e494181acc685f552e3c
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=de1283b2267a48bd7dc7bf45ac6e0fd8713a3a9352d85a5020e4b945c237eca2
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=291ee1e371f69e644a32f41e67310e9e7330f7fd670eeac6aeb86ad06f3d2b17
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=05d60c4e43e4b177240593f9442a59d4248c806fc59833564e35f6a2f7b9260e
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=d8eccefd91a2cbaf2c318f8a220d08ac55402ea40b7935d454e8be28499505e7
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=ac4b93fa85ec323f6ff0fe714898c4fd08ef3ca0da66d6ad05d697517179dac0
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=2148a4d78ff169ee1d99a4e1fecf95a02df96e5c0fc56e1cdff2f00a82fbb1ad
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=b49f085027d914a2407d8bd2b54e223b95ef8297883209c8a2ae8a771bbc0e08
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
	$(INSTALL_CONF) ./files/bandix-plus.config $(1)/etc/config/bandix_plus

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
