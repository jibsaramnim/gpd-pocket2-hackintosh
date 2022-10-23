# GPD Pocket 2 Hackintosh configuration 
Clover configuration and required/related Kexts to run macOS on your GPD Pocket 2. At long last, we've got it!

Special thanks go out to [Tonymacx86 user fnanao](https://www.tonymacx86.com/threads/gpd-pocket-2-battery-status.279659/), who kindly shared their EFI folder and configuration files that helped us get to working hardware acceleration, something that had stumped me personally in the ~6 or so months I off-and-on tried to make it work.
  
## Screenshot

![macOS Mojave 10.14.5 running on the GPD Pocket 2 m3-8100y](images/screenshot_m3-8100y.png?raw=true)

## Compatibility

 - **GPD Pocket 2 m3-8100Y**: The main model this configuration was developed with and for.
 - **GPD Pocket 2 m3-7y30**: [Reported as working](https://github.com/hellodeibu/gpd-pocket2-hackintosh/issues/40).
 - **GPD Pocket 2 Celeron 3965Y**: Unconfirmed. Please let us know if it works, Consider submitting a pull request with this model's specific fixes if you have them. Thank you!
 - **One Mix 3 & 2S**: Yes. [Please check out Balopez83's separate repository](https://github.com/balopez83/One-Mix-3-Hackintosh).

## Requirements

- A GPD Pocket 2
- A USB stick, drive or microSD card to install macOS on (minimum 32GB)
- A USB stick, drive or microSD card to install macOS from (if you're installing macOS, if you're cloning from an existing installation this is not required)

## Quick Start Guide:

1. Create a bootable Mojave or Catalina bootable USB using this [tutorial](https://internet-install.gitbook.io/macos-internet-install/) OR this [tutorial](https://www.olarila.com/topic/6278-new-vanilla-olarila-images/)(arguably easier) but use my CLOVER folder at the point where you should configure CLOVER bootloader
2. Boot from this usb then install Mojave/Catalina on a free partition of your drive ( please note...must be installed to usb stick, external hard drive or micro sd as macos does not detect emmc )

Download [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/), mount EFI partition of newly created drive/stick, place EFI folder from this repository in there. Open `config.plist` with Clover Configurator.

Under `SMBIOS` click `Generate New` to generate a new serial number. Copy the entire serial number and in the `Board Serial Number` paste it whilst leaving the last five characters of whatever number is already there in place, so that the board serial number ends up looking like `{serial number}{five more characters}`. Then go to `System Parameters` and click `Generate New` to create a new UUID too. While you're there, ensure `Inject Kexts` is still set to `Detect`. Save the config file, and you're set. Either use this EFI on a macOS Mojave installer USB stick or for an already installed system, both should work.

## What's included
Required Kexts to be able to run macOS on your GPD Pocket 2 are included in this repo already. You might want to check to see if there are newer versions available, or just go ahead with what's here and start this way.

- [Lilu](https://github.com/acidanthera/Lilu) v1.4.5
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen) v1.4.0
- [AppleALC](https://github.com/acidanthera/AppleALC) v1.5.0
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC) v1.1.4
  - SMCProcessor v1.1.4
  - SMCBatteryManager v1.1.4
  - SMCSuperIO v1.1.4
- [USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads) v0.7.1
- [VoodooHDA](https://sourceforge.net/projects/voodoohda/) v2.9.2
- [VoodooI2C](https://github.com/alexandred/VoodooI2C) v2.3
- [VoodooPS2Controller](https://bitbucket.org/RehabMan/os-x-voodoo-ps2-controller/downloads/) v1.8.28
- [CPUFriend](https://github.com/acidanthera/CPUFriend) v1.1.8
- [CodecCommander](https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/) v2.7.1
- [NullEthernet](https://bitbucket.org/RehabMan/os-x-null-ethernet/downloads/) v1.0.6
- [VoodooI2CGoodix](https://github.com/lazd/VoodooI2CGoodix) v0.3.1
- [itlwm](https://github.com/zxystd/itlwm) v1.0

## Post-installation tweaks
A few required or otherwise useful steps to take on a running macOS system on your GPD Pocket 2:

## Changing the display orientation
To correct the display orientation within macOS, press and hold down `cmd+opt` (`Windows+alt`) and open `System Preferences`. Keep holding down those two buttons and click on `Displays`. The screen orientation option will now appear even for the built-in display. Set it to `270 degrees` for the correct orientation. Alternatively...just download the [Display Rotation Menu](https://www.magesw.com/displayrotation/) app and set the view to portrait flipped

### Enabling HiDPI mode
I recommend running the following command post-installation to enable HiDPI mode (thanks to fnanao for this tip), as the default resolution of 1920x1200 makes everything quite small:

`sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true`

### Ensuring en0 is present
If you started by cloning an existing system drive of another machine or if you used a USB-C hub that has built-in networking for example, you might want to fix the order of network devices to ensure things like the App Store will work. Included in this repo is NullEthernet, which I included in case you're not planning to (always) have an ethernet or wifi USB device plugged in. If you don't need this because you're always planning to have such a device plugged in, you can remove `kexts/Other/NullEthernet.kext` as-well as `ACPI/patched/ssdt-rmne.aml`. The easiest way to ensure the order of network devices is optimal is to delete all of them, reboot, and have macOS automatically re-populate the list (after which you can make whatever changed you might want to make). To do this, open System Preferences and head to `Network`. Then use the minus button to remove each of the networks shown, selecting "yes" whenever macOS asks if it should re-add them should they appear later on again. Once you have done that, open Terminal and run the following command to fully delete the network configuration's config file:

`sudo rm /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`

Then, reboot your Pocket 2. Once back in macOS, re-open System Preferences, head to `Network` and you can set up VLANs or whatever else you might have to do for your network. If you want to make sure this set things up the right way, you can check the contents of the config file, searching for the interface named `en0` (which, if you kept NullEthernet, should be the interface simply called "Ethernet"). To check, you can run the following command which prints out the entire file's contents for you to read through (or `cmd+f` through, anyway ;):

`cat /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`

## Setting resolutions over 1080p
1. load clover and select "start uei shell 64"
2. enter the command "setup_var_3 0x83F 0x2" (without the quotations) hit enter then type exit
3. either delete config.plist and rename config4k.plist to config.plist OR select config4k at clover boot screen under options

### Additional drivers

[Wifi dongle driver](https://github.com/chris1111/Wireless-USB-Adapter-Clover)

[Wifi dongle driver(older adapters)](https://github.com/chris1111/Wireless-Ralink-Panel-Utility)

[Latest AutoBuild of Intel WiFi Driver & HeliPort App](https://github.com/1hbb/OpenIntelWireless-Factory/releases)

## What Works

- Full hardware acceleration
- Audio (for the time being you must choose between built-in OR external speakers...internal is fine as-is but you must delete voodoohda.kext for external sound to work) 
- Bluetooth
- Battery reading and charging recognition
- Both USB-A ports as-well as the USB-C port
- Built-in microSD card reader
- CPU Temperature and voltage/wattage reading (Confirmed with iStat Menus)
- TouchScreen ( double click doesnt work and non-functional on mojave )
- Internal Wi-Fi ( use the heliport app to join networks just like the built in wireless and use the instructions [here](https://support.apple.com/guide/mac-help/open-items-automatically-when-you-log-in-mh15189/mac) to autostart it if u would like...if u want to connect to your home internet automatically then u will need to edit the info.plist of the itlwm.kext with your ssid and password OR set your router to ssid "ssdt" with security key "zxyssdt112233")

## What's not yet working

- Sleep mode (likely related to display brightness configuration, wip)
- Microphone

## What will never* work
- eMMC built-in storage. You can only use an external USB drive/stick or microSD card to run macOS.

_* Not unless someone decides to make custom kexts for these, of course._

## Notes:
You can use the "right orientation" to have Clover boot in proper landscape orientation, but be aware that if you want to access Clover's boot options (e.g. enabling verbose mode, disabling loading of certain kexts, etc.) this will cause graphical issues to the point where you cannot actually read the names of these options. For this to work you must change the screen orientation in BIOS back to the screen's default mode. This is not an issue for normal usage, however, only when debugging for example, but I wanted to mention it here just in case.

