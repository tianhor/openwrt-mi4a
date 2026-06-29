# 小米路由器 4A 千兆版 OpenWrt 定制化开发指南

你好！本项目旨在为你提供**小米路由器 4A 千兆版 (Xiaomi Mi Router 4A Gigabit Edition)** 的 OpenWrt 定制和开发环境。

## 硬件确认
首先纠正一个小细节：小米 4A 千兆版使用的核心芯片是 **联发科 MT7621A**（你提到的 7921 可能是笔误，MT7621 是非常经典的一代神U，适合刷 OpenWrt）。

## 如何编译你的专属系统？

由于在 Windows 上直接编译 OpenWrt 极其复杂（需要配置大量的 Linux 交叉编译工具链），目前主流且最优雅的“开发”方式是利用 **GitHub Actions** 进行云端编译。我已经为你配置好了全套代码。

### 使用步骤：
1. **上传到 GitHub**：
   将此文件夹内的所有内容（`.github` 文件夹和 `mi4a.config`）作为一个新的仓库推送到你的 GitHub。
2. **触发编译**：
   进入 GitHub 仓库的 `Actions` 页面，同意开启 Actions，云端服务器便会自动开始为你拉取源码并编译系统。
3. **定制功能**：
   如果你需要添加特定的功能（如游戏加速、广告屏蔽等），只需要修改 `mi4a.config` 文件，添加相应的 `CONFIG_PACKAGE_luci-app-xxx=y` 即可。修改后推送到 GitHub 会再次自动触发编译。
4. **下载固件**：
   编译完成后（通常需要1-2小时），在 Actions 运行记录的底部，可以下载到名为 `OpenWrt_firmware` 的压缩包，里面包含了 `sysupgrade.bin` 刷机文件。

## 如何将系统刷入路由器？

小米路由器默认锁定了 SSH 权限，因此不能直接通过网页上传第三方固件。你需要利用一个叫 **OpenWrtInvasion** 的漏洞脚本来破解。

### 刷机流程概述：
1. **降级官方固件**：如果你的官方固件版本较新，可能修补了漏洞。建议先在小米后台降级到 `2.28.62` 等旧版固件。
2. **使用 OpenWrtInvasion**：
   - 电脑连接路由器。
   - 运行 [OpenWrtInvasion 脚本](https://github.com/acecilia/OpenWrtInvasion)。
   - 输入路由器的 IP 加上 `stok`（在小米路由管理页面的网址中可以找到这个 token）。
   - 脚本会自动为你开启 telnet/SSH 甚至直接提供 FTP，然后你可以将编译好的 `xxx-sysupgrade.bin` 上传到路由器的 `/tmp` 目录。
3. **刷入固件**：
   通过 SSH 登录路由器，运行命令：
   ```bash
   mtd -e OS1 -r write /tmp/你的固件名称.bin OS1
   ```
   *（注：具体命令请参考 OpenWrt 官方针对该型号的 WiKi，不同版本可能细微差异，通常是写入 OS1 甚至 firmware 分区。）*

准备好之后，我们就可以开始你的 OpenWrt 开发之旅了！如果你有特定想要实现的功能模块，我可以帮你补充到 config 中。
