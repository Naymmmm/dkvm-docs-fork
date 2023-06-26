<p align="center">
  <img src="./Assets/DarwinKVMLogo.png" width="50%" height="50%" >
</p>

<p align="center">
  <img width="650" height="200" src="./Assets/HeaderTextOnly.png">
</p>

<h1 align="center">An Advanced Template for running macOS within QEMU/KVM</h1>
<h4 align="center">Features: Clean EFI Template for maximum customizability before boot, Compatibility with RisingPrism's Single GPU Passthrough, DisplayOverrides for repairing incompatible monitors, Custom Memory Mapping, Custom USB Mapping, Fake Core Count for incompatible CPU Topology, Host CPU Overclocking, Host Network Bridge for VM visibility, AMD GPU Zero-RPM Disable and custom sPPT Fan Curve! as well as many more advanced tutorials ready to further perfect your experience!</h4>

### Requirements

* A compatible graphics card. <b>This is a must, don't bother if you're not getting GPU Accel.</b> please reference this [list](https://dortania.github.io/GPU-Buyers-Guide/) to verify.
  * There are some exceptions, if you're crazy and want to use a legacy NVIDIA GPU, please reference this [list](https://elitemacx86.com/threads/nvidia-gpu-compatibility-list-for-macos.614/) to check macOS/OCLP compatibility. If it's supported, there is a section down for Legacy NVIDIA Patching.

* A modern Linux distribution. E.g. Arch Based for the latest packages, my personally tested and working are:
  * EndeavourOS
  * ArcoLinuxB Plasma
  * Pure Arch

* A CPU with Intel VT-x or AMD SVM support is required (`grep -e vmx -e svm /proc/cpuinfo`)

* A CPU with SSE4.1 support is required for >= macOS Sierra

* A CPU with AVX2 support is required for >= macOS Ventura; but you can bypass the limitation with CryptexFixup as of now.

* Previous experience creating an EFI for your bare metal system and/or reading/understanding the [Dortania OpenCore Guide](https://dortania.github.io/OpenCore-Install-Guide/)

* Various Software/Packages, some optional, some not:
  * qemu
  * libvirtd/virtmanager
  * Python 3 installed with the tkinter package
  * dmg2img
  * qemu-img
  * [RisingPrism's Single GPU Passthrough Scripts](https://gitlab.com/risingprismtv/single-gpu-passthrough)
  * [ProperTree](https://github.com/corpnewt/ProperTree)
  * [GenSMBios](https://github.com/corpnewt/GenSMBIOS)
  * [Hackintool](https://github.com/benbaker76/Hackintool)
  * [SSDTTime](https://github.com/corpnewt/SSDTTime)

<br>

* <b>PATIENCE! This is NOT a Pre-Built EFI! You are responsible for completing it!</b>

</br>
<h1 align="center">Table of Contents</h1>

- [What is this for?](https://github.com/royalgraphx/DarwinKVM#what-is-this-for)

- [Who is this for?](https://github.com/royalgraphx/DarwinKVM#who-is-this-for)

- [Host Preparations](https://github.com/royalgraphx/DarwinKVM#host-preparations)
  - [Part 1 - BIOS Settings](https://github.com/royalgraphx/DarwinKVM#part-1-bios-settings)
  - [Part 2 - GRUB Configuration](https://github.com/royalgraphx/DarwinKVM#part-2-grub-configuration)
  - [Part 3 - Bridge Networking](https://github.com/royalgraphx/DarwinKVM#part-3-bridge-networking)
    - [A. Goal Examples](https://github.com/royalgraphx/DarwinKVM#a-goal-examples)
    - [B. Prerequisites to the script](https://github.com/royalgraphx/DarwinKVM#b-prerequisites-to-the-script) 
  - [Part 4 - Package Installation](https://github.com/royalgraphx/DarwinKVM#part-4-package-installation)
  - [Part 5 - Libvirtd Configuration](https://github.com/royalgraphx/DarwinKVM#part-5-libvirtd-configuration)
    - [A. Modifying Files](https://github.com/royalgraphx/DarwinKVM#a-modifying-files)
    - [B. Libvirt Services](https://github.com/royalgraphx/DarwinKVM#b-libvirtd-services)

- [OpenCore Configuration](https://github.com/royalgraphx/DarwinKVM#opencore-configuration)
  - [Part 0 - Image Creation](https://github.com/royalgraphx/DarwinKVM#part-0-image-creation)
  - [Part 1 - ACPI Tables](https://github.com/royalgraphx/DarwinKVM#part-1-acpi-tables)
  - [Part 2 - Drivers](https://github.com/royalgraphx/DarwinKVM#part-2-drivers)
  - [Part 3 - Kexts](https://github.com/royalgraphx/DarwinKVM#part-3-kexts)
  - [Part 4 - Tools](https://github.com/royalgraphx/DarwinKVM#part-4-tools)

- [Config.plist Configuration](https://github.com/royalgraphx/DarwinKVM#configplist-configuration)
  - [Part 0 - Required Tools / Brief Overview](https://github.com/royalgraphx/DarwinKVM#part-0-required-tools--brief-overview)
  - [Part 1 - ACPI](https://github.com/royalgraphx/DarwinKVM#part-1-acpi)
  - [Part 2 - Booter](https://github.com/royalgraphx/DarwinKVM#part-2-booter)
  - [Part 3 - Device Properties](https://github.com/royalgraphx/DarwinKVM#part-3-device-properties)
  - [Part 4 - Kernel](https://github.com/royalgraphx/DarwinKVM#part-4-kernel)
  - [Part 5 - Misc](https://github.com/royalgraphx/DarwinKVM#part-5-misc)
  - [Part 6 - NVRAM](https://github.com/royalgraphx/DarwinKVM#part-6-nvram)
  - [Part 7 - Platform Info](https://github.com/royalgraphx/DarwinKVM#part-7-platform-info)
  - [Part 8 - UEFI](https://github.com/royalgraphx/DarwinKVM#part-8-uefi)

- [Congratulations! EFI Complete](https://github.com/royalgraphx/DarwinKVM#congratulations-youve-built-your-efi-image)

- [Fetching BaseSystem.dmg](https://github.com/royalgraphx/DarwinKVM#fetching-basesystemdmg)
  - [Part 0 - Required Tools / Brief Overview](https://github.com/royalgraphx/DarwinKVM#part-0-image-creation)
  - [Part 1 - Usage](https://github.com/royalgraphx/DarwinKVM#part-1-acpi-tables)

- [Installing macOS](https://github.com/royalgraphx/DarwinKVM#installing-macos)
  - [Part 0 - Importing the XML to Virt-Manager](https://github.com/royalgraphx/DarwinKVM#part-0-importing-the-xml-to-virt-manager)
  - [Part 1 - Configure VirtIO Display](https://github.com/royalgraphx/DarwinKVM#part-1-configure-virtio-display)
  - [Part 2 - Configure OpenCore VirtIO Drive](https://github.com/royalgraphx/DarwinKVM#part-2-configure-opencore-virtio-drive)
  - [Part 3 - Configure VirtIO NIC](https://github.com/royalgraphx/DarwinKVM#part-3-configure-virtio-nic)
  - [Part 4 - Review!](https://github.com/royalgraphx/DarwinKVM#part-4-review)
  - [Part 5 - Installation](https://github.com/royalgraphx/DarwinKVM#part-5-installation)

- [Single GPU Passthrough](https://github.com/royalgraphx/DarwinKVM#single-gpu-passthrough)
  - [Part 1 - Installation](https://github.com/royalgraphx/DarwinKVM#part-1-installation)
  - [Part 2 - Hook Modification](https://github.com/royalgraphx/DarwinKVM#part-2-hook-modification)
  - [Part 3 - Virt-Manager Modifications](https://github.com/royalgraphx/DarwinKVM#part-3-virt-manager-modifications)

- [Thanks for reading!](https://github.com/royalgraphx/DarwinKVM#thanks-for-reading)

</br>
<h1 align="center">What is this for?</h1>

This repository and its contents are to be a continuation of my work on [LegacyOSXKVM](https://github.com/royalgraphx/LegacyOSXKVM). The goal of that project was to allow anyone to quickly revisit some of their favorite versions of Mac OS X as it was known to many for years with its various releases. Snow Leopard was the main focus of that project, and as such had the most effort put into it. Nowadays we need to be on modern versions of macOS to enjoy the latest and greatest offered from Apple. The focus has now shifted to providing an Up-to-Date, Out of Box (OOB), Clean Template for creating Virtual Machines of the latest versions offered by Apple. As of writing this, you can create a powerful VM of macOS Ventura, Monterey, and even Sonoma works. The guides in this repository will help you continuously work on your virtual machine to make it the perfect experience. Things will not work right away, you will slowly keep fixing them as you discover what must be fixed.

</br>
<h1 align="center">Who is this for?</h1>

This is for experienced users. You should already be familiar with 3 core concepts: [Virtualization](https://libvirt.org/)/[QEMU](https://www.qemu.org/docs/master/), [OpenCore](https://dortania.github.io/OpenCore-Install-Guide/), and [macOS](https://en.wikipedia.org/wiki/MacOS). If you feel as though you are not up to speed on any of these concepts, please take the time to first gain adequate knowledge as it will vastly improve your chances of having a working system you can daily drive. This guide is written completely from my perspective as I've learned throughout my time in the Hackintosh community. What you would typically do if you wanted to run macOS on your system you would have to use the OpenCore bootloader to provide macOS with the necessary information it needs. A Virtual Machine is no different. In theory, what we are simply doing is creating an OpenCore disk image that acts as if it were the equivalent of a USB or an EFI partition post-installation. While there exist many projects that utilize QEMU/KVM, for daily driving you must have a compatible GPU. What this means for the user of any projects that are seen as the equivalent to "Prebuilt EFI's" is that there is no learning involved. This causes the user to not understand why certain things are broken on their system and possibly may never fix those issues, potentially leaving them in the background. This guide is for those who are looking to properly create a macOS Virtual Machine from the ground up. <b>PLEASE READ CAREFULLY.</b> Try not to ask for support before rereading, many times you will misread something on accident or are simply not paying enough attention to what it's instructing you to do. 

<br>
<h1 align="center">Host Preparations</h1>

<h2 align="center"><b>Part 1:</b> BIOS Settings</h2>
<h4 align="center">Will depend on your Host Hardware.</h4>
<h4 align="center">This section has been derived from the <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/1)-Preparations">Preparations</a> section via <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/home">RisingPrism</a>.</h4>
<br>

Depending on your machine's CPU, you need to enable certain settings in your BIOS for your passthrough to succeed. Enable the settings listed in this table:

<br>

| AMD  | Intel |
| ---- | ----- |
| IOMMU | VT-d |
| NX Mode | XD (eXecute Disable) Bit |
| SVM Mode | VT-x |

<br>

If you do not have any virtualization settings, chances are they're already enabled, but double check that your BIOS is up to date, and that your CPU and motherboard support virtualization.

<br>
<br>
<br>

<h2 align="center"><b>Part 2:</b> GRUB Configuration</h2>
<h4 align="center">Enabling flags needed for Virtualization/QEMU/KVM/libvirtd</h4>
<h4 align="center">This section has been derived from the <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/2)-Editing-GRUB">Editing GRUB</a> section via <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/home">RisingPrism</a>.</h4>
<br>

<br>

Start by using your favorite terminal text editor. In this example, we'll be using nano. Note: If you're not using GRUB, follow the same steps, but refer to the Arch Wiki on how to edit the config file.

```
sudo nano /etc/default/grub
```

We'll need to check our GRUB CMD Line flags and add various ones depending on the users' hardware.

| AMD CPU  | Intel CPU | AMD GPU | Needed Regardless |
| ---- | ----- | ----- | ----- |
| amd_iommu=on | intel_iommu=on | video=efifb:off | iommu=pt | 

Example GRUB configuration for an AMD CPU + AMD GPU host:

```
amd_iommu=on iommu=pt video=efifb:off
```

When you're done make sure you use ``grub-mkconfig`` to update the GRUB Bootloader. Restart Required.

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

<br>
<br>

<h2 align="center"><b>Part 3:</b> Bridge Networking</h2>
<h4 align="center">Creation of the Bridge interface for your DKVM.</h4>
<h4 align="center">This section has been derived from the <a href="https://github.com/royalgraphx/DarwinKVM/tree/main/BridgeNetworking">Bridge Networking</a> Submodule.</h4>
<br>

<br>

<h3 align="center"><b>Overview</b></h3>

<br>

First things first, I highly recommend you take the time out of your day to not skip this process, just as you shouldn't skip over any other steps. To understand the point of a network bridge, you'll need to know that when you use QEMU or Virt-Manager to start a Virtual Machine with networking, you will typically be using the default network provided by libvirt. This creates its own DHCP server, meaning that our Machines are created with IP addresses such that, we see "192.168.68.2" or something along those lines, which does not match up with our local IPs on our broader network. The point of setting this Bridge interface up is to strip our ethernet controller of an assigned IP address and stop any other Network Managers that may be present on the users' system so that we can leverage systemd-networkd to automate the bridge creation. What it simply does, is create a new interface named "br0". It then modifies the users' current ethernet interface to "master br0", meaning that br0 is now providing the information to the users' physical ethernet interface. We then give br0 an IP address via DHCP using ipv4. When we use Virt-Manager in this configuration, we can use a bridge interface, we set it to ``br0`` and the result is our Virtual Machines now appear on the broader network as physical, real devices. Allowing SSH via and to any other devices on the LAN.

<br>

<h2 align="center"><b>A. Goal Examples</b></h2>

<br>

<h3 align="center">This is the guest macOS talking to devices on the broader network.</h3>

<p align="center">
  <img src="./Assets/BridgeNetworkingHypervisorSSH.png">
</p>

<p align="center">
  <img src="./Assets/BridgeNetworkingLocalTerminal.png">
</p>

<p align="center">
  <img src="./Assets/BridgeNetworkingRPI.png">
</p>

<p align="center">
  <img src="./Assets/BridgeNetworkingRouterDash.png">
</p>

<br>

<h2 align="center"><b>B. Prerequisites to the script</b></h2>

<br>

In my opinion, this is the number one step to set up practically right after the installation of a new host operating system. Allowing your virtual machines to be visible to the broader network may seem like something you can ignore, and while you very well can... I enjoy my Virtual Machines appearing as real, physical ethernet devices. The most popular and commonly used package for managing your network connection will most likely be "NetworkManager", and this can be checked by issuing a systemctl command to check the status.

```
sudo systemctl status NetworkManager
```

If you see that it is running, you can choose to stop it but for this guide, we'll be making use of the DKVM Bridge Networking submodule. It contains a script that will quickly go through a few checks and create a bridge interface for your use. You must have systemd in some form, install it prior or check your system for systemd-networkd with the following command.

```
sudo systemctl status systemd-networkd
```

For more information and the completion of this section, refer to [Bridge Networking](https://github.com/royalgraphx/DarwinKVM/tree/main/BridgeNetworking).


<br>
<br>

<h2 align="center"><b>Part 4:</b> Package Installation</h2>
<h4 align="center">Package requirements for base DKVM.</h4>
<h4 align="center">This section has been derived from the <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/4)-Configuration-of-libvirt">Configuration of libvirt
</a> section via <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/home">RisingPrism</a>.</h4>
<br>

Since everything in this guide is outlined for simply Arch, you may need to find the package equivalent to your system. Here is the command to install all required packages.

```
sudo pacman -S virt-manager qemu vde2 ebtables iptables-nft nftables dnsmasq bridge-utils ovmf
```

Please note: Conflicts may happen when installing these programs.
A warning like the below example may appear in your terminal:

```
iptables and iptables-nft are in conflict. Remove iptables? [y/N]
```

If you do encounter this kind of message, press y and enter to continue the installation.

<br>
<br>

<h2 align="center"><b>Part 5:</b> Libvirtd Configuration</h2>
<h4 align="center">Necessary changes to use Virt-Manager via User.</h4>
<h4 align="center">This section has been derived from the <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/4)-Configuration-of-libvirt">Configuration of libvirt
</a> section via <a href="https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/home">RisingPrism</a>.</h4>
<br>

Note; Adding yourself to the libvirt group allows for password-less root both from the host and guest. If you are uncomfortable with that and want to avoid this, consider using [Polkit](https://wiki.archlinux.org/title/Polkit) instead which will prompt you for your password.

<br>

<h2 align="center"><b>A. Modifying Files</b></h2>

<br>

There are two files we must edit. Please make the changes accordingly. Not too much to say, follow this step-by-step and you'll be fine along with getting logs printed.

<br>

Use your favorite text editor to make the following changes:
```
/etc/libvirt/libvirtd.conf
```

``Read/Write permissions and Group - Uncomment the following lines.``

```
unix_sock_group = "libvirt"
unix_sock_rw_perms = "0770"
```

``Logging - Add to the very bottom.``

```
log_filters="3:qemu 1:libvirt"
log_outputs="2:file:/var/log/libvirt/libvirtd.log"
```

<br>
<br>

Use your favorite text editor to make the following changes:
```
/etc/libvirt/qemu.conf
```

``Read/Write permissions and Group - Uncomment and Edit the following lines to your username.``

```
user = "root"
group = "root"
```

Example properly modified snippet:

```
user = "royalgraphx"
group = "royalgraphx"
```

<br>

<h2 align="center"><b>B. Libvirtd Services</b></h2>

<br>

You now need to add your user to the kvm and libvirt groups, to allow libvirt to write files properly:

```
sudo usermod -a -G kvm,libvirt $(whoami)
```

Now enable and start the libvirtd Service to fully apply changes:

```
sudo systemctl enable --start libvirtd
```

If you did not set up the bridge interface and will be using Virt-Managers default NIC, you will also set the virtual networks to auto-start on boot:

```
sudo virsh net-autostart default
```

Skip this if you did set up the br0 interface.

<br>
<h1 align="center">OpenCore Configuration</h1>
<h2 align="center">Current OpenCorePkg supported: 0.9.3</h2>
<p align="center">
  <img src="./Assets/OpenCorePkgBase.png">
</p>

<h2 align="center"><b><span style="color:red">Part 0:</span></b> Image Creation</h2>
<h4 align="center">Creation of the OpenCore .img for your DKVM.</h4>
<h4 align="center">This section has been derived from the <a href="https://github.com/royalgraphx/DarwinKVM/tree/main/DiskProvision">DiskProvision</a> Submodule.</h4>
<br>

```
This tool automates the process of creating and setting up an OpenCore.img disk image for use with QEMU. It also comes with mount.sh and unmount.sh to easily modify the contents.
```

Don't skip this section. To continue in this guide you will need an image file that will act as our OpenCore USB, holding all its contents. The fastest way to do this is by changing the directory into the OpenCore folder in this repository. You can quickly generate and mount a 1GB ``.img`` file to add to your Virtual Machine later. If you need any help understanding this section, please refer to the [README.md](https://github.com/royalgraphx/DarwinKVM/tree/main/DiskProvision) for better context. You can now go ahead and transfer the EFI folder from [DarwinOCPkg](https://github.com/royalgraphx/DarwinOCPkg) to the root of the image. The rest of the steps will outline adding the necessary files to build your EFI for your Virtual Machine.

<br>
<h2 align="center"><b>Part 1:</b> ACPI Tables</h2>
<h4 align="center">This section has been derived from the <a href="https://dortania.github.io/Getting-Started-With-ACPI/">Getting Started with ACPI</a> guide. It may be out of date.</h4>
<br>

``So what are DSDTs and SSDTs? Well, these are tables present in your firmware that outline hardware devices like USB controllers, CPU threads, embedded controllers, system clocks and such. A DSDT(Differentiated System Description Table) can be seen as the body holding most of the info with smaller bits of info being passed by the SSDT(Secondary System Description Table). You can think of the DSDT as the building blueprints with SSDTs being sticky notes outlining extra details to the project.``

You can read more about ACPI and it's specs [here](https://uefi.org/sites/default/files/resources/ACPI_Spec_6_4_Jan22.pdf).

``macOS can be very picky about the devices present in the DSDT and so our job is to correct it. The main devices that need to be corrected for macOS to work properly:``

 - Embedded controller (EC)
   - macOS Catalina+ requires a device named EC to be present, so we create a dummy EC. The USBX devices (See next) also requires an EC device to work.
 - USBX
   - This sets the correct USB power properties for charging devices like phones.
 - Plugin type
   - This generally allows the use of XCPM providing native CPU power management on Intel CPUs. Our version will enable VMPlatformPlugin XCPM, exactly like a Parallels VM.


For our Virtual Machine use case, we will be emulating an Intel Cascade Lake CPU so regardless of the host architecture, the only ACPI's we require to boot macOS will be SSDT-EC-USBX and SSDT-PLUG.

You can view the CPU ACPI requirements by generation [here](https://dortania.github.io/Getting-Started-With-ACPI/ssdt-platform.html#desktop). 

``Note: Cascade Lake supersedes Skylake although not shown on the chart.``

<br>
<h4><b>The required files can be found in the DarwinOCPkg/X64/EFI/OC/ACPI folder.</b></h4>

Thanks to [ExtremeXT](https://github.com/ExtremeXT) for allowing me to include his manually created SSDT-EC-USBX which combines them into a single file, as well as the included SSDT-PLUG file. We've both tested it and it works as expected, and I use it for my daily machine so I'm confident including it, feel free to manually make your own or possibly try the ones from Acidanthera! As long as you complete this ACPI section, you can go ahead to the next step.

<br>
<h2 align="center"><b>Part 2:</b> Drivers</h2>
<h4 align="center">This section has been derived from the <a href="https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html">Adding The Base OpenCore Files</a> guide. It may be out of date.</h4>
<br>

``Now something you'll notice is that it comes with a bunch of files in Drivers and Tools folder, we don't want most of these:``
 - Keep the following from Drivers (if applicable):

| Driver  | Status | Description | 
| ----- | ----- | ----- |
| OpenRuntime.efi | Required | Required for fixing NVRAM, power management, RTC, memory mapping etc |
| ResetNvramEntry.efi | Required | Required to reset the system's NVRAM |
| OpenHfsPlus.efi | Optional | Open sourced HFS+ driver, but slower than Apple's proprietary driver |

There are already base files included in the repository. You'll have to check with your hardware to see if you need anything additional. As outlined in [Gathering files -> Firmware Drivers](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#firmware-drivers) you will see a table that states [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi) is a required Driver. Personally from my experience, I've been fine using OpenHfsPlus.efi but you should first try with [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi), please download and add that to your OpenCore EFI.

<br>
<h2 align="center"><b>Part 3:</b> Kexts</h2>
<h4 align="center">This section has been derived from the <a href="https://dortania.github.io/OpenCore-Install-Guide/ktext.html#kexts">Kexts</a> section via <a href="https://dortania.github.io/OpenCore-Install-Guide/ktext.html">Gathering files</a>. It may be out of date.</h4>
<br>

Here is a basic chart of a Kext, its use, and the status of the requirement. Check with the hardware you'll be passing through if you need any Kexts. For example, Samsung NVMe should be using NVMeFix.kext for better voltage and temperature management by macOS.

| Kext  | Status | Description | 
| ----- | ----- | ----- |
| [Lilu](https://github.com/acidanthera/Lilu/releases) | Required | A kext to patch many processes, required for AppleALC, WhateverGreen, VirtualSMC and many other kexts. Without Lilu, they will not work. |
| [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases) | Required | Used for graphics patching, DRM fixes, board ID checks, framebuffer fixes, etc; all GPUs benefit from this kext. |
| [AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip) | Required | Required on macOS 12.3 and later on AMD systems and dual-socket Intel systems, and on KVM. |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) | Optional | Emulates the SMC chip found on real macs, but we are already emulating it from the XML file, this might be useful for kexts that send temperature info to the SMC, like SMCRadeonGPU. |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases) | Optional | NVMeFix is a set of patches for the Apple NVMe storage driver, IONVMeFamily. Its goal is to improve compatibility with non-Apple SSDs. It may be used both on Apple and non-Apple computers. Note: Does not work on Sonoma as of now. |
| [RestrictEvents](https://github.com/acidanthera/RestrictEvents/releases) | Optional | Lilu plugin for blocking unwanted processes causing compatibility issues on different hardware and unlocking the support for certain features restricted to other hardware. We will mainly use it for changing the name of the CPU cosmetically. |
| [RadeonSensor](https://github.com/NootInc/RadeonSensor/releases) | Optional | Kext and Gadget to show AMD GPU temperature on macOS. |
| [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector/releases) | Optional | Injects AGPM (Apple Graphics Power Management) to our non-Apple GPUs. |

<br>
<h2 align="center"><b>Part 4:</b> Tools</h2>
<h4 align="center">This section has been derived from the <a href="https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html">Adding The Base OpenCore Files</a>. It may be out of date.</h4>
<br>

As far as I'm concerned, you only need OpenShell.efi and even then, that's only for debugging. It's already included within [DarwinOCPkg](https://github.com/royalgraphx/DarwinOCPkg).

| Tool  | Status | Description | 
| ----- | ----- | ----- |
| OpenShell.efi | Optional | Recommended for easier debugging. |

<br>
<h2 align="center">You should now be able to continue to the continue to the config.</h2>


<br>
<br>
<h1 align="center">Config.plist Configuration</h1>
<h2 align="center">Virtual Machine Cascade Lake</h2>
<p align="center">
  <img src="./Assets/OpenCoreProperTree.png">
</p>

<h2 align="center"><b><span style="color:red">Part 0:</span></b> Required Tools / Brief Overview</h2>
<h4 align="center">Download the following tools needed for modifications.</h4>
<br>

| Tool  | Status | Description | 
| ----- | ----- | ----- |
| [Python](https://www.python.org/downloads/) | Required | Needed as a dependency. |
| [ProperTree](https://github.com/corpnewt/ProperTree) | Required | Software that required Python, provides GUI and Tools for config.plist |
| [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) | Required | Must generate clean SMBIOS information for iServices |
| [DarwinOCPkg](https://github.com/royalgraphx/DarwinOCPkg/blob/main/Docs/Sample.plist) | Required | Need Docs/Sample.plist renamed to config.plist in OC folder. |

We'll first need a valid Python installation on our host. Don't forget to add Python to PATH. If for whatever reason you missed the option, restart the installer and select "Add to PATH". You'll then need to get your hands on ProperTree. 

This will allow you to use tools like "OC Clean Snapshot" which will scan your OC folder and add the various files to your config.plist automatically and in the correct order. This is especially true when dealing with kext loading order. Essentially, you cannot load a dependency before the main Kext that needs it, is loaded... so to make sure we don't run into any issues we use the officially recommended method of OC Clean Snapshot and regular OC Snapshot when updating things in our folders. 

GenSMBIOS will be very important because the Sample.plist does not contain any serial number. Meaning you cannot log into iServices until you properly generate MacPro7,1 SMBIOS information and use that on your config.plist

Of course, the repository includes a custom Sample.plist that has been cleaned up and peer-reviewed for the proper configuration needed for QEMU/KVM. There is still a Config guide you must follow for the Virtual Machine platform, but it will do the same thing as the main Dortania guide Configs: teach you what makes your hack work.

<br>
<h2 align="center"><b>Part 1:</b> ACPI</h2>
<p align="center">
  <img src="./Assets/OpenCoreACPIAdd.png">
</p>

<h2 align="center"><b>Add</b></h2>

This section of the config is meant to expose the various ACPI in your OC folder. This along with many of the other sections will be auto-filled by simply going to ``File -> OC Clean Snapshot`` and going to the OC folder in your OpenCore.img mount point.

<h2 align="center"><b>Delete</b></h2>

This blocks certain ACPI tables from loading, for us, we can ignore this.

<h2 align="center"><b>Patch</b></h2>

This section allows us to dynamically modify parts of the ACPI (DSDT, SSDT, etc.) via OpenCore. For us, our patches are handled by our SSDTs. This is a much cleaner solution as this will allow us to boot Windows and other OSes with OpenCore for dual or multi-boot configurations.

<h2 align="center"><b>Quirks</b></h2>

For settings relating to ACPI, leave everything here as default as we have no use for these quirks.

<br>
<br>
<h2 align="center"><b>Part 2:</b> Booter</h2>
<p align="center">
  <img src="./Assets/OpenCoreBooterQuirks.png">
</p>

<h2 align="center"><b>MmioWhitelist</b></h2>

This section is allowing spaces to be passthrough to macOS that are generally ignored, useful when paired with DevirtualiseMmio. We can ignore this for our Virtual Machine use cases generally.

<h2 align="center"><b>Patch</b></h2>

This contains general patches, for us, we can ignore this.

<h2 align="center"><b><span style="color:gold">Quirks</span></b></h2>

Don't skip over this section, we'll be changing the following:

<br>

| Quirk  | Value | Description | 
| ----- | ----- | ----- |
| EnableWriteUnprotector | False | This quirk and RebuildAppleMemoryMap can commonly conflict, recommended to enable the latter on newer platforms and disable this entry. |
| RebuildAppleMemoryMap | True | Rebuilds the memory map to a macOS-compatible one. |
| SetupVirtualMap | False | Fixes SetVirtualAddresses calls to virtual addresses, VMs don't require it. |
| SyncRuntimePermissions | True | Fixes MAT table alignment and required to boot Windows and Linux with MAT tables, also recommended for macOS. Mainly relevant for RebuildAppleMemoryMap users. |

<br>
<br>
<h2 align="center"><b>Part 3:</b> Device Properties</h2>
<p align="center">
  <img src="./Assets/OpenCoreDeviceProperties.png">
</p>

<h2 align="center"><b>Add</b></h2>

This allows you to add properties to various devices using its PciRoot address. For now, and in most cases we can ignore this. An example would be: overriding an ethernet controller to appear as built-in so that macOS allows iServices to work. On Virtual Machines, we rarely have to override.

<h2 align="center"><b>Delete</b></h2>

This allows you to delete properties of various devices using its PciRoot address. For now, and in most cases we can ignore this.

<br>
<br>
<h2 align="center"><b>Part 4:</b> Kernel</h2>
<p align="center">
  <img src="./Assets/OpenCoreKernel.png">
</p>

<h2 align="center"><b>Add</b></h2>

This section of the config is meant to expose the various Kexts in your OC folder. This along with many of the other sections will be auto-filled by simply going to ``File -> OC Clean Snapshot`` and going to the OC folder in your OpenCore.img mount point.

<h2 align="center"><b>Block</b></h2>

Blocks certain kexts from loading. Not relevant for us.

<h2 align="center"><b>Emulate</b></h2>

Needed for spoofing unsupported CPUs like Pentiums and Celerons. We are already spoofing the CPU to CascadeLake in the XML, so we won't need this.

- Cpuid1Mask: Leave this blank
- Cpuid1Data: Leave this blank

<h2 align="center"><b>Force</b></h2>

Used for loading kexts off system volume, only relevant for older operating systems where certain kexts are not present in the cache, i.e IONetworkingFamily in 10.6.

For us, we can ignore.

<h2 align="center"><b>Patch</b></h2>

Patches both the kernel and Kexts. I've gone ahead and incorporated CaseySJ's PCI Bus Enumeration fix on KVM. For us, we can ignore this section.

<h2 align="center"><b><span style="color:gold">Quirks</span></b></h2>

<br>
Don't skip over this section, we'll be changing the following:
<br>
<br>

| Quirk  | Value | Description | 
| ----- | ----- | ----- |
| ForceSecureBootScheme | True | Forces the x86 scheme for IMG4 verification in Apple Secure Boot initialization. |
| PanicNoKextDump | True | Allows for reading kernel panics logs when kernel panics occur. |
| PowerTimeoutKernelPanic | True | Helps fix kernel panics relating to power changes with Apple drivers in macOS Catalina, most notably with digital audio. |
| ProvideCurrentCpuInfo | True | Provides current CPU info to the kernel. This quirk works differently depending on the CPU: For KVM and other hypervisors it provides precomputed MSR 35h values solving kernel panic with ``-cpu host``. |

<h2 align="center"><b>Scheme</b></h2>

Settings related to legacy booting, we can change the following.

| Key  | Type | Value | 
| ----- | ----- | ----- |
| CustomKernel | Boolean | False |
| FuzzyMatch | Boolean | False |
| KernelArch | String | x86_64 |
| KernelCache | String | Prelinked |

<br>
<br>
<h2 align="center"><b>Part 5:</b> Misc</h2>
<p align="center">
  <img src="./Assets/OpenCoreMisc.png">
</p>

<h2 align="center"><b>BlessOverride</b></h2>

To be filled with plist string entries containing absolute UEFI paths to customised bootloaders such as \EFI\debian\grubx64.efi for the Debian bootloader. We do nothing here, nor will we ever.

<h2 align="center"><b>Boot</b></h2>

<br>
Don't skip over this section, we'll be changing the following:
<br>
<br>

| Key  | Type | Value | 
| ----- | ----- | ----- |
| HideAuxiliary | Boolean | True |
| PollAppleHotKeys | Boolean | True |

<h2 align="center"><b>Debug</b></h2>

Helpful for debugging OpenCore boot issues.
Don't skip over this section, we'll be changing the following:
<br>
<br>

| Key  | Type | Value | 
| ----- | ----- | ----- |
| Target | Number | 67 |

<h2 align="center"><b>Entries</b></h2>

Used for specifying irregular boot paths that can't be found naturally with OpenCore. We do nothing here, nor will we ever.

<h2 align="center"><b><span style="color:gold">Security</span></b></h2>

Security is pretty self-explanatory, <b>do not skip</b>. Optional is a word, you must type it out. It IS case-sensitive. We'll be changing the following:

| Key  | Type | Value | 
| ----- | ----- | ----- |
| AllowSetDefault | Boolean | True |
| ExposeSensitiveData | Number | 15 |
| ScanPolicy | Number | 0 |
| Vault | String | <span style="color:red">Optional</span> |


<h2 align="center"><b>Serial</b></h2>

Used for serial debugging (Leave everything as default).

<h2 align="center"><b>Tools</b></h2>

This section of the config is meant to expose the various Tools in your OC folder. This along with many of the other sections will be auto-filled by simply going to ``File -> OC Clean Snapshot`` and going to the OC folder in your OpenCore.img mount point.


<br>
<h2 align="center"><b>Part 6:</b> NVRAM</h2>
<p align="center">
  <img src="./Assets/OpenCoreNVRAM.png">
</p>


<h2 align="center"><b>Add</b></h2>

```
7C436110-AB2A-4BBB-A880-FE41995C9F82
```

<br>

We can use this dictionary to modify boot-args. Use the chart below for various arguments that possibly be useful later in the future. For the Recovery and Installation, before the GPU passthrough, you don't need to modify this section.

<br>

| boot-arg | Description | 
| ----- | ----- |
| -v | This enables verbose mode, which shows all the behind-the-scenes text that scrolls by as you're booting instead of the Apple logo and progress bar. It's invaluable to any Hackintosher, as it gives you an inside look at the boot process, and can help you identify issues, problem kexts, etc. |
| debug=0x100	 | This disables macOS's watchdog which helps prevents a reboot on a kernel panic. That way you can hopefully glean some useful info and follow the breadcrumbs to get past the issues. |
| keepsyms=1 | This is a companion setting to debug=0x100 that tells the OS to also print the symbols on a kernel panic. That can give some more helpful insight as to what's causing the panic itself. |

<br>

GPU Related boot-args

<br>

| boot-arg | Description | 
| ----- | ----- |
| agdpmod=pikera | Used for disabling board ID checks on some Navi GPUs (RX 5000 & 6000 series), without this you'll get a black screen. Don't use if you don't have Navi (ie. Polaris and Vega cards shouldn't use this) |
| -radcodec	| Used for allowing officially unsupported AMD GPUs (spoofed) to use the Hardware Video Encoder |
| radpg=15 | Used for disabling some power-gating modes, helpful for properly initializing AMD Cape Verde based GPUs |
| unfairgva=1 | Used for fixing hardware DRM support on supported AMD GPUs |

<h2 align="center"><b>Delete</b></h2>

Forcibly rewrites NVRAM variables, do note that Add will not overwrite values already present in NVRAM so values like boot-args should be left alone.

<h2 align="center"><b>LegacyOverwrite</b></h2>

For us, we can leave it to the default value of ``False``.

<h2 align="center"><b>LegacySchema</b></h2>

Used for assigning NVRAM variables, used with OpenVariableRuntimeDxe.efi. Only needed for systems without native NVRAM. The values under this can be deleted safely.

<h2 align="center"><b>WriteFlash</b></h2>

Enables writing to flash memory for all added variables.

| Key  | Type | Value | 
| ----- | ----- | ----- |
| WriteFlash | Boolean | True |

<br>
<br>
<h2 align="center"><b>Part 7:</b> Platform Info</h2>
<p align="center">
  <img src="./Assets/OpenCorePlatformInfo.png">
</p>


<h2 align="center"><b>Automatic</b></h2>

Leave as default.

<h2 align="center"><b>CustomMemory</b></h2>

Can be used later.

<h2 align="center"><b><span style="color:gold">Generic</span></b></h2>

At this point in the guide, you'll need to open a terminal and an instance of GenSMBIOS. Select option 1 for downloading MacSerial and Option 3 for selecting SMBIOS. For this Cascade Lake example, we'll choose the MacPro7,1 SMBIOS.

This will give us output similar to the following:

```
  #######################################################
 #               MacPro7,1 SMBIOS Info                 #
#######################################################

Type:         MacPro7,1
Serial:       F0000000000M
Board Serial: F0000000000000000F
SmUUID:       90000006-3009-4004-B001-800000000008
Apple ROM:    000000000005

Press [enter] to return...
```

To fill out the information on your config.plist, refer to the following chart to convert across.

| GenSMBIOS | config.plist | 
| ----- | ----- |
| Board Serial | MLB |
| Apple ROM | ROM |
| Type | SystemProductName |
| Serial | SystemSerialNumber |
| SmUUID | SystemUUID |

<h2 align="center"><b>UpdateDataHub</b></h2>

Update Data Hub fields. We can leave this default.

<h2 align="center"><b>UpdateSMBIOS</b></h2>

Updates SMBIOS fields. We can leave this default.

<h2 align="center"><b>UpdateSMBIOSMode</b></h2>

Replace the tables with newly allocated EfiReservedMemoryType. We can leave this default.

<h2 align="center"><b>UseRawUuidEncoding</b></h2>

Use raw encoding for SMBIOS UUIDs. We can leave this default.

<br>
<br>
<h2 align="center"><b>Part 8:</b> UEFI</h2>
<p align="center">
  <img src="./Assets/OpenCoreUEFI.png">
</p>


<h2 align="center"><b>APFS</b></h2>

By default, OpenCore only loads APFS drivers from macOS Big Sur and newer. If you are booting macOS Catalina or earlier, you may need to set a new minimum version/date. Not setting this can result in OpenCore not finding your macOS partition! For us, running Monterey, Ventura, or even Sonoma, we can skip this section.

<h2 align="center"><b>Audio</b></h2>

Related to AudioDxe settings, for us we'll be ignoring (leave as default). This is unrelated to audio support in macOS. This is mainly for adding back the Chime sound when macOS starts on bare metal situations.

<h2 align="center"><b>ConnectDrivers</b></h2>

Forces .efi drivers, change to NO will automatically connect added UEFI drivers. This can make booting slightly faster, but not all drivers connect themselves. E.g. certain file system drivers may not load. Leave it as default for our use case.

<h2 align="center"><b>Drivers</b></h2>

This section of the config is meant to expose the various Drivers in your OC folder. This along with many of the other sections will be auto-filled by simply going to ``File -> OC Clean Snapshot`` and going to the OC folder in your OpenCore.img mount point.

<h2 align="center"><b>Input</b></h2>

Related to boot.efi keyboard passthrough used for FileVault and Hotkey support, leave everything here as default as we have no use for these quirks.

<h2 align="center"><b>Output</b></h2>

Relating to OpenCore's visual output, leave everything here as default as we have no use for these quirks.

<h2 align="center"><b>ProtocolOverrides</b></h2>

Mainly relevant for Virtual Machines, legacy Macs and FileVault users. leave everything here as default as we have no use for these quirks.

<h2 align="center"><b><span style="color:gold">Quirks</span></b></h2>

Relating to quirks with the UEFI environment, leave everything here as default as we have no use for these quirks:

<h2 align="center"><b>ReservedMemory</b></h2>

Used for exempting certain memory regions from OSes to use, mainly relevant for Sandy Bridge iGPUs or systems with faulty memory. Use of this quirk is not covered in this guide. We also won't be needing it anyways, safely ignore.

<br>
<h2 align="center">Congratulations! You've built your EFI image!</h2>
<p align="center">
  <img src="./Assets/OpenCoreEFIComplete.png">
</p>

<br>
<br>
<h1 align="center">Fetching BaseSystem.dmg</h1>
<h2 align="center">Getting macOS images using macrecovery Submodule.</h2>

<h2 align="center"><b><span style="color:red">Part 0:</span></b> Required Tools / Brief Overview</h2>
<h4 align="center">Download the following tools needed for modifications.</h4>
<br>

| Tool  | Status | Description | 
| ----- | ----- | ----- |
| [Python](https://www.python.org/downloads/) | Required | Needed as a dependency. |
| [macrecovery]() | Required | Allows you to fetch RecoveryOS images. |

<br>
<br>
<h2 align="center"><b>Part 1:</b> Usage</h2>

Open a terminal and navigate to the directory containing the script files.

For this example, we'll be getting the latest macOS Ventura:

``python3 macrecovery.py -b Mac-4B682C642B45593E -m 00000000000000000 download``

<p align="center">
  <img src="./Assets/macrecovery.png">
</p>

Notice that it will then create a folder ``com.apple.recovery.boot``, which you will need to copy over to the root of the OpenCore .img mount point. Refer to the image above for an example. It does load slower this way, but will persist so you will always have it around, you can delete it after if you'd like. You can also use dmg2img to convert the BaseSystem.dmg to a BaseSystem.img you can then mount via Virt-Manager.

``dmg2img -i BaseSystem.dmg BaseSystem.img``

Make sure to unmount your img when done modifying!

<p align="center">
  <img src="./Assets/OpenCoreUnmount.png">
</p>

<br>
<br>
<h1 align="center">Installing macOS</h1>
<h2 align="center">This example will show the installation of macOS Sonoma.</h2>
<h3 align="center">This applies for Monterey and Ventura as well.</h3>


<br>
<br>
<h2 align="center"><b><span style="color:red">Part 0:</span></b> Importing the XML to Virt-Manager</h2>
<h4 align="center">This imports a blank Virtual Machine titled "DarwinKVM" ready to be used for macOS Guests.</h4>
<br>

Run the following command in the root directory of DarwinKVM:

``virsh --connect qemu:///system define DarwinKVM.xml``

Which will now allow you to view it in Virt-Manager.

<p align="center">
  <img src="./Assets/VManTemplateImport.png">
</p>

Notice this template is missing a few things you must set up.

- Has no default Display
- Has no drives configured
- Has no NIC configured

Let's go through these steps quickly.

<br>
<br>
<h2 align="center"><b>Part 1:</b> Configure VirtIO Display</h2>
<h4 align="center">This is required to have a display.</h4>

Select the "Add Hardware" button and navigate to Graphics. Select the ``Listen Type:`` to None. then select the "Finish" button.

<p align="center">
  <img src="./Assets/VManAddRecoveryDisplay1.png">
</p>

Select the Video tab on the left-hand side, and choose the ``Virtio`` model.

<p align="center">
  <img src="./Assets/VManAddRecoveryDisplay2.png">
</p>


<br>
<br>
<h2 align="center"><b>Part 2:</b> Configure OpenCore VirtIO Drive</h2>
<h4 align="center">This is required to boot OpenCore.</h4>

Select the "Add Hardware" button to bring up the Storage prompt. Select the OpenCore image via the "Manage..." button. The ``Bus type:`` should be set to VirtIO. Cache mode set to none, and Discard mode is set to unmap. If you're not going to passthrough an NVME drive to install macOS on, then this is the step to make a disk image.


<p align="center">
  <img src="./Assets/VManAddVirtIOInstallation2.png">
</p>


<p align="center">
  <img src="./Assets/VManAddOpenCore1.png">
</p>

Don't forget to set it as your boot drive.

<p align="center">
  <img src="./Assets/VManAddOpenCore2.png">
</p>

Here you can see me creating the disk image I'll be installing macOS on.

<p align="center">
  <img src="./Assets/VManAddVirtIOInstallation.png">
</p>

<br>
<br>
<h2 align="center"><b>Part 3:</b> Configure VirtIO NIC</h2>
<h4 align="center">This is required to download macOS via RecoveryOS.</h4>

Select the "Add Hardware" button and choose the Network category on the left-hand side. You can now see your network source can be set to Bridge device. The device name given by the script is ``br0``. Of course don't forget to set the Device model as ``VirtIO``.

<p align="center">
  <img src="./Assets/VManAddBridgeNIC.png">
</p>

<br>
<br>
<h2 align="center"><b>Part 4:</b> Review!</h2>
<h4 align="center">Notice the display, drives, and NIC added.</h4>

<p align="center">
  <img src="./Assets/VManExampleReadyToInstall.png">
</p>

<br>
<br>
<h2 align="center"><b>Part 5:</b> Installation</h2>
<h4 align="center">You should now be ready to install macOS!</h4>
<br>

OpenCore Menu, shows RecoveryOS detected.

<p align="center">
  <img src="./Assets/OpenCoreVMBootRecovery.png">
</p>

macOS Ventura RecoveryOS booting

<p align="center">
  <img src="./Assets/BootingRecovery.png">
</p>

macOS Ventura Disk Utility showing OpenCore drive.

<p align="center">
  <img src="./Assets/macOSRecoveryDiskUtility.png">
</p>

From here on out, the screenshots will show macOS Sonoma because I had the USB prepared nearby and wanted to test everything working still even in Sonoma. This won't make a difference for you, it's the same for macOS Ventura.

<p align="center">
  <img src="./Assets/OpenCoreSonomaRecoveryBoot.png">
</p>

Open Disk Utility, and format the target drive to APFS.

<p align="center">
  <img src="./Assets/macOSRecoveryFormatInstallTarget.png">
</p>

<p align="center">
  <img src="./Assets/macOSRecoveryFormatInstallTarget2.png">
</p>

<p align="center">
  <img src="./Assets/macOSRecoveryFormatInstallTarget3.png">
</p>

You are now ready to proceed to the installation!

<p align="center">
  <img src="./Assets/OpenCoreSonomaRecoveryInstallation1.png">
</p>

<p align="center">
  <img src="./Assets/OpenCoreSonomaRecoveryInstallation2.png">
</p>

Second Boot Phase

<p align="center">
  <img src="./Assets/OpenCoreSonomaSecondBootPhase.png">
</p>

Third Boot Phase, further unpacking.

<p align="center">
  <img src="./Assets/OpenCoreSonomaThirdBootPhase.png">
</p>

You may get a fourth reboot, if not, eventually, you will the proper name of your drive:

<p align="center">
  <img src="./Assets/OpenCoreSonomaInstallationComplete.png">
</p>

Here we are at the desktop of our Virtual Machine.

<p align="center">
  <img src="./Assets/macOSSonomaDesktop.png">
</p>

Our OpenCore image is mounted easily and recognized by macOS. Allowing easy modification within and out of the Virtual Machine. Any changes made here, can easily be viewed by mounting on the host. This can be useful for moving small files.

<p align="center">
  <img src="./Assets/macOSSonomaNoMountEFIneeded.png">
</p>

<br>
<br>
<h1 align="center">Single GPU Passthrough</h1>
<h2 align="center">Script Installation thanks to RisingPrism</h2>
<h4 align="center">If the <a href="">RisingPrism</a> scripts do not work, use the <a href="">akshaycodes</a> scripts instead.</h4>

<br>
<br>
<h2 align="center"><b>Part 1:</b> Installation</h2>
<h4 align="center">View the Gitlab from above.</h4>
<br>

We'll start by doing a ``git clone`` command to fetch the scripts.

``git clone https://gitlab.com/risingprismtv/single-gpu-passthrough.git``

In the newly created single-gpu-passthrough folder, run the installation as root:

``sudo chmod +x install_hooks.sh``

``sudo ./install_hooks.sh``

Verify that the files are installed correctly. You should have files in the following location:

```
/etc/systemd/system/libvirt-nosleep@.service
/bin/vfio-startup.sh
/bin/vfio-teardown.sh
/etc/libvirt/hooks/qemu
```

Because we previously set logs to output, you can find them here:

```
DarwinKVM.log    => /var/log/libvirt/qemu
custom_hooks.log => /var/log/libvirt/
libvirtd.log     => /var/log/libvirt/
```

But if the RisingPrism scripts aren't working well for you and you end up using akshaycodes, you can find the logs at:

```
custom_hooks.log => /var/tmp/vfio-script.log
```

<br>
<br>
<h2 align="center"><b>Part 2:</b> Hook Modification</h2>
<h4 align="center">Use your favorite terminal text editor!</h4>
<br>

Modify the following file to add `` $OBJECT == "DarwinKVM" `` to the if statement. This will allow the hook to now pay attention to the DarwinKVM Virtual Machine. If for any reason you need to use the Virtual Machine without GPU passthrough, you should remove this modification to allow proper usage of the VirtIO Display when not doing passthrough willingly or for debugging reasons.

```
sudo nano /etc/libvirt/hooks/qemu
```

Example modified file:

<p align="center">
  <img src="./Assets/QEMUHookModification.png">
</p>

<br>
<br>
<h2 align="center"><b>Part 3:</b> Virt-Manager Modifications</h2>
<h4 align="center">How to properly use GPU passthrough.</h4>
<br>

First things first, we must remove the ``Spice Display`` and ``Video Virtio`` from the left hand side in our Virt-Manager window for DarwinKVM. As you can see here, they are now removed.

<p align="center">
  <img src="./Assets/VManGPUPassthroughRemoveVirtIODisplay.png">
</p>

Now let's go ahead and remove the recoveryOS Keyboard and Mouse as we'll now be passing through our actual USB controllers later. You'll have to select the ``Overview`` tab on your left-hand side, and swap to the XML tab at the top. Scroll to the very bottom and delete and save this change.

<p align="center">
  <img src="./Assets/VManGPUPassthroughRemoveRecoveryKBM.png">
</p>

Depending on your CPU, you should enable either Topoext or SVM for your host OS. Here it is on an AMD host.

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddMultithreading.png">
</p>

Now here's the tricky bit. You'll need to check your IOMMU groups. This can be done within the DarwinKVM repository by simply issuing the command ``./iommu-check.sh``

Sample Output, this is a host using [Zen Kernel](https://github.com/zen-kernel/zen-kernel) and [ACS Patches](https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF#Bypassing_the_IOMMU_groups_(ACS_override_patch)):

Note that you will not get this output, you will see multiple devices within groups, <b>you can only pass through devices in a group, if you can pass the entire group</b>. This means that if your GPU and Ethernet are in the same group, you must pass through the entire group. This is where many people will run into issues. I do not want to go into great detail about it because I feel like it's common sense when reading the documentation I've linked above. If your IOMMU groups are not great, then consider the Zen Kernel, and ACS Patch.

<b>And remember for later, DO NOT ADD BRIDGES to your VM, that means devices like PCI bridge [0604] in your groups. </b>

```
IOMMU Group 24:
	08:00.0 PCI bridge [0604]: Advanced Micro Devices, Inc. [AMD/ATI] Navi 10 XL Upstream Port of PCI Express Switch [1002:1478] (rev c7)
IOMMU Group 25:
	09:00.0 PCI bridge [0604]: Advanced Micro Devices, Inc. [AMD/ATI] Navi 10 XL Downstream Port of PCI Express Switch [1002:1479]
IOMMU Group 26:
	0a:00.0 VGA compatible controller [0300]: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23 [Radeon RX 6600/6600 XT/6600M] [1002:73ff] (rev c7)
IOMMU Group 27:
	0a:00.1 Audio device [0403]: Advanced Micro Devices, Inc. [AMD/ATI] Navi 21/23 HDMI/DP Audio Controller [1002:ab28]
IOMMU Group 28:
	0b:00.0 Non-Essential Instrumentation [1300]: Advanced Micro Devices, Inc. [AMD] Starship/Matisse PCIe Dummy Function [1022:148a]
IOMMU Group 29:
	0c:00.0 Non-Essential Instrumentation [1300]: Advanced Micro Devices, Inc. [AMD] Starship/Matisse Reserved SPP [1022:1485]
```

If everything seems fine, if you think that you can pass your USB controllers, and Graphics card + it's audio without issues, you can move on.

<br>

Let's go ahead and add the GPU and its Audio to our Virtual Machine.

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddGPU.png">
</p>

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddGPUAudio.png">
</p>

Here's what a lot of people don't check. This will be required here as on macOS if you want your HDMI/DP Audio to work, this must be configured correctly. Windows doesn't care so it's not an issue.

Essentially, your GPU and Audio must be on the same ``Bus`` in the Virtual Machine, but your Audio must be a ``Function`` of the Virtual Machines GPU. Thus creating a multifunction GPU in the VM which has an accompanying Audio device. This displays the GPU as a single unit. Allowing for HDMI/DP Audio in macOS.


Go Ahead and select your GPU from the left-hand side. Note the Bus assigned.

<p align="center">
  <img src="./Assets/VManGPUPassthroughFindGPUBus.png">
</p>

Go Ahead and select your Audio from the left-hand side. Note the Bus assigned does not line up with that which was assigned to the GPU. We will now correct it to the same value. You must also switch the ``0x0`` to ``0x1`` to assign the Audio as a ``Function`` of the GPU device.

<p align="center">
  <img src="./Assets/VManGPUPassthroughCorrectAudioBusBefore.png">
</p>

The corrected Audio device:

<p align="center">
  <img src="./Assets/VManGPUPassthroughCorrectAudioBusAfter.png">
</p>

Now you can see the GPU is a multifunction device.

<p align="center">
  <img src="./Assets/VManGPUPassthroughCorrectedGPUMultifunction.png">
</p>

Go ahead and add your USB Controllers as you'll need to use your Keyboard and Mouse now.

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddUSBController1.png">
</p>

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddUSBController2.png">
</p>

If you have two NVMEs in your system, and you'd like to dedicate one completely to the installation of macOS for maximum performance, you can add your NVME drive now. It still needs to be supported by macOS, or at least not reported to be problematic with macOS. I won't be adding it, but here it is for the example.

<p align="center">
  <img src="./Assets/VManGPUPassthroughAddNVME.png">
</p>

If you've gone ahead and verified everything, your IOMMU groups, and your multifunction GPU, you are now ready! Here's an example of what a completed GPU Passthrough Virt-Manager will look like.

<p align="center">
  <img src="./Assets/VManGPUPassthroughCompletedExample.png">
</p>

<br>
<br>
<h1 align="center">Thanks for reading!</h1>
<h2 align="center">For further customization please refer to Submodules</h2>
<h4 align="center">Enjoy screenshots of Sonoma with GPU Accel. Don't forget to add the bootflag!</h4>

<p align="center">
  <img src="./Assets/macOSSonomaGPUAccel.png">
</p>
