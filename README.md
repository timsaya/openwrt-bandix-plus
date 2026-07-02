# openwrt-bandix-plus

English | [简体中文](README.zh.md)


[![License](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](LICENSE)

## Introduction

openwrt-bandix-plus is a dependency package for luci-app-bandix-plus, providing core functionality for network traffic monitoring.

## Installation

### Install from Release

1. Visit the [Release page](https://github.com/timsaya/openwrt-bandix-plus/releases) to download the ipk file suitable for your device architecture

2. Upload the downloaded ipk file to your OpenWrt device

3. Install:

```bash
opkg install /path/to/bandix-plus_xxx.ipk
```

### Build

```bash
# feeds.conf
    "src-git openwrt_bandix_plus https://github.com/timsaya/openwrt-bandix-plus.git;main"
    "src-git luci_app_bandix_plus https://github.com/timsaya/luci-app-bandix-plus.git;main"
```

## License

This project is licensed under the Apache-2.0 License.

## Maintainer

- [timsaya](https://github.com/timsaya)
