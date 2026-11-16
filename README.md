# Flipper Zero 固件 · ESP32-C5 — OTA 分发仓库

[![最新版本](https://img.shields.io/badge/latest-2.0.0--alpha.8-orange)](version.txt)
[![目标](https://img.shields.io/badge/target-ESP32--C5-blue)](https://www.espressif.com/en/products/socs/esp32-c5)
[![固件源码](https://img.shields.io/badge/source-GitHub-lightgrey)](https://github.com/Geek-us/flipperc5-firmware)

这里是 **Flipper Zero 固件 ESP32-C5 移植版** 的在线更新源：设备通过本仓库读取
`version.txt` 清单与更新包，完成「设置 → 固件更新 → 在线更新」。

仓库只存放**预加密**更新包 —— 包内固件经 ECIES 预加密、DFU 经 AES-XTS 预加密并做
Secure Boot V2 签名，只有目标设备能解密使用。这里没有也不会有明文固件。

## 更新方式

### 设备内在线更新（推荐）

1. 设备连上 Wi-Fi（需能访问下方任一镜像）；
2. `设置 → 固件更新 → 在线更新 (OTA)`；
3. 设备会先拉取各镜像的 `version.txt` 并测速，从最快的镜像下载更新包；
4. 下载完成后自动校验、写入备用 OTA 分区并重启。

### SD 卡离线更新

1. 从本仓库下载 `<version>.tar`；
2. 放到 SD 卡的 `/ext/update/` 目录；
3. `设置 → 固件更新 → 从 SD 卡更新`。

更新包只覆盖 `apps/` 与固件本身，**不会**动你的红外、Sub-GHz、NFC、抓包等数据。

## 镜像站点

设备端按延迟自动择优，前一个失败会自动切换下一个：

| # | 镜像 | 地址 |
| --- | --- | --- |
| 1 | Netlify | <https://flipperesp32c5-ota.netlify.app> |
| 2 | Azure Static Web Apps | <https://agreeable-hill-00908fc00.5.azurestaticapps.net> |
| 3 | Upstash Blob | <https://bfa8b33f0ef0.blob.upstash.io> |
| 4 | GitHub Pages | <https://ghost64847.github.io/flipperc5-release> |
| 5 | Codeberg Pages | <https://codeberg.org/Ghost47/flipperesp32c5-ota/raw/branch/pages> |

自建镜像只需同样目录布局（`version.txt` + 更新包），设备端可用
`/ext/.fw_ota_url` 覆盖整张镜像表。

## 目录与格式

镜像根目录只有三样东西：

```
version.txt                     # 清单：版本号 / 包名 / 字节数（各一行）
<version>.tar                   # 统一更新包
index.html                      # 本目录的人类可读说明页
```

`version.txt` 示例：

```
2.0.0-alpha.8
2.0.0-alpha.8.tar
6369280
```

`<version>.tar` 内部结构：

```
update/fw.bin     预加密主固件（写入 OTA 分区）
update/dfu.bin    DFU 恢复镜像（留在 /ext/update/ 供救砖）
apps/...          FAP 应用（覆盖 /ext/apps/）
```

## 测试版本

仓库同时保留一个**内容与当前版本完全一致、仅版本号不同**的包，用于验证更新链路：

| 文件 | 用途 |
| --- | --- |
| `2.0.0-alpha.9.tar` | 与 `2.0.0-alpha.8.tar` 字节一致，仅版本号不同 |
| `version-2.0.0-alpha.9.txt` | 对应的清单，需要触发更新时替换 `version.txt` |

判定规则很简单：`version.txt` 首行的版本号与设备当前固件版本**不同**即视为有更新，
因此把 `version-2.0.0-alpha.9.txt` 的内容作为 `version.txt`，已装 alpha.8 的设备
就会收到 alpha.9 的更新提示。

## 常见问题

**提示有更新，但下载失败？**
先确认设备能访问镜像域名；多镜像会自动切换，全部失败通常是网络受限。

**能拿到明文固件自己刷吗？**
不能。分发包是预加密的，解密密钥在设备 eFuse 内；明文固件仅用于产线与内部调试，
不对外分发。

**版本号怎么读？**
遵循 [语义化版本](https://semver.org/lang/zh-CN/)：`2.0.0-alpha.8` 表示 2.0.0 的
第 8 个 alpha 预发布版，alpha < beta < rc < 正式版。

## 链接

- 固件源码与更新日志：<https://github.com/Geek-us/flipperc5-firmware>
- 上游项目：<https://github.com/flipperdevices/flipperzero-firmware>
- QQ 交流群：**206149821**
