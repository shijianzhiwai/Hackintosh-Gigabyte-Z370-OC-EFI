# Hackintosh-Gigabyte-Z370-OC-EFI

## OpenCore Hackintosh EFI

OS: macOS Catalina 10.15.5

Motherboards: Gigabyte Z370 AORUS Ultra Gaming

CPU: i7-8700k

Wireless: BCM94360CD

SSD: WD Black 500GB

GPU: Sapphire RX 560XT

RAM: G.SKILL 8\*2G 3200 C16 RGB



## 写在前面

请先阅读 [OpenCore](https://dortania.github.io/OpenCore-Desktop-Guide/) 官方文档，此仓库不是黑苹果教程，只提供安装过程中遇到的问题，以及特殊配置的说明。

目前由此 EFI 引导的黑苹果运行稳定，系统截图见最后。

文件夹说明

`EFI` OpenCore EFI

`SSDT` 未编译的和已经编译的 SSDT

`assets` 仓库图片资源文件

## 配置说明

由于之前存在英伟达显卡，故机箱上实际上是插着两块显卡的，英伟达显卡在 macOS 下禁用，config.plist 禁用内容如下：
```xml
<key>PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)</key>
  <dict>
    <key>name</key>
    <data>I2Rpc3BsYXk=</data>
    <key>IOName</key>
    <string>#display</string>
    <key>class-code</key>
    <data>/////w==</data>
  </dict>
```
如果你使用请将其删除，如果需要，则请确认你的英伟达显卡 pci 路径将其替换，路径查找方法请参考[显卡禁用](https://dortania.github.io/OpenCore-Desktop-Guide/extras/spoof.html)。

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

[原资料链接](https://www.elitemacx86.com/threads/fix-pink-screen-on-intel-hd-530-540-550-630-640-650-and-uhd-630-on-macos-sierra-and-later.434/)

### USB 映射

我使用的无线是某宝购入的 BCM94360CD 免驱网卡+蓝牙，如果你也是和我相同型号的主板，则可以使用我映射好的 USB 端口，具体连接位置如下：

![主板图片](https://raw.githubusercontent.com/shijianzhiwai/Hackintosh-Gigabyte-Z370-OC-EFI/master/assets/2018111315052465_src.png)

你也可以使用 [Hackintool](https://github.com/headkaze/Hackintool) 和 [USBInjectAll.kext](https://github.com/RehabMan/OS-X-USB-Inject-All) 自行映射。

### 休眠唤醒需要触发两个事件才能正常点亮显示器

请先阅读 OpenCore [此文档](https://dortania.github.io/USB-Map-Guide/misc/keyboard.html)，此解决方法中，第一种方法对此主板不奏效，需要使用第二种伪造 ACPI 设备。问题原因可以参阅此处：[usb-fix](https://osy.gitbook.io/hac-mini-guide/details/usb-fix)。

### 修改主板固件中的 CFG Lock

在较新版本的技嘉 BIOS 中此选项已经被移除，如果需要禁用则需要修改固件，请参阅 OpenCore 中的方法：[MSR-LOCK](https://dortania.github.io/OpenCore-Desktop-Guide/extras/msr-lock.html)。

技嘉主板 BIOS 可以在 Windows 中使用官方工具备份导出（需要先下载安装其 APP Center 软件）。

### Intel® Power Gadget 软件闪退

请查看主板设置中是否启用核心显卡，设置成自动也是不行的，必须是启用状态。

### 开机启动项默认是 Windows

请在 macOS 中 系统偏好设置-> 启动磁盘 选择 macOS 的磁盘重启即可。

### 杂项
由于是之前的机器安装，并不是按照黑苹果的配置来组的机器，所以之前还有一块 RTX2070 显卡，此显卡在 macOS 下已禁用，禁用方法参考：[显卡禁用](https://dortania.github.io/OpenCore-Desktop-Guide/extras/spoof.html)。所以在切回 Windows 时候需要插拔一下显示器连接线到 2070 上。

如果你之前在使用 intel 的无线网卡和蓝牙，请将其拆下，其会导致免驱的苹果网卡无法使用 AirDrop 等功能，主要是蓝牙冲突（应该也可以禁用使其共存，未测试）。

只有部分高端主板自带的 HDMI 接口支持 4k60FPS，如果你是 4k 显示器并且不加装独立显卡需要注意这一点，可以购买免驱独立显卡解决此问题（我是购买的免驱的 RX 560XT 目前最便宜的可以买到全新的 A卡）。

![系统截图](https://raw.githubusercontent.com/shijianzhiwai/Hackintosh-Gigabyte-Z370-OC-EFI/master/assets/1594049859981.jpg)
