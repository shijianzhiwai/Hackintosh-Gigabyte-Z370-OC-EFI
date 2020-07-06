# Hackintosh-Gigabyte-Z370-OC-EFI

## OpenCore Hackintosh EFI

OS: macOS Catalina 10.15.5

Motherboards: Gigabyte Z370 AORUS Ultra Gaming

CPU: i7-8700k

Wireless: BCM94360CD

SSD: WD Black 500GB

GPU: Sapphire RX560XT

RAM: G.SKILL 8\*2 G 3200 C16 RGB



## 写在前面

请先阅读 [OpenCore](https://dortania.github.io/OpenCore-Desktop-Guide/) 官方文档，此仓库不是 Hackintosh 教程，只提供安装过程中遇到的问题，以及特殊配置的说明。

## 遇到的问题

### Windows 和 macOS 时间同步问题

如果你安装有双系统，在安装完成 macOS 之后会发现 Windows 系统的时间和 macOS 差几个小时，这是由于 macOS 会把硬件中存储的时间当做 UTC 时间，而 Windows 会当做本地时间。建议修改 Windows 注册表以使将其视作 UTC 时间。

以管理员权限运行 CMD 执行以下命令：

```
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_QWORD /d 1
```

### USB 音响造成不休眠的问题

USB 音频设备可能会造成系统无法进入休眠状态，我的 JBL 小音箱会造成这个问题，无论是 Windows 还是 macOS，要想使系统正确进入休眠状态，只能临时关闭其电源，目前此问题未找到解决方案。

### 连接主板自带的 HDMI 接口可能会造成粉屏、紫屏问题

原因可能是 macOS 将 HDMI 视为 DP 连接，要想解决此问题请尝试在 OpenCore config.plist 此位置中加入如下代码：

DeviceProperties -> Add:

framebuffer-con2-enable = 01000000

framebuffer-con2-type = 00080000

```xml
<key>PciRoot(0x0)/Pci(0x2,0x0)</key>
  <dict>
    <key>AAPL,ig-platform-id</key>
    <data>AACbPg==</data>
    <key>framebuffer-patch-enable</key>
    <data>AQAAAA==</data>
    <key>framebuffer-stolenmem</key>
    <data>AAAwAQ==</data>
    <key>framebuffer-con2-enable</key>
    <data>AQAAAA==</data>
    <key>framebuffer-con2-type</key>
    <data>AAgAAA==</data>
  </dict>
```
[源资料链接](https://www.elitemacx86.com/threads/fix-pink-screen-on-intel-hd-530-540-550-630-640-650-and-uhd-630-on-macos-sierra-and-later.434/)

### USB 映射

我使用的无线是某宝购入的 BCM94360CD 免驱网卡+蓝牙，如果你也是和我相同型号的主板，则可以使用我映射好的 USB 端口，具体连接位置如下：

![主板突破](https://raw.githubusercontent.com/shijianzhiwai/Hackintosh-Gigabyte-Z370-OC-EFI/master/assets/2018111315052465_src.png)

你也可以使用 [Hackintool](https://github.com/headkaze/Hackintool) 和 [USBInjectAll.kext](https://github.com/RehabMan/OS-X-USB-Inject-All) 自行映射。
