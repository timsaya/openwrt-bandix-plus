# openwrt-bandix-plus

[English](README.md) 简体中文

[![许可证](https://img.shields.io/badge/许可证-Apache--2.0-blue.svg)](LICENSE)

## 简介

openwrt-bandix-plus 是 luci-app-bandix-plus 的依赖包，提供网络流量监控的核心功能。

## 安装方法

### 从 Release 下载安装

1. 前往 [Release 页面](https://github.com/timsaya/openwrt-bandix-plus/releases) 下载适合您设备架构的 ipk 文件

2. 将下载的 ipk 文件上传到您的 OpenWrt 设备

3. 安装：

```bash
opkg install /path/to/bandix-plus_xxx.ipk
```

### 从源码编译

```bash
# feeds.conf
    "src-git openwrt_bandix_plus https://github.com/timsaya/openwrt-bandix-plus.git;main"
    "src-git luci_app_bandix_plus https://github.com/timsaya/luci-app-bandix-plus.git;main"
```

## 许可证

本项目采用 Apache-2.0 许可证。

## 维护者

- [timsaya](https://github.com/timsaya)
