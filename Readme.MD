# GB-BNi7HG4-950-macOS-10.13
---

This repository provides files necessary for running macOS 10.13 High Sierra on GB-BNi7HG4-950 only.

This solution may apply to its Skylake siblings of BRIX Pro models (GB-BNi7QG4-950, GB-BNi7G4-950).

Credits to `RehabMan`, `Natan Zalkin (kozlek)`, `Darkvoid`, `Laura Müller (Mieze)`, `Pike R. Alpha`.

**Do not try the solution provided here for macOS 10.12 Sierra.** I do not recommend installing macOS 10.12 on this BRIX model simply due to too many hassles and patches.

Note that the GB-BNi7HG4-950 ships with an Intel Wifi-BT module and you MUST use Broadcom BCM94352Z to replace it UNLESS you don't want to use wireless features such as Continuity, WIFI, Bluetooth, and AirDrop.

Also note that I do only use USB audio interfaces (mine is Native Instruments Komplete Audio 6) on my desktop computers, leading to my lack of the solution on how to enable the built-in audio of this BRIX. Thus, this repo (incl. this article) does not count the onboard audio. If anyone wants to enable the onboard audio, please check solutions from others.

##### Kexts used in `/Library/Extensions/`:

* `BrcmFirmwareRepo.kext` and `BrcmPatchRAM2.kext` from the **latest** OS-X-BrcmPatchRAM package downloadable via https://bitbucket.org/RehabMan/os-x-brcmpatchram/downloads/ , maintained by RehabMan.

* `FakePCIID_Broadcom_WiFi.kext` and `FakePCIID.kext` from the **latest** FakePCIID package downloadable via https://bitbucket.org/RehabMan/os-x-fake-pci-id/downloads/ , maintained by RehabMan.

* `Lilu.kext` from the **latest** Lilu package downloadable via https://github.com/vit9696/Lilu/releases , maintained by vit9696.

* The Latest `NvidiaGraphicsFixup.kext_10.13.?_Lag_Fix.zip` from https://hackintosher.com website.

##### Kexts used in `/EFI/EFI/Clover/kexts/Other/`:

* `FakeSMC.kext` from the **latest** OS-X-FakeSMC-kozlek package downloadable via https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/ , maintained by RehabMan.

* `IntelMausiEthernet.kext` from the **latest** OS-X-intel-network package downloadable via https://bitbucket.org/RehabMan/os-x-intel-network/downloads/ , maintained by RehabMan.

* `USBInjectAll.kext` from the **latest** OS-X-USB-Inject-All package downloadable via https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/ , maintained by RehabMan.

#### Clover KextsToPatch

No KextsToPatch required according to my tests. My BRIX running macOS 10.13 could use Intel 600p and SAMSUNG 950 Pro out of the box without those complicated Clover KextsToPatch entries I used with macOS 10.12 Sierra.

HOWEVER, in this case, If you want to boot from macOS 10.13 USB installer, you have to plug your USB 3.0 memory stick to the **red** USB sockets on this BRIX. Otherwise, your computer may fail in booting into the macOS 10.13 Installer (you will get a STOP sign instead). AFAIK the two red USB sockets are USB 3.1 sockets which work through a non-Intel solution. If you want to plug and boot macOS 10.13 USB installer from Intel-driven USB sockets (the three blue sockets on this BRIX), you may have to use the Clover KextsToPatch and SSDT-Patches from https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist (though I don't have time to test this patch with these sockets at this moment, theoretically this should work) together with the following important SSDT patches applied.

#### Clover SSDT Hot Patch and SSDT Patch files

###### 1. USB Injection (from https://github.com/RehabMan/OS-X-USB-Inject-All/)

I found that those SSDT Hot Patches from https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/config_patches.plist are still necessary to boot this BRIX into macOS 10.13 (though I don't know whether all of them are crucial or just some of them). I duplicated these Clover SSDT Hot Patches here in HEX format for your convenience if you prefer to put the patches directly into Clover Configurator:

* `change _OSI to XOSI`: Let all `5f4f5349 ` be replaced by `584f5349 `.

* `change EHC1 to EH01`: Let all `45484331 ` be replaced by `45483031 `.

* `change EHC2 to EH02`: Let all `45484332 ` be replaced by `45483032 `.

* `change XHCI to XHC`: Let all `58484349 ` be replaced by `5848435f `.

* `change XHC1 to XHC`: Let all `58484331 ` be replaced by `5848435f `.

Note that you have to use all these patches if you use the KextsToPatch content from the same plist consulted above.

Also, you have to put the file https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/SSDT-XOSI.dsl into `/EFI/EFI/Clover/ACPI/patched/` which is necessary to make sure the SSDT Hot Patches above work correctly.

###### 2. SSDT for Power Management

The CPU of this BRIX (GB-BNi7HG4-950) and its siblings in the same Skylake Generation all uses Intel® Core™ i7-6820HQ (GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950) and Intel® Core™ i5-6300HQ (GB-BNi5HG4-950). These two CPUs are laptop processors and have Intel Speed Shift Technology (HWP) introduced, saving power efficiently.

However, this has to match certain MacBook (Pro) models in your SMBIOS configuration.

Since BRIX computers are desktop computers, I hesitate to try models other than iMac or Mac Pro. I initially used the iMac 5k Skylake `iMac17,1` as the model in my SMBIOS profile (because my BRIX GB-BNi7HG4-950 is a Skylake computer), leading to a hassle that I have to use AGDPFix to patch `AppleGraphicsDevicePolicy.kext` (which gets constantly refreshed by minor system updates) to make sure video signals get to my monitor successfully. [RehabMan shared me his opinion that he prefers to use NVidia-compatible Mac models in the SMBIOS configuration](https://www.tonymacx86.com/threads/needs-help-on-gigabyte-brix-bni7hg4-950-for-macos-sierra-10-12-1.207471/page-4#post-1377248), leading to my decision of using `iMac14,2` instead in this case.

One of the common disadvantages of all HWP-unsupported mac models (like all iMac models) in SMBIOS at this moment is that you won't get the Intel Speed Shift work correctly and you have to use special boot arguments: To ensure your BRIX could successfully boot into macOS and its installer with SMBIOS configured as iMac models, you must use `cpus=X-1` (X = the number of logical cores of your CPU) in the boot arguments of your Clover configuration file. For instance: this BRIX (GB-BNi7HG4-950) has Intel® Core™ i7-6820HQ which has 8 logical cores, and your boot argument shall be `cpus=7' (4 physical cores + 3 virtual cores). **This step is necessary even if you have enabled correct Skylake settings in your Clover configuration file** (e.g. HWPValue, HWPEnable, UseARTFrequency).

> **Note** that I have no GB-BNi5HG4-950 with me to test whether this works on it, and I do discourage people from buying GB-BNi5HG4-950 because you could only be able to use 3 physical cores of Intel® Core™ i5-6300HQ (a quad-core processor without Hyperthreading support) with macOS installed on this model. If you want to let your investments on your hardware be worthy, buy i7 models (GB-BNi7QG4-950, GB-BNi7G4-950, GB-BNi7HG4-950) instead.

In this particular case, [Piker Alpha's `ssdtPRGen.sh`](https://github.com/Piker-Alpha/ssdtPRGen.sh) could be confused while generating the SSDT Power Management patch for such hardware-SMBIOS configurations: it detects a Skylake CPU from the computer running under a SMBIOS configuration which is configured with a non-Skylake Mac model, and it loses its shit. Fortunately, when I was previously using my SMBIOS configured as `iMac17,1`, I ran Piker's `ssdtPRGen.sh` and generated the Power Management SSDT for GB-BNi7QG4-950, GB-BNi7G4-950 and my GB-BNi7HG4-950. You could get this file from my repository (`/EFIVolume/EFI/Clover/ACPI/patched/SSDT.aml`), download it as a RAW file, keep this file intact, and put it into the right place of your Clover folder: `/EFI/EFI/Clover/ACPI/patched/`.

If you want to know how to use Piker Alpha's `ssdtPRGen.sh` to generate the Power Management SSDT patch, please read this article: https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.146870/

#### Clover Configurations

These are what you are going to do **from a scratch file** created by your [Clover Configurator](http://mackie100projects.altervista.org/download-clover-configurator/) (at least version 4.52 recommended):

###### 1. ACPI

* Enable / Tick / Check `FixShutdown_0004`.

* Apply those SSDT patches listed from the "1. USB Injection" section above in this article.

###### 2. BOOT

* Boot Arguments

  - `dart=0`  

  - `cpus=7` (cpus = "total amount of logical cores of your CPU" -1) # Necessary even if Skylake settings below are enabled.  

* Legacy `PBR`.

###### 3. CPU

* Skylake Settings

  - Enable / Tick / Check `HWPEnable` and `UseARTFrequency`.  
  
  - CPU Type should be set to `0x0905`.

  - HWPValue should be set to `0x00002401` (maximum performance) for i7-6820HQ installed on desktop computers.  

  - Regarding how to set HWPValue, please read: https://www.tonymacx86.com/threads/skylake-hwp-enable.214915/

###### 4. Devices

* Enable / Tick / Check `FixOwnership` under "USB" pane.

* Audio = `Detect`

###### 5. GUI (Not what you have to do, according to your necessities)

* Scan

  - Click `Custom`.
  
  - Enable / Tick / Check `Entries` and `Tool`.
  
* Hide Volume

  - If you have Windows installed (EFI boot) on the same computer, you could hide Windows volumes and boot Windows from `Microsoft EFI` entry listed in Clover.

###### 6. Kernel and Kext Patches

* Enable / Tick / Check `AppleRTC` and `KernelPm`.

* Regarding `KextsToPatch`, read "Clover KextsToPatch" above in this article.

###### 7. System Parameters

* Enable / Tick / Check `Inject System ID` and `NvidiaWeb`.

  - The `NvidiaWeb` is useless during macOS Installation but won't interfere your computer to boot into macOS Installer. It only works as long as you have the correct version of NVidia Web Driver installed.
  
  - You have your responsibility to perform system updates NOT BEFORE THAN the correct NVidia Web Driver is available for the new system update you want to install. Also, every NVidia Web Driver release matches a certain build of macOS.

###### 8. SMBIOS (Please finish this before proceeding to "Rt Variables")

1. Run `Terminal.app` and run `uuidgen` command, and you will get a new UUID generated by the command. This is your `SmUUID`. Note down this UUID for further purposes. Do not put it into the `SmUUID` (System UUID) field right now until you finish the next step, or you may have to enter it again.

2. Click on the vertical upside-down arrow button to the top-right of the "memory" pane (this button is located to the bottom-right of the big question mark icon or the big computer icon, depending on the existed SMBIOS data written in this Window), **then** choose `iMac14,2`. You will then automatically get a set of randomly-generated SMBIOS Identifiers: `Board-ID (BID)`, `Board Serial Number (MLB)`, `Serial Number (Product Serial Number, PSN)`. You could always click the `Generate New` button to regenerate a new set of SMBIOS Identifiers.

  - You SHALL BEAR IN MIND that **Each Hackintosh Shall use its Unique SMBios Identifiers and SmUUID**. Copying these parameters from a real Mac computer is stealing its owner's legal rights of using services related to Apple ID and may lead to potential problems (e.g. the real Mac which your SMBios Identifiers and SmUUID are from may be blacklisted by Apple and may not be appealable in the future; so do the Apple IDs used on computers using such SmUUID and SMBios Identifiers). Similar problems could happen to Apple IDs used on Hackintosh computers sharing a set of SMBios Identifiers and SmUUID (or any subset like MLB, BID, PSN).
  
  - Go to https://everymac.com/ultimate-mac-lookup/ and use your PSN to see whether this website could use this PSN to refer to the model you chose in Clover Configurator. **If the website returns either wrong model or informs that it failed to refer a product, please switch back to Clover Configurator window and regenerate a new set of SMBIOS Identifiers.** If the website returns the correct result, you are good to do the next step.
  
  - Go to https://checkcoverage.apple.com/ and use your PSN to "check its coverage". **If this website successfully locates an actual Apple product according to your generated PSN, please do ditch this PSN (and its bundled MLB, BID, and SmUUID if ever used together in any case) and regenerate a new set of SmBIOS Identifiers.** As long as it says "We're sorry, but this serial number is not valid. Please check your information and try again", you are good to go.
  
3. Switch back to Clover Configurator and put your SmUUID (generated by `uuidgen` in Terminal) into the `SmUUID` field.

4. Please do check whether Clover Configurator helped you fill out the `Firmware Features`, `Firmware Features Mask`, `Platform Feature`, `Bios Version`, `Bios Release Date`.

  - The correct parameters used in these entries vary, depending on the actual mac model chosen in your Clover configuration file and the nature of the latest firmware updates released by Apple to the public.
  
  - If you are migrating your previously used Clover configuration file in the same mac model (e.g. `iMac 14,2`), please update your Clover Configurator to the latest version, use it to create a new blank configuration file, choose `iMac 14,2` in its SMBIOS category, and copy the just-generated `Firmware Features`, `Firmware Features Mask`, `Platform Feature`, `Bios Version` & `Bios Release Date` data to your current Clover configuration file.

###### 9. Rt Variables (Runtime Variables)

* BooterConfig = `0x28`

* CsrActiveConfig = `0x67`

* MLB = The final Board Serial Number (MLB) value you get through the previous step "SMBIOS".

* Regarding the ROM entry, press "from SMBIOS" and then click the "Generate" button. As long as you correctly configured your SMBIOS, you will be able to generate new MAC address here.

  If you are pretty sure that the physical MAC address of your computer (both WIFI and Ethernet) are not compromised to other people and are not used on other Hackintosh computers (in their Clover configuration files), you could leave the ROM entry blank and let Clover read your physical MAC address instead.

#### EFI Drivers in Clover

For macOS 10.13, you don't need the "HFSPlus.efi" unless you have to install your system into an HFS+ volume without converting to APFS. You could always get the "apfs.efi" from the `/usr/standalone/i386/` folder from an installed macOS 10.13 system volume.

You do need OsxAptioFixDrv-64.efi (do watch the spelling of the filename with caution).

In my current Clover configuration, I have the following EFI drivers existed under `/EFI/EFI/Clover/driver64UEFI/` folder, and I forgot which of them are not necessary: `apfs.efi` `AppleImageCodec-64.efi` `AppleKeyAggregator-64.efi` 
`AppleUITheme-64.efi` `DataHubDxe-64.efi` `EmuVariableUefi-64.efi` `Fat-64.efi` `FirmwareVolume-64.efi` `FSInject-64.efi` `HFSPlus.efi` `OsxAptioFixDrv-64.efi` `OsxFatBinaryDrv-64.efi` `SMCHelper-64.efi`.

$ End of File.
