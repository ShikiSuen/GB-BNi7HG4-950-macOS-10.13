# GB-BNi7HG4-950-macOS-10.13
---

該倉庫提供用以在 Gigabyte BRIX Gaming UHD GB-BNi7HG4-950 電腦運行 macOS 10.13 High Sierra 所必須的檔案套件。

該倉庫所提供的解決方案可能適用於其姊妹機種（Skylake 平台的 BRIX Pro：GB-BNi7QG4-950, GB-BNi7G4-950）。

鳴謝：`RehabMan`、`Natan Zalkin (kozlek)`、`Darkvoid`、`Laura Müller (Mieze)`、`Pike R. Alpha`。

**請勿將該解決方案用於 macOS 10.12 Sierra**。筆者不建議用該機種運行 macOS 10.12 Sierra、以避免引入過多的第三方修改。

GB-BNi7HG4-950 隨機裝附 Intel 的 Wifi-藍芽模組，且該模組無 macOS 驅動可用。除非您篤定您用不到任何無線功能（比如 Continuity、Wifi、藍芽、AirDrop），否則請換裝 Broadcom BCM94352Z。

另註：該解決方案（包括本文）不涵蓋、不討論任何針對板載音效晶片的啟用方法，只因為筆者用不到（筆者的桌機只可能會使用諸如 NI Komplete Audio 6 這樣的 USB 音訊介面）。如果有誰想啟用板載音效晶片的話，請自行尋找其它解決方案。

##### 要放在 `/Library/Extensions/` 目錄下的 Kext 驅動：

* `BrcmFirmwareRepo.kext` 和 `BrcmPatchRAM2.kext`，源於最新的「[OS-X-BrcmPatchRAM](https://bitbucket.org/RehabMan/os-x-brcmpatchram/downloads/)」封包。

* `FakePCIID_Broadcom_WiFi.kext` 和 `FakePCIID.kext`，源於最新的「[FakePCIID](https://bitbucket.org/RehabMan/os-x-fake-pci-id/downloads/)」封包。

* `Lilu.kext`，可以在 vit9696 維護的 https://github.com/vit9696/Lilu/releases 這個倉庫內找到。

* 最新的 `NvidiaGraphicsFixup.kext_10.13.?_Lag_Fix.zip`，可以在 https://hackintosher.com 找到。


##### 要放在 `/EFI/EFI/Clover/kexts/Other/`目錄下的 Kext 驅動：

* `FakeSMC.kext`，源於最新的「[OS-X-FakeSMC-kozlek](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/)」封包。

* `IntelMausiEthernet.kext`，源於最新的「[OS-X-intel-network](https://bitbucket.org/RehabMan/os-x-intel-network/downloads/)」封包。

* `USBInjectAll.kext`，源於最新的「[OS-X-USB-Inject-All](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/)」封包。

#### Clover 的 KextsToPatch 驅動熱修正

筆者經測試發現該機種不需要任何 Clover 驅動熱修正（KextsToPatch）。筆者的 BRIX 所安裝的 Intel 600p 和 SAMSUNG 950 Pro 可在不經過第三方 NVMe 驅動熱修正的情況下直接被 macOS 10.13 所辨識。之前在 macOS 10.12 系統下，每一次小更新都需要輔以不同的、非常繁瑣的 NVMe 驅動熱修正。

然而，在這種情況下，如果您想以 macOS 10.13 USB 安裝載體來開機的話，您得將 USB 3.0 記憶棒插入機身的紅色 USB 插座、以避免開機失敗（屆時  會顯示為「　⃠」）。該 BRIX（GB-BNi7HG4-950）機身的兩個紅色 USB 插座（其中一個是 Type-C 型）均為基於非 Intel 解決方案的 USB 3.1 插座。其餘三個藍色的 USB 插座均為基於 Intel 解決方案的 USB 3.0 插座，需要專門的 [Clover 熱驅動修正補丁和 SSDT 修正](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist)、以使得插在上面的 macOS 10.13 USB 安裝載體能夠正常開機。

#### SSDT 冷修正檔案與 Clover SSDT 熱修正

###### 1. USB 注入，依 https://github.com/RehabMan/OS-X-USB-Inject-All/

筆者發現在上文提到的 [Clover 熱修正補丁檔案](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist) 當中的 SSDT 熱修正仍舊在起作用、且不使用的話就無法正常開機進入 macOS 10.13（雖然筆者尚未弄清其中是否有不需要的部分）。筆者將這五條 SSDT 熱修正以十六進位明文提供於此、方便各位直接輸入至 Clover Configurator：

* `change _OSI to XOSI`: 將 `5f4f5349 ` 全部換成 `584f5349 `；

* `change EHC1 to EH01`: 將 `45484331 ` 全部換成 `45483031 `；

* `change EHC2 to EH02`: 將 `45484332 ` 全部換成 `45483032 `；

* `change XHCI to XHC`: 將 `58484349 ` 全部換成 `5848435f `；

* `change XHC1 to XHC`: 將 `58484331 ` 全部換成 `5848435f `。

如果您使用了上文提到的 [Clover 熱修正補丁檔案](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist) 當中的 Kext 驅動熱修正的話，則該五條 SSDT 熱修正需要全部套用至您的 Clover 配置檔案才可以。

此外，您還需要將與之對應的 SSDT 冷修正檔案 [SSDT-XOSI.dsl](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/SSDT-XOSI.dsl) 放入 `/EFI/EFI/Clover/ACPI/patched/` 資料夾。

###### 2. 用於 CPU 電源管理的專用 SSDT 冷修正

本文在討論的這台 BRIX Gaming UHD（GB-BNi7HG4-950）的系列及其姊妹機種在 Skylake 世代所用的 CPU 均為 Intel® Core™ i7-6820HQ (對應機種：GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950) 和 Intel® Core™ i5-6300HQ (對應機種：GB-BNi5HG4-950)。這兩款 CPU 均為筆電而設計、擁有 Intel Speed Shift 智能節能技術（俗稱 HWP）。

然而，該技術得與 SMBIOS 當中的模擬指定 Mac 筆電機種彼此對應。

因為 Gigabyte BRIX 是桌上型電腦，筆者沒敢嘗試在 SMBIOS 當中將模擬指定 Mac 機種設定為 MacBook (Pro) 系列。筆者最初測試時所使用的模擬指定機種的是 Skylake 世代的 iMac 5K（`iMac17,1`），畢竟筆者的 BRIX GB-BNi7HG4-950 也是一台採 Skylake 世代的 Intel 晶片組的電腦。該模擬指定機種設定在當時帶來了一些麻煩：筆者不得不在每次系統小更新之後都使用 AGDPFix 修改 `AppleGraphicsDevicePolicy.kext`、以確保視訊得以正常輸出至顯示器。[RehabMan 有與筆者分享過一些意見、提及他在 SMBIOS 設定時對那些提供 NVidia 相容的 Mac 模擬指定機種設定的偏好](https://www.tonymacx86.com/threads/needs-help-on-gigabyte-brix-bni7hg4-950-for-macos-sierra-10-12-1.207471/page-4#post-1377248)，這使得筆者最終決定使用 `iMac14,2` 作為本次的模擬指定機種。

像迄今為止所有 iMac 機種這樣「不支持 HWP 技術」的 SMBIOS 模擬指定機種，都會讓支持 HWP 技術的筆電 CPU 無法正常工作、必須在 Clover 配置檔案當中寫入特定的開機參數 `cpus=X-1` 才可以（X 代指該 CPU 的全部邏輯核心數量之總和）。舉例：筆者的這台 BRIX GB-BNi7HG4-950 內建 Intel® Core™ i7-6820HQ 四核心處理器、且支援 HyperThreading 技術，也就是有八個邏輯核心、對應的開機參數為 `cpus=X-1`（四個物理核心＋三個虛擬核心）。**縱使您在 Clover 配置檔案的 CPU 環節正確配置了諸如 `HWPValue`、`HWPEnable`、`UseARTFrequency` 這樣的設定，只要沒有 `cpus=X-1` 這種開機參數的話、您的 BRIX 仍舊會無法正常開機進入 macOS 10.13 系統（及其安裝程式）**。

> 【囉嗦】筆者手頭沒有 GB-BNi5HG4-950、無法測試這個參數對該機種的作用。但筆者不建議購買 GB-BNi5HG4-950：畢竟其所使用的 Intel® Core™ i5-6300HQ 不支持 HyperThreading 技術、總共只有四個邏輯核心、對應的 `cpus=X-1` 是 `cpus=3`、只可能允許您在 macOS 系統下使用三個物理核心，就顯得很不划算。為了讓錢花得更值，筆者推薦購買採 Core™ i7 處理器的機種（GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950）。

Piker Alpha 有就 CPU 電源管理寫了[專用的 SSDT 冷修正檔案生成腳本 `ssdtPRGen.sh`](https://github.com/Piker-Alpha/ssdtPRGen.sh)。在上文所講的情況下，該腳本檢測到當前系統的 SMBIOS 模擬指定機種所處的 Intel 晶片組世代與實際的 CPU 所處的 Intel 晶片組世代不一致、會在那裡鬼打牆。好在筆者一開始以 `iMac17,1` 作為 SMBIOS 模擬指定機種進行測試時、有利用該腳本生成了對應的 SSDT 冷修正檔案（僅適用於 GB-BNi7QG4-950、GB-BNi7G4-950 和 my GB-BNi7HG4-950）。該檔案在敝倉庫的位置是：`/EFIVolume/EFI/Clover/ACPI/patched/SSDT.aml`，請將其下載為 RAW 格式、直接置入 `/EFI/EFI/Clover/ACPI/patched/` 這個目錄下。

如果您想知道怎樣使用該腳本生成用於電源管理的 SSDT 冷修正檔案的話，請洽：https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.146870/

#### Clover 配置檔案

本部分將會以 [Clover Configurator](http://mackie100projects.altervista.org/download-clover-configurator/) 為工具（建議至少 4.52 版）來說明一個新建的空白 Clover 配置檔案所應該添入的內容：

###### 1. ACPI（進階配備與電源介面）

* 給 `FixShutdown_0004` 打鉤。

* 將上文「1. USB 注入」部分所列出的 SSDT 熱修正填進來。

###### 2. BOOT（開機項）

* 開機參數（Boot Arguments）

  - `dart=0`  

  - `cpus=7` （cpus = "CPU 邏輯核心總數" 減去 1） # 不得省略，哪怕在 CPU 設定內啟用了 Skylake 專有設定。

* 將 Legacy 設為 `PBR`。

###### 3. CPU

* Skylake Settings（Skylake 專有設定）

  - 給 `HWPEnable` 和 `UseARTFrequency` 這兩項打鉤。

  - CPU Type 應設為 `0x0905`。

  - HWPValue 的數值應設為 `0x00002401`（最強效能），該設定適合將 i7-6820HQ 用於桌上型電腦的情形。 

  - 關於 HWPValue 數值的取值方法，請洽：https://www.tonymacx86.com/threads/skylake-hwp-enable.214915/

###### 4. Devices（設備）

* 在 USB 窗格下，給 `FixOwnership` 打鉤。

* Audio 設定為 `Detect`。

###### 5. GUI （非必需，視具體需求而定）

* Scan 窗格

  - 先點選 `Custom`，再給 `Entries` 和 `Tool` 打鉤。
  
* Hide Volume（隱藏卷宗）

  - 如果這台電腦有以 EFI 模式安裝並開機運作的 Windows 系統的話，您可以在此隱藏 Windows 所在的磁碟分割、且今後直接在 Clover 當中藉由「Microsoft EFI」這個開機項來進入 Windows。

###### 6. Kernel and Kext Patches （系統內核與 Kext 熱修正）

* 給 `AppleRTC` 和 `KernelPm` 打鉤。

* 有關 `KextsToPatch`，請閱讀上文「Clover 的 KextsToPatch 驅動熱修正」章節。

###### 7. System Parameters（系統參數）

* 給 `Inject System ID` 和 `NvidiaWeb` 打鉤。

  - `NvidiaWeb` 這一條在安裝 macOS 時完全派不上用場，但卻不會妨害 macOS 系統的安裝流程。當且僅當與您所安裝的 macOS 系統的組建編號對應的 NVidia Web Driver 驅動程式的情況下，這一條才會生效。
  - 您有義務在對系統組建進行更新之前（比如說 macOS 的小版本更新）確保您已經拿到了正確的 NVidia Web Driver 驅動程式的安裝包。每個版本的 NVidia Web Driver 驅動程式安裝包都只對應某一個 macOS 組建。

###### 8. SMBIOS （請在完成這一步之前不要考慮下一步「Rt Variables」）

1. 開啟終端機、執行「uuidgen」指令、記下其所反饋的 UUID。這將會是您這台電腦的 `系統唯一辨識碼（SmUUID）`。但您現在應該不想急著填入 Clover Configurator 的 SMBIOS 畫面內，不然您之後恐怕會不得不重新填一次。

2. 在 Clover Configurator 的 SMBIOS 畫面內的右側，應該有個大問號圖樣、或者某個具體的某款 Mac 電腦產品的圖樣（取決於已經在該窗格當中填寫的 SMBIOS 資訊），在該圖樣的右下角應該有個按鈕（裡面有兩個箭頭，分別指上、指下）。點摁該按鈕之後，在上下文選單當中選取正確的模擬指定機種（這裡選擇 `iMac14,2`）。只要您的 Clover Configurator 版本沒過期的話，在您點選模擬指定機種的那一瞬間，Clover Configurator 就已經自動隨機生成了一套 SMBIOS 辨識碼組，其中包含了「主機板辨識碼 `Board-ID (BID)`」、「主機板序號 `Board Serial Number (MLB)`」和「產品序號 `Serial Number (Product Serial Number, PSN)`」這三碼。您可以隨時摁下「Generate New」按鈕來另行隨機生成一組新的 SMBIOS 辨識碼組。

  - 您得意識到：直接挪用一台正品 Mac 電腦的 SMBIOS 辨識碼組和/或 SmUUID 的行為，就是對這台正品 Mac 電腦的所有者的法定權益（使用、享受與 Apple ID 有關的服務）的侵害、且會導致某些災難性的問題：這台正品 Mac 電腦可能會被 Apple 列入黑名單、且不接受任何申訴；只要是在使用其 SMBIOS 辨識碼組和/或 SmUUID 的 mac 兼容機上使用過的 Apple ID，都會有同樣被停權的風險。類似的問題也會出現在「共用同一組 SMBIOS 辨識碼組和 SmUUID 的多台 mac 兼容機」的情形下。
  
  - 請造訪 https://everymac.com/ultimate-mac-lookup/ 網站、使用您的產品序號（PSN）來看看該網站所反饋的結果是否與您的 SMBIOS 模擬指定機種互相重合。**如果不重合、或者返回其它「認為該產品序號有誤」的結果的話，請返回 Clover Configurator 再生成一組新的 SMBIOS 辨識碼組來測試**。
  
  - 請在之後造訪 https://checkcoverage.apple.com/ 網站、使用您的產品序號（PSN）來看看該網站能否據此序號查出一款具體的 Mac 電腦的保固資訊。**如果有查出的話，這一組 SMBIOS 辨識碼組都不要用了、請返回 Clover Configurator 再生成一組新的 SMBIOS 辨識碼組、來重複這兩道測試**。當且僅當該網站的反饋結果為「We're sorry, but this serial number is not valid. Please check your information and try again.」時，一套 SMBIOS 辨識碼組的可用性才算測試通過、可以投入使用了。
  
3. 切換回 Clover Configurator 的畫面，將剛才藉由終端指令「uuidgen」生成的 SmUUID 填入到對應的窗格內。

4. 請確認 Clover Configurator 是否已經自動幫您填好韌體五項： `Firmware Features`、`Firmware Features Mask`、`Platform Feature`、`Bios Version`、`Bios Release Date`。（這一條對 macOS 10.13 開始的系統而言至關重要，在之前都是不痛不癢的東西。）

  - 用於韌體五項的正確參數因 SMBIOS 模擬指定機種、及該機種對應的 Apple 官方最新的韌體更新而異。
  
  - 如果您在重複利用之前的 Clover 配置檔案、且該檔案使用相同的 SMBIOS 模擬指定機種的話，請用最新版的 Clover Configurator 新開一個檔案、點開 SMBIOS 畫面、選取相同的模擬指定機種、將該畫面內自動生成的韌體五項的資料手動轉移至您自己的 Clover 配置檔案當中。

###### 9. Rt Variables （Runtime Variables，執行階段變數）

* BooterConfig 設為 `0x28`

* CsrActiveConfig 設為 `0x67`

* 論及 MLB 這一項，請直接填入您在 SMBIOS 步驟所最終確定的主機板序號（MLB）。

* 有關 ROM 這一項，請點擊「from SMBIOS」再點「Generate」按鈕。只要您 SMBIOS 組態設定是正確的，您就可以在這裡成功生成新的 MAC 位址。

  如果您十分確信您的電腦的物理 MAC 位址（無論是有線網路還是無線網路）都沒有曝露給其它 mac 兼容机使用者使用（用在 Clover 配置檔案內）的話，您可以將 ROM 這一項留空、讓 Clover 直接讀取其物理 MAC 位址。
  
#### 用於 Clover 的 EFI 驅動

對 macOS 10.13 而言，您不需要「HFSPlus.efi」，除非您打算將系統安裝至 HFS+ 卷宗、且不執行 APFS 轉換。您可以隨時在某個已經安裝好 macOS 10.13 系統的卷宗內的 `/usr/standalone/i386/` 目錄下拿到「apfs.efi」這個檔案。

與此同時，您還需要「OsxAptioFixDrv-64.efi」這個檔案（請注意拼寫）。

在筆者目前的 Clover 組態下，`/EFI/EFI/Clover/driver64UEFI/` 目錄內有著如下檔案、且筆者也記不清究竟哪些是派不上用場的： `apfs.efi` `AppleImageCodec-64.efi` `AppleKeyAggregator-64.efi` 
`AppleUITheme-64.efi` `DataHubDxe-64.efi` `EmuVariableUefi-64.efi` `Fat-64.efi` `FirmwareVolume-64.efi` `FSInject-64.efi` `HFSPlus.efi` `OsxAptioFixDrv-64.efi` `OsxFatBinaryDrv-64.efi` `SMCHelper-64.efi`。

$ EOF.
