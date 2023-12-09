# Gigabyte Z370 AORUS Ultra Gaming - OpenCore Build

I have used the same templete as [iTechDhaval's](https://github.com/iTechDhaval/gigabyte-Z370-aorus-gaming-5-opencore) repo. 
You must generate your own SMBIOS for iMessage to work with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS).

## System Configuration
* Intel Core i7-8700 3.2GHz 6-Core Processor
* Gigabyte - Z370 AORUS Ultra Gaming (rev. 1.0)
* Corsair Vengeance LPX
* Samsung 980 SSD 1TB M.2 NVMe PCI Express 3.0
* Corsair PSU CS Series 650 W 80+ Gold CS650M Modular
* TP-LINK UB400

## What works
- [x] iMessage/Facetime
- [x] Audio
- [x] Display Port & HDMI Audio (Needs a small patch, see below)
- [x] Graphics Intel UHD Graphics 630
- [x] Ethernet
- [x] USB 2.0 & 3.0 & 3.1 & Type-C

## What does not work
* Sleep

## Gathering Files

### Required
- [LiLu](https://github.com/acidanthera/Lilu/releases)
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)
- [AppleALC](https://github.com/acidanthera/AppleALC/releases)
- [IntelMausi](https://github.com/acidanthera/IntelMausi/releases)

## Optional
- [Bluetooth](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.8)

Replace the above kexts in `/EFI/OC/Kexts` to get the latest version

## BIOS SETTINGS
* Save & Exit → Load Optimized Defaults
* BIOS → Fast Boot: Disabled
* BIOS → CSM Support: Disabled
* Peripherals → Trusted Computing → Security Device Support: Disable
* Peripherals → Network Stack Configuration → Network Stack: Disabled
* Peripherals → USB Configuration → Legacy USB Support: Auto
* Peripherals → USB Configuration → XHCI Hand-off: Enabled
* Chipset → Vt-d: Disabled
* Chipset → Internal Graphics: Enabled. (Reboot BIOS if the following options are not available)
* Chipset → DMVT Pre-Allocated: 128M
* Chipset → DMVT Total Gfx Mem: 256M
* Chipset → Wake on LAN Enable: Disabled
* Chipset → IOAPIC 24-119 Entries: Enabled
* Power → Erp : Enabled (Fix shutdown & motherboard leds not powering off)

## USB Port Mapping

If you have a different motherboard go to [USBMap](https://github.com/corpnewt/USBMap) for instructions to create the kext that works for you. Then replace it in `/EFI/OC/Kexts`.

## Unlock MSR 0xE2 register (CFG Lock)

### Checking via VerifyMsrE2

Boot OpenCore and select the VerifyMsrE2.efi entry. This should provide you with one of the following:

CFG-Lock is enabled:
```
This firmware has LOCKED MSR 0xE2 register!
```

CFG-Lock is disabled:
```
This firmware has UNLOCKED MSR 0xE2 register!
```
For the former, please continue here: Disabling CFG Lock.

For the latter, you don't need to do any CFG-Lock patches and can simply disable `Kernel -> Quirks -> AppleCpuPmCfgLock` and `Kernel -> Quirks -> AppleXcpmCfgLock`.

| Quirk | Enabled |
| :--- | :--- |
| AppleCpuPmCfgLock | No/False |
| AppleXcpmCfgLock | No/False |

### Disabling CFG Lock

So you've created the EFI folder but you can't still boot without unlocking before CFG Lock. In order to do this boot OpenCore and select modGRUBShell.efi entry.

1. `Gigabyte Z370 AORUS Ultra Gaming` board is using `8653Ah` offset to manage CFG Lock status.

2. Run the Modified GRUB Shell and write the following command:

```
setup_var 8653Ah 0x00
```

3. At this point, run either `reset` in the shell or simply reboot your machine. And with that, you should have `CFG Lock` unlocked! To verify, you can run VerifyMsrE2.efi again to verify whether the variable was set correctly then finally disable `Kernel -> Quirks -> AppleCpuPmCfgLock` and `Kernel -> Quirks -> AppleXcpmCfgLock`.

If you have a different motherboard download [UEFITool](https://github.com/LongSoft/UEFITool) and the BIOS from your motherboard manufacturer. I used version 0.28.0 because I got multiple results in newer versions. 

1. Open UEFITool
2. File → 'Open image file...'
3. Click 'Show options' and select 'All files (\*)'
4. Select the file containing the BIOS
5. File → 'Search...' select text and type 'CFG Lock'
6. It should display: 'Unicode text "CFG Lock" found in PE32 image section at offset XXXXXX'

## Troubleshooting

1. Reset BIOS Settings
2. Disable CFG Lock
3. Reboot
