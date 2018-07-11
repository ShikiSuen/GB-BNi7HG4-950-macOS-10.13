# GB-BNi7HG4-950-macOS-10.13
---

该仓库提供用以在 Gigabyte BRIX Gaming UHD GB-BNi7HG4-950 电脑运行 macOS 10.13 High Sierra 所必须的档案套件。

该仓库所提供的解决方案可能适用于其姐妹机种（Skylake 平台的 BRIX Pro：GB-BNi7QG4-950, GB-BNi7G4-950）。

鸣谢：`RehabMan`、`Natan Zalkin (kozlek)`、`Darkvoid`、`Laura Müller (Mieze)`、`Pike R. Alpha`。

**请勿将该解决方案用于 macOS 10.12 Sierra**。笔者不建议用该机种运行 macOS 10.12 Sierra、以避免引入过多的第三方修改。

GB-BNi7HG4-950 随机装附 Intel 的 Wifi-蓝芽模组，且该模组无 macOS 驱动可用。除非您笃定您用不到任何无线功能（比如 Continuity、Wifi、蓝芽、AirDrop），否则请换装 Broadcom BCM94352Z。

另注：该解决方案（包括本文）不涵盖、不讨论任何针对板载音效晶片的启用方法，只因为笔者用不到（笔者的桌机只可能会使用诸如 NI Komplete Audio 6 这样的 USB 音讯介面）。如果有谁想启用板载音效晶片的话，请自行寻找其它解决方案。

##### 要放在 `/Library/Extensions/` 目录下的 Kext 驱动：

* `BrcmFirmwareRepo.kext` 和 `BrcmPatchRAM2.kext`，源于最新的「[OS-X-BrcmPatchRAM](https://bitbucket.org/RehabMan/os-x-brcmpatchram/downloads/)」封包。

* `FakePCIID_Broadcom_WiFi.kext` 和 `FakePCIID.kext`，源于最新的「[FakePCIID](https://bitbucket.org/RehabMan/os-x-fake-pci-id/downloads/)」封包。

* `Lilu.kext`，可以在 vit9696 维护的 https://github.com/vit9696/Lilu/releases 这个仓库内找到。

* 最新的 `NvidiaGraphicsFixup.kext_10.13.?_Lag_Fix.zip`，可以在 https://hackintosher.com 找到。


##### 要放在 `/EFI/EFI/Clover/kexts/Other/`目录下的 Kext 驱动：

* `FakeSMC.kext`，源于最新的「[OS-X-FakeSMC-kozlek](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/)」封包。

* `IntelMausiEthernet.kext`，源于最新的「[OS-X-intel-network](https://bitbucket.org/RehabMan/os-x-intel-network/downloads/)」封包。

* `USBInjectAll.kext`，源于最新的「[OS-X-USB-Inject-All](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/)」封包。

#### Clover 的 KextsToPatch 驱动热修正

笔者经测试发现该机种不需要任何 Clover 驱动热修正（KextsToPatch）。笔者的 BRIX 所安装的 Intel 600p 和 SAMSUNG 950 Pro 可在不经过第三方 NVMe 驱动热修正的情况下直接被 macOS 10.13 所辨识。之前在 macOS 10.12 系统下，每一次小更新都需要辅以不同的、非常繁琐的 NVMe 驱动热修正。

然而，在这种情况下，如果您想以 macOS 10.13 USB 安装载体来开机的话，您得将 USB 3.0 记忆棒插入机身的红色 USB 插座、以避免开机失败（届时  会显示为「　⃠」）。该 BRIX（GB-BNi7HG4-950）机身的两个红色 USB 插座（其中一个是 Type-C 型）均为基于非 Intel 解决方案的 USB 3.1 插座。其余三个蓝色的 USB 插座均为基于 Intel 解决方案的 USB 3.0 插座，需要专门的 [Clover 热驱动修正补丁和 SSDT 修正](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist)、以使得插在上面的 macOS 10.13 USB 安装载体能够正常开机。

#### SSDT 冷修正档案与 Clover SSDT 热修正

###### 1. USB 注入，依 https://github.com/RehabMan/OS-X-USB-Inject-All/

笔者发现在上文提到的 [Clover 热修正补丁档案](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist) 当中的 SSDT 热修正仍旧在起作用、且不使用的话就无法正常开机进入 macOS 10.13（虽然笔者尚未弄清其中是否有不需要的部分）。笔者将这五条 SSDT 热修正以十六进位明文提供于此、方便各位直接输入至 Clover Configurator：

* `change _OSI to XOSI`: 将 `5f4f5349 ` 全部换成 `584f5349 `；

* `change EHC1 to EH01`: 将 `45484331 ` 全部换成 `45483031 `；

* `change EHC2 to EH02`: 将 `45484332 ` 全部换成 `45483032 `；

* `change XHCI to XHC`: 将 `58484349 ` 全部换成 `5848435f `；

* `change XHC1 to XHC`: 将 `58484331 ` 全部换成 `5848435f `。

如果您使用了上文提到的 [Clover 热修正补丁档案](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist) 当中的 Kext 驱动热修正的话，则该五条 SSDT 热修正需要全部套用至您的 Clover 配置档案才可以。

此外，您还需要将与之对应的 SSDT 冷修正档案 [SSDT-XOSI.dsl](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/SSDT-XOSI.dsl) 放入 `/EFI/EFI/Clover/ACPI/patched/` 资料夹。

###### 2. 用于 CPU 电源管理的专用 SSDT 冷修正

本文在讨论的这台 BRIX Gaming UHD（GB-BNi7HG4-950）的系列及其姐妹机种在 Skylake 世代所用的 CPU 均为 Intel® Core™ i7-6820HQ (对应机种：GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950) 和 Intel® Core™ i5-6300HQ (对应机种：GB-BNi5HG4-950)。这两款 CPU 均为笔电而设计、拥有 Intel Speed Shift 智能节能技术（俗称 HWP）。

然而，该技术得与 SMBIOS 当中的模拟指定 Mac 笔电机种彼此对应。

因为 Gigabyte BRIX 是桌上型电脑，笔者没敢尝试在 SMBIOS 当中将模拟指定 Mac 机种设定为 MacBook (Pro) 系列。笔者最初测试时所使用的模拟指定机种的是 Skylake 世代的 iMac 5K（`iMac17,1`），毕竟笔者的 BRIX GB-BNi7HG4-950 也是一台基于 Skylake 世代的 Intel 晶片组的电脑。该模拟指定机种设定在当时带来了一些麻烦：笔者不得不在每次系统小更新之后都使用 AGDPFix 修改 `AppleGraphicsDevicePolicy.kext`、以确保视讯得以正常输出至显示器。[RehabMan 有与笔者分享过一些意见、提及他在 SMBIOS 设定时对那些提供 NVidia 相容的 Mac 模拟指定机种设定的偏好](https://www.tonymacx86.com/threads/needs-help-on-gigabyte-brix-bni7hg4-950-for-macos-sierra-10-12-1.207471/page-4#post-1377248)，这使得笔者最终决定使用 `iMac14,2` 作为本次的模拟指定机种。

像迄今为止所有 iMac 机种这样「不支持 HWP 技术」的 SMBIOS 模拟指定机种，都会让支持 HWP 技术的笔电 CPU 无法正常工作、必须在 Clover 配置档案当中写入特定的开机参数 `cpus=X-1` 才可以（X 代指该 CPU 的全部逻辑核心数量之总和）。举例：笔者的这台 BRIX GB-BNi7HG4-950 内建 Intel® Core™ i7-6820HQ 四核心处理器、且支援 HyperThreading 技术，也就是有八个逻辑核心、对应的开机参数为 `cpus=X-1`（四个物理核心＋三个虚拟核心）。**纵使您在 Clover 配置档案的 CPU 环节正确配置了诸如 `HWPValue`、`HWPEnable`、`UseARTFrequency` 这样的设定，只要没有 `cpus=X-1` 这种开机参数的话、您的 BRIX 仍旧会无法正常开机进入 macOS 10.13 系统（及其安装程式）**。

> 【啰嗦】笔者手头没有 GB-BNi5HG4-950、无法测试这个参数对该机种的作用。但笔者不建议购买 GB-BNi5HG4-950：毕竟其所使用的 Intel® Core™ i5-6300HQ 不支持 HyperThreading 技术、总共只有四个逻辑核心、对应的 `cpus=X-1` 是 `cpus=3`、只可能允许您在 macOS 系统下使用三个物理核心，就显得很不划算。为了让钱花得更值，笔者推荐购买基于 Core™ i7 处理器的机种（GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950）。

Piker Alpha 有就 CPU 电源管理写了[专用的 SSDT 冷修正档案生成脚本 `ssdtPRGen.sh`](https://github.com/Piker-Alpha/ssdtPRGen.sh)。在上文所讲的情况下，该脚本检测到当前系统的 SMBIOS 模拟指定机种所处的 Intel 晶片组世代与实际的 CPU 所处的 Intel 晶片组世代不一致、会在那里鬼打墙。好在笔者一开始以 `iMac17,1` 作为 SMBIOS 模拟指定机种进行测试时、有利用该脚本生成了对应的 SSDT 冷修正档案（仅适用于 GB-BNi7QG4-950、GB-BNi7G4-950 和 my GB-BNi7HG4-950）。该档案在敝仓库的位置是：`/EFIVolume/EFI/Clover/ACPI/patched/SSDT.aml`，请将其下载为 RAW 格式、直接置入 `/EFI/EFI/Clover/ACPI/patched/` 这个目录下。

如果您想知道怎样使用该脚本生成用于电源管理的 SSDT 冷修正档案的话，请洽：https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.146870/

#### Clover 配置档案

本部分将会以 [Clover Configurator](http://mackie100projects.altervista.org/download-clover-configurator/) 为工具（建议至少 4.52 版）来说明一个新建的空白 Clover 配置档案所应该添入的内容：

###### 1. ACPI（进阶配备与电源介面）

* 给 `FixShutdown_0004` 打钩。

* 将上文「1. USB 注入」部分所列出的 SSDT 热修正填进来。

###### 2. BOOT（开机项）

* 开机参数（Boot Arguments）

  - `dart=0`  

  - `cpus=7` （cpus = "CPU 逻辑核心总数" 减去 1） # 不得省略，哪怕在 CPU 设定内启用了 Skylake 专有设定。

* 将 Legacy 设为 `PBR`。

###### 3. CPU

* Skylake Settings（Skylake 专有设定）

  - 给 `HWPEnable` 和 `UseARTFrequency` 这两项打钩。

  - CPU Type 應設為 `0x0905`。

  - HWPValue 的数值应设为 `0x00002401`（最强效能），该设定适合将 i7-6820HQ 用于桌上型电脑的情形。 

  - 关于 HWPValue 数值的取值方法，请洽：https://www.tonymacx86.com/threads/skylake-hwp-enable.214915/

###### 4. Devices（设备）

* 在 USB 窗格下，给 `FixOwnership` 打钩。

* Audio 设定为 `Detect`。

###### 5. GUI （非必需，视具体需求而定）

* Scan 窗格

  - 先点选 `Custom`，再给 `Entries` 和 `Tool` 打钩。
  
* Hide Volume（隐藏卷宗）

  - 如果这台电脑有以 EFI 模式安装并开机运作的 Windows 系统的话，您可以在此隐藏 Windows 所在的磁碟分割、且今后直接在 Clover 当中借由「Microsoft EFI」这个开机项来进入 Windows。

###### 6. Kernel and Kext Patches （系统内核与 Kext 热修正）

* 给 `AppleRTC` 和 `KernelPm` 打钩。

* 有关 `KextsToPatch`，请阅读上文「Clover 的 KextsToPatch 驱动热修正」章节。

###### 7. System Parameters（系统参数）

* 给 `Inject System ID` 和 `NvidiaWeb` 打钩。

  - `NvidiaWeb` 这一条在安装 macOS 时完全派不上用场，但却不会妨害 macOS 系统的安装流程。当且仅当与您所安装的 macOS 系统的组建编号对应的 NVidia Web Driver 驱动程式的情况下，这一条才会生效。
  - 您有义务在对系统组建进行更新之前（比如说 macOS 的小版本更新）确保您已经拿到了正确的 NVidia Web Driver 驱动程式的安装包。每个版本的 NVidia Web Driver 驱动程式安装包都只对应某一个 macOS 组建。

###### 8. SMBIOS （请在完成这一步之前不要考虑下一步「Rt Variables」）

1. 开启终端机、执行「uuidgen」指令、记下其所反馈的 UUID。这将会是您这台电脑的 `系统唯一辨识码（SmUUID）`。但您现在应该不想急着填入 Clover Configurator 的 SMBIOS 画面内，不然您之后恐怕会不得不重新填一次。

2. 在 Clover Configurator 的 SMBIOS 画面内的右侧，应该有个大问号图样、或者某个具体的某款 Mac 电脑产品的图样（取决于已经在该窗格当中填写的 SMBIOS 资讯），在该图样的右下角应该有个按钮（里面有两个箭头，分别指上、指下）。点摁该按钮之后，在上下文选单当中选取正确的模拟指定机种（这里选择 `iMac14,2`）。只要您的 Clover Configurator 版本没过期的话，在您点选模拟指定机种的那一瞬间，Clover Configurator 就已经自动随机生成了一套 SMBIOS 辨识码组，其中包含了「主机板辨识码 `Board-ID (BID)`」、「主机板序号 `Board Serial Number (MLB)`」和「产品序号 `Serial Number (Product Serial Number, PSN)`」这三码。您可以随时摁下「Generate New」按钮来另行随机生成一组新的 SMBIOS 辨识码组。

  - 您得意识到：直接挪用一台正品 Mac 电脑的 SMBIOS 辨识码组和/或 SmUUID 的行为，就是对这台正品 Mac 电脑的所有者的法定权益（使用、享受与 Apple ID 有关的服务）的侵害、且会导致某些灾难性的问题：这台正品 Mac 电脑可能会被 Apple 列入黑名单、且不接受任何申诉；只要是在使用其 SMBIOS 辨识码组和/或 SmUUID 的 mac 兼容机上使用过的 Apple ID，都会有同样被停权的风险。类似的问题也会出现在「共用同一组 SMBIOS 辨识码组和 SmUUID 的多台 mac 兼容机」的情形下。
  
  - 请造访 https://everymac.com/ultimate-mac-lookup/ 网站、使用您的产品序号（PSN）来看看该网站所反馈的结果是否与您的 SMBIOS 模拟指定机种互相重合。**如果不重合、或者返回其它「认为该产品序号有误」的结果的话，请返回 Clover Configurator 再生成一组新的 SMBIOS 辨识码组来测试**。
  
  - 请在之后造访 https://checkcoverage.apple.com/ 网站、使用您的产品序号（PSN）来看看该网站能否据此序号查出一款具体的 Mac 电脑的保固资讯。**如果有查出的话，这一组 SMBIOS 辨识码组都不要用了、请返回 Clover Configurator 再生成一组新的 SMBIOS 辨识码组、来重复这两道测试**。当且仅当该网站的反馈结果为「We're sorry, but this serial number is not valid. Please check your information and try again.」时，一套 SMBIOS 辨识码组的可用性才算测试通过、可以投入使用了。
  
3. 切换回 Clover Configurator 的画面，将刚才借由终端指令「uuidgen」生成的 SmUUID 填入到对应的窗格内。

4. 请确认 Clover Configurator 是否已经自动帮您填好韧体五项： `Firmware Features`、`Firmware Features Mask`、`Platform Feature`、`Bios Version`、`Bios Release Date`。（这一条对 macOS 10.13 开始的系统而言至关重要，在之前都是不痛不痒的东西。）

  - 用于韧体五项的正确参数因 SMBIOS 模拟指定机种、及该机种对应的 Apple 官方最新的韧体更新而异。
  
  - 如果您在重复利用之前的 Clover 配置档案、且该档案使用相同的 SMBIOS 模拟指定机种的话，请用最新版的 Clover Configurator 新开一个档案、点开 SMBIOS 画面、选取相同的模拟指定机种、将该画面内自动生成的韧体五项的资料手动转移至您自己的 Clover 配置档案当中。

###### 9. Rt Variables （Runtime Variables，执行阶段变数）

* BooterConfig 设为 `0x28`

* CsrActiveConfig 设为 `0x67`

* 论及 MLB 这一项，请直接填入您在 SMBIOS 步骤所最终确定的主机板序号（MLB）。

* 有关 ROM 这一项，请点击「from SMBIOS」再点「Generate」按钮。只要您 SMBIOS 组态设定是正确的，您就可以在这里成功生成新的 MAC 位址。

  如果您十分确信您的电脑的物理 MAC 位址（无论是有线网路还是无线网路）都没有曝露给其它 mac 兼容机用户使用（用在 Clover 配置档案内）的话，您可以将 ROM 这一项留空、让 Clover 直接读取其物理 MAC 位址。
  
#### 用于 Clover 的 EFI 驱动

对 macOS 10.13 而言，您不需要「HFSPlus.efi」，除非您打算将系统安装至 HFS+ 卷宗、且不执行 APFS 转换。您可以随时在某个已经安装好 macOS 10.13 系统的卷宗内的 `/usr/standalone/i386/` 目录下拿到「apfs.efi」这个档案。

与此同时，您还需要「OsxAptioFixDrv-64.efi」这个档案（请注意拼写）。

在笔者目前的 Clover 组态下，`/EFI/EFI/Clover/driver64UEFI/` 目录内有着如下档案、且笔者也记不清究竟哪些是派不上用场的： `apfs.efi` `AppleImageCodec-64.efi` `AppleKeyAggregator-64.efi` 
`AppleUITheme-64.efi` `DataHubDxe-64.efi` `EmuVariableUefi-64.efi` `Fat-64.efi` `FirmwareVolume-64.efi` `FSInject-64.efi` `HFSPlus.efi` `OsxAptioFixDrv-64.efi` `OsxFatBinaryDrv-64.efi` `SMCHelper-64.efi`。

$ EOF.
