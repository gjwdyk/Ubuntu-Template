# Ubuntu Template on VMware WorkStation

<br><br><br>
```
╔═╦═══════════════════════════════════════════════════════╦═╗
╠═╬═══════════════════════════════════════════════════════╬═╣
║ ║ Content of this Folder was Last Updated on 2026 09 24 ║ ║
╠═╬═══════════════════════════════════════════════════════╬═╣
╚═╩═══════════════════════════════════════════════════════╩═╝
```
<br><br><br>

Download the installation disc from :
- [ ] [Ubuntu Releases](http://releases.ubuntu.com/) (recommended because this page provides access to CheckSum files)
- [ ] [Download Ubuntu](https://ubuntu.com/download)

Do download the `.iso` file(s), either directly with your browser, or through your favorite download mechanism (example: torrent); and also do download the CheckSum file to verify that the downloaded `.iso` file is intact (not corrupted).
The CheckSum file contains checksum information for multiple files.

There are multiple versions of Ubuntu (example: Desktop, Server, WSL, etc.), for this document we are focusing only to Ubuntu Server.



<br><br><br>

***

## Main Node

### Create new VM from the `.iso` file

This part creates the (still empty) virtual machine in VMware Workstation Pro 25H2 and tunes its virtual hardware, so that it is ready to boot from the Ubuntu Server `.iso` file downloaded above.
The values shown in the screen captures are the values used in this guide; adjust them to your own needs (CPU, memory, disk size, network layout).

<br>

#### Step 1 — Start the New Virtual Machine wizard

In VMware Workstation, open the **File** menu and choose **New Virtual Machine...** (shortcut: `Ctrl+N`).

![VMware New VM](20260412152806VMwareNewVM.png)

#### Step 2 — Choose the *Custom (advanced)* configuration

Select **Custom (advanced)** and click **Next >**.
*Typical* would create the VM in fewer steps, but *Custom* lets us pick the SCSI controller type, the virtual disk type, and the hardware compatibility version ourselves.

![VMware New VM Wizard Custom](20260412153044VMwareNewVMWizardCustom.png)

#### Step 3 — Choose the hardware compatibility

In this guide **Workstation 15.x** is selected as the *Hardware compatibility*, which keeps the VM usable with a wide range of VMware products (the *Compatible products* list on the left).
The trade-off is shown in the *Limitations* list on the right: up to 64 GB memory, 16 processors, 10 network adapters, 8 TB disk size, and 3 GB shared graphics memory (no DirectX 11 support).
Those limits are more than enough for a server VM.
Click **Next >**.

![VMware New VM Wizard WorkStation 15.x](20260412153232VMwareNewVMWizardWorkStation15.x.png)

#### Step 4 — Point the wizard to the installer `.iso`

Select **Installer disc image file (iso)**, then use **Browse...** to pick the Ubuntu Server `.iso` file you downloaded.

Workstation reads the image and reports **Ubuntu 64-bit Server 26.04 detected**.

> **Note:** The example in this section uses `ubuntu-26.04-live-server-amd64.iso`. If you downloaded a different Ubuntu Server release, the file name and the "detected" message in this Step 4 will differ accordingly.

The wizard also mentions *Easy Install* (it asks to insert the first disc of the set).
This guide does not use Easy Install; the Ubuntu installer will be run manually the first time the VM is booted.

![VMware New VM Wizard Disc Image](20260424172219VMwareNewVMWizardDiscImage.png)

#### Step 5 — Name the virtual machine and choose where to store it

Give the VM a descriptive **Virtual machine name** (here: `Ubuntu Server 26.04`) and choose the **Location** where its files will be stored.

![VMware New VM Wizard VM Name](20260424172419VMwareNewVMWizardVMName.png)

#### Step 6 — Processor configuration

Set the number of processors and the number of cores per processor.
This guide uses **2 processors** with **1 core per processor**, for a total of 2 processor cores.
The value can be changed later in the VM settings if the workload requires it.

![VMware New VM Wizard Processor](20260412153929VMwareNewVMWizardProcessor.png)

#### Step 7 — Memory

Set the memory for the VM (it must be a multiple of 4 MB).
This guide uses **4096 MB (4 GB)**, which matches the *Recommended memory* marker in the wizard.
The *Guest OS recommended minimum* is 2 GB, and the *Maximum recommended memory* (55.5 GB in the capture) depends on the RAM installed in your host computer.

![VMware New VM Wizard Memory](20260412154007VMwareNewVMWizardMemory.png)

#### Step 8 — Network type

Select **Use network address translation (NAT)** for now.
This is only a placeholder: the network adapters are reconfigured in detail later, in *Customize Hardware* (Steps 15 to 18).

![VMware New VM Wizard Network](20260412154104VMwareNewVMWizardNetwork.png)

#### Step 9 — I/O controller type

Keep **LSI Logic (Recommended)** as the SCSI controller.
*BusLogic* is greyed out because it is not available for 64-bit guests; *LSI Logic SAS* and *Paravirtualized SCSI* are also offered but are not used here.

![VMware New VM Wizard IO Controller](20260412154142VMwareNewVMWizardIOController.png)

#### Step 10 — Virtual disk type

Keep **SCSI (Recommended)** as the virtual disk type (the other choices are IDE, SATA, and NVMe).

![VMware New VM Wizard Disk Type](20260412154222VMwareNewVMWizardDiskType.png)

#### Step 11 — Select the disk

Choose **Create a new virtual disk**.
The other two choices reuse a previously configured virtual disk, or give the VM direct access to a physical disk (advanced, and requires administrator privileges).

![VMware New VM Wizard Select Disk](20260412154300VMwareNewVMWizardSelectDisk.png)

#### Step 12 — Disk capacity

Set the **Maximum disk size** (here: **1024 GB**; the wizard's recommended size for Ubuntu 64-bit is only 20 GB).
Leave **Allocate all disk space now** unchecked, so the virtual disk starts small and grows as data is written to it.
Keep **Store virtual disk as a single file** selected.
Splitting the disk into multiple files makes the VM easier to move to another computer, but may reduce performance with very large disks.

> **Note:** Because the disk is not pre-allocated, the maximum size is a ceiling, not an immediate cost. The host drive still needs enough free space for the disk file as it grows.

![VMware New VM Wizard Disk Capacity](20260412154340VMwareNewVMWizardDiskCapacity.png)

#### Step 13 — Disk file name

Accept the proposed name of the disk file (here: `Ubuntu Server 26.04.vmdk`).
The wizard confirms that one 1024 GB disk file will be created with this name, in the VM folder chosen in Step 5.

![VMware New VM Wizard Disk File](20260424172842VMwareNewVMWizardDiskFile.png)

#### Step 14 — Review, then click *Customize Hardware...*

The *Ready to Create Virtual Machine* page summarizes the settings chosen so far.
Note that the **Network Adapter** is still listed as **NAT** (from Step 8).
Leave **Power on this virtual machine after creation** unchecked, and click **Customize Hardware...** to adjust the network adapters and a few other devices before the VM is created.

![VMware New VM Wizard Customize Hardware](20260424173035VMwareNewVMWizardCustomizeHardware.png)

<br>

### Customize the virtual hardware

The **Hardware** dialog lists the devices of the VM (Memory, Processors, CD/DVD, Network Adapter, USB Controller, Sound Card, Display).
Select a device in the list on the left to edit its settings on the right.
In this guide the VM gets **four network adapters**, each one connected to its own virtual network (`VMnet8`, `VMnet9`, `VMnet7`, and `VMnet6`).

Kindly refer to the [Virtual Networks](VMnet) section for more details on each VMnet's configurations and the generic purposes.

> **Note:** For this Ubuntu template, `VMnet8`, `VMnet9`, `VMnet7` and `VMnet6` must already exist and be configured in the host's **Virtual Network Editor** (Workstation: **Edit > Virtual Network Editor...**, which needs administrator rights via *Change Settings*). Again, kindly refer to [Virtual Networks](VMnet) for details.

This is what the first *Network Adapter* looks like by default: **Connect at power on** is checked and the connection type is **NAT** (share the host's IP address).
The available connection types are Bridged, NAT, Host-only, **Custom: Specific virtual network**, and LAN segment.

![VMware New VM Wizard Hardware Network Adapter NAT](20260412154939VMwareNewVMWizardHardwareNetworkAdapterNAT.png)

#### Step 15 — Network Adapter 1: VMnet8

Keep **Connect at power on** checked, change the connection type to **Custom: Specific virtual network**, and select **VMnet8** from the drop-down list.

![VMware New VM Wizard Hardware Network Adapter VMnet8](20260424173147VMwareNewVMWizardHardwareNetworkAdapterVMnet8.png)

#### Step 16 — Add more network adapters

To add another adapter, click **Add...** at the bottom of the device list, select **Network Adapter** in the *Add Hardware Wizard*, and click **Finish**.
Repeat this for each additional adapter (three more in this guide).
A newly added adapter defaults to NAT, so each one needs to be set to its own virtual network, as in the next steps.

![VMware New VM Wizard Hardware Add Network Adapter](20260412155223VMwareNewVMWizardHardwareAddNetworkAdapter.png)

#### Step 17 — Network Adapter 2: VMnet9

Select **Network Adapter 2**, choose **Custom: Specific virtual network**, and select **VMnet9**.

![VMware New VM Wizard Hardware Network Adapter2 VMnet9](20260424173254VMwareNewVMWizardHardwareNetworkAdapter2VMnet9.png)

#### Step 18 — Network Adapters 3 and 4: VMnet7 and VMnet6

Do the same for the remaining adapters:
- **Network Adapter 3** → **Custom: VMnet7**
- **Network Adapter 4** → **Custom: VMnet6**

![VMware New VM Wizard Hardware Network Adapter3 VMnet7](20260424173335VMwareNewVMWizardHardwareNetworkAdapter3VMnet7.png)

![VMware New VM Wizard Hardware Network Adapter4 VMnet6](20260412155534VMwareNewVMWizardHardwareNetworkAdapter4VMnet6.png)

#### Step 19 — USB Controller

Select **USB Controller**. Here the **USB compatibility** is set to **USB 3.2**, and **Show all USB input devices** is left unchecked.

![VMware New VM Wizard Hardware USB Controller](20260412155608VMwareNewVMWizardHardwareUSBController.png)

#### Step 20 — Display

Select **Display**.
Since this is a server VM without a graphical desktop, **Accelerate 3D graphics** is left unchecked and the monitors use the **host setting**.
Under *Display scaling*, **Stretch mode** is enabled with **Keep aspect ratio stretch**, so that the VM screen scales to the window while keeping the correct proportions.

Click **Close** to leave the *Hardware* dialog.

![VMware New VM Wizard Hardware Display](20260412155654VMwareNewVMWizardHardwareDisplay.png)

<br>

### Create the VM and adjust the final VM settings

#### Step 21 — Review the final configuration and click *Finish*

Back in the wizard, the summary now lists the customized **Network Adapter** entries (Custom: VMnet7, VMnet9, VMnet6, and so on; the list is cut off in the capture).
Leave **Power on this virtual machine after creation** unchecked (there is one more setting to check before the first boot), and click **Finish**.

![VMware New VM Wizard Hardware Result](20260424173658VMwareNewVMWizardHardwareResult.png)

#### Step 22 — The new VM in Workstation

The VM now appears in Workstation with its state **Powered off**.
The *Devices* list confirms the configuration: 4 GB memory, 2 processors, a 1 TB SCSI hard disk, the CD/DVD (SATA) attached to the `.iso` file, and the four network adapters (Network Adapter → VMnet8, Network Adapter 2 → VMnet9, Network Adapter 3 → VMnet7, Network Adapter 4 → VMnet6).
The *Virtual Machine Details* also show the location of the configuration file (`.vmx`) and the hardware compatibility (Workstation 15.x).

> **Note:** Do not click **Upgrade this virtual machine** if you want to keep the Workstation 15.x hardware compatibility chosen in Step 3.

To open the remaining settings, click **Edit virtual machine settings**.

![VMware Ubuntu Server 26.04](20260424173901VMwareUbuntuServer26.04.png)

#### Step 23 — Options > Advanced

In the *Virtual Machine Settings* dialog, open the **Options** tab and select **Advanced**.

- **Enable side channel mitigations for Hyper-V enabled hosts** is left **unchecked** (disabled).
  This option only matters if the Windows host runs with Hyper-V enabled. Turning it on applies extra protection against side-channel (CPU speculative execution) attacks inside the VM, at a cost in performance. Leaving it off, as in this guide, favors performance; consider enabling it if the VM runs untrusted workloads.
- **Firmware type** is left at **BIOS** (the alternative is UEFI, with the optional *Secure Boot*).
  Decide this now: as the dialog warns, changing the firmware after the guest OS is installed might make it unbootable.

Click **OK** to save the settings.

![VMware VM Settings Options Advanced DISABLE Side Channel Mitigations](20260424174010VMwareVMSettingsOptionsAdvancedDISABLESideChannelMitigations.png)

<br>

#### Summary of the resulting VM

| Item | Value |
|---|---|
| Name | `Ubuntu Server 26.04` |
| Hardware compatibility | Workstation 15.x |
| Guest OS type | Ubuntu 64-bit |
| Processors | 2 (2 processors × 1 core) |
| Memory | 4096 MB |
| Disk | 1024 GB (SCSI, LSI Logic controller), not pre-allocated, single `.vmdk` file |
| CD/DVD | SATA, attached to the Ubuntu Server `.iso` |
| Network adapters | 4, all *Custom*: VMnet8, VMnet9, VMnet7, VMnet6 |
| USB | USB 3.2 |
| Display | 3D acceleration off, stretch mode (keep aspect ratio) |
| Firmware | BIOS |
| Side channel mitigations (Hyper-V hosts) | Off |

The virtual machine is now ready to be powered on and to boot from the `.iso`, which continues with the Ubuntu installation wizard in the next section.

<br><br><br>

***

### Install Ubuntu VM Guest OS from the `.iso` file

This part boots the virtual machine created above and runs the Ubuntu Server installer wizard, from the boot menu up to the first login over SSH.
The example below uses four network interfaces, matching the four network adapters configured in the *Customize the virtual hardware* section; adjust the steps if your VM has a different number of interfaces.

<br>

#### Step 24 — Boot the VM and start the installer

Power on the VM. It boots from the attached `.iso` into the GRUB menu, with **Try or Install Ubuntu Server** highlighted.
The entry boots automatically after 10 seconds if left untouched; press **Enter** to start it immediately.

![Ubuntu Try Or Install](20260427221148UbuntuTryOrInstall.png)

#### Step 25 — Select the installer language

Choose **English** (or the language of your choice) and press **Enter**.

![Ubuntu Select Language](20260427221244UbuntuSelectLanguage.png)

#### Step 26 — Confirm the keyboard layout

Keep **Layout** and **Variant** at **English (US)** (or use **Identify keyboard** to detect it automatically), then select **Done**.

![Ubuntu Keyboard Layout](20260427221335UbuntuKeyboardLayout.png)

#### Step 27 — Choose the type of installation

Keep **Ubuntu Server** selected (the default, full install).
**Ubuntu Server (minimized)** trims the runtime footprint for environments with no interactive login, which does not apply here.
Leave **Search for third-party drivers** unchecked, then select **Done**.

![Ubuntu Type Of Install](20260427221457UbuntuTypeOfInstall.png)

<br>

### Configure the network interfaces

The installer lists one entry per network adapter attached to the VM: `ens33`, `ens34`, `ens35`, `ens36` in this guide, corresponding respectively to **Network Adapter** (VMnet8), **Network Adapter 2** (VMnet9), **Network Adapter 3** (VMnet7), and **Network Adapter 4** (VMnet6) from the *Customize the virtual hardware* section.

Finding the association between Ubuntu's Network Adapter (e.g. `ens33`) with the VMware's Network Adapter (e.g. VMnet8) is useful
for you to get knowledge which subnet to be configured for each Ubuntu's Network Adapter (e.g. `ens33`). Referring to [Virtual Networks](VMnet) section, for example, we gain the knowledge that VMnet8's subnet was defined as `192.168.123.0/24`. And therefore `ens33` needs to be configured around the same subnet `192.168.123.0/24`, in order for the network adapter to work properly.

> **Tip:** To confirm which `ensXX` name belongs to which VMnet, match the MAC address shown next to each interface in the installer (for example `00:0c:29:de:23:30`) against the **MAC Address** field in that adapter's **Advanced Settings** in VMware (**Edit virtual machine settings > Hardware**, select the adapter, click **Advanced...**). This is why the captures below alternate between the VMware hardware dialog and the Ubuntu installer.

All four interfaces start out **disabled**, with **autoconfiguration failed** (there is no DHCP server to answer them yet), so each one is set to a static address in turn.

#### Step 28 — Network configuration overview

The **Network configuration** screen lists all four interfaces, all disabled.
Selecting an interface's `▸` opens a menu with **Info**, **Edit IPv4**, **Edit IPv6**, and **Add a VLAN tag**.

![Ubuntu Network Configuration](20260427221544UbuntuNetworkConfiguration.png)

#### Step 29 — Configure `ens33` (VMnet8)

In VMware, **Network Adapter** is confirmed as **Custom: VMnet8 (Host-only)**, with MAC address `00:0C:29:DE:23:30`.

![VMware Hardware Network Adapter VMnet8](20260427221709VMwareHardwareNetworkAdapterVMnet8.png)
![VMware Hardware Network Adapter VMnet8 Advanced Settings](20260427221729VMwareHardwareNetworkAdapterVMnet8AdvancedSettings.png)

Back in the installer, the MAC address `00:0c:29:de:23:30` identifies this entry as `ens33`.
Select `ens33 > Edit IPv4`.

![Ubuntu Network Configuration ENS33](20260427221935UbuntuNetworkConfigurationENS33.png)

Change the **IPv4 Method** from *Automatic (DHCP)* to **Manual**.

![Ubuntu Network Configuration ENS33 IPv4 Manual](20260427222050UbuntuNetworkConfigurationENS33IPv4Manual.png)

Referring to [Virtual Networks](VMnet) section, we gain the knowledge that VMnet8's subnet was defined as `192.168.123.0/24`. And therefore `ens33` needs to be configured around the same subnet `192.168.123.0/24`, in order for the network adapter to work properly.

Fill in the **Subnet** and **Address** (here: `192.168.123.0/24` and `192.168.123.38`).
**Gateway** and **Name servers** are left blank on this interface, since it is a host-only network with no route to the internet.
Select **Save**.

![Ubuntu Network Configuration ENS33 IPv4 Configuration](20260427222216UbuntuNetworkConfigurationENS33IPv4Configuration.png)

#### Step 30 — Configure `ens34` (VMnet9)

**Network Adapter 2** is confirmed as **Custom: VMnet9 (NAT)**, with MAC address `00:0C:29:DE:23:3A`.

![VMware Hardware Network Adapter VMnet9](20260427225740VMwareHardwareNetworkAdapterVMnet9.png)
![VMware Hardware Network Adapter VMnet9 Advanced Settings](20260427225751VMwareHardwareNetworkAdapterVMnet9AdvancedSettings.png)

The matching MAC identifies `ens34`. Select `ens34 > Edit IPv4`.

![Ubuntu Network Configuration ENS34](20260427222241UbuntuNetworkConfigurationENS34.png)

Change the **IPv4 Method** to **Manual**.

![Ubuntu Network Configuration ENS34 IPv4 Manual](20260427222259UbuntuNetworkConfigurationENS34IPv4Manual.png)

Referring to [Virtual Networks](VMnet) section, we gain the knowledge that VMnet9's subnet was defined as `192.168.101.0/24`. And therefore `ens34` needs to be configured around the same subnet `192.168.101.0/24`. More over from [Virtual Networks](VMnet) section, we learn that VMnet9 also have NAT Default Gateway which also serves as DNS.

Fill in **Subnet** `192.168.101.0/24`, **Address** `192.168.101.38`, **Gateway** `192.168.101.8`, and **Name servers** `192.168.101.8`.
Because this interface sits on the NAT network, it is the one used to reach the outside world (and to fetch package updates during the rest of the installation), which is why it is the only interface configured with a gateway and a name server here.
Select **Save**.

![Ubuntu Network Configuration ENS34 IPv4 Configuration](20260427222413UbuntuNetworkConfigurationENS34IPv4Configuration.png)

#### Step 31 — Configure `ens35` (VMnet7)

**Network Adapter 3** is confirmed as **Custom: VMnet7**, with MAC address `00:0C:29:DE:23:44`.

![VMware Hardware Network Adapter VMnet7](20260427225809VMwareHardwareNetworkAdapterVMnet7.png)
![VMware Hardware Network Adapter VMnet7 Advanced Settings](20260427225829VMwareHardwareNetworkAdapterVMnet7AdvancedSettings.png)

The matching MAC identifies `ens35`. Select `ens35 > Edit IPv4`.

![Ubuntu Network Configuration ENS35](20260427222556UbuntuNetworkConfigurationENS35.png)

Change the **IPv4 Method** to **Manual**.

![Ubuntu Network Configuration ENS35 IPv4 Manual](20260427222613UbuntuNetworkConfigurationENS35IPv4Manual.png)

Referring to [Virtual Networks](VMnet) section, we gain the knowledge that VMnet7's subnet was defined as `192.168.111.0/24`. And therefore `ens35` needs to be configured around the same subnet `192.168.111.0/24`, in order for the network adapter to work properly.

Fill in **Subnet** `192.168.111.0/24` and **Address** `192.168.111.38`, again leaving **Gateway** and **Name servers** blank.
Select **Save**.

![Ubuntu Network Configuration ENS35 IPv4 Configuration](20260427222646UbuntuNetworkConfigurationENS35IPv4Configuration.png)

#### Step 32 — Configure `ens36` (VMnet6)

**Network Adapter 4** is confirmed as **Custom: VMnet6**, with MAC address `00:0C:29:DE:23:4E`.

![VMware Hardware Network Adapter VMnet6](20260427225847VMwareHardwareNetworkAdapterVMnet6.png)
![VMware Hardware Network Adapter VMnet6 Advanced Settings](20260427225859VMwareHardwareNetworkAdapterVMnet6AdvancedSettings.png)

The matching MAC identifies `ens36`. Select `ens36 > Edit IPv4`.

![Ubuntu Network Configuration ENS36](20260427222706UbuntuNetworkConfigurationENS36.png)

Change the **IPv4 Method** to **Manual**.

![Ubuntu Network Configuration ENS36 IPv4 Manual](20260427222721UbuntuNetworkConfigurationENS36IPv4Manual.png)

Again, with reference to [Virtual Networks](VMnet) section, fill in **Subnet** `192.168.222.0/24` and **Address** `192.168.222.38`, leaving **Gateway** and **Name servers** blank.
Select **Save**.

![Ubuntu Network Configuration ENS36 IPv4 Configuration](20260427222752UbuntuNetworkConfigurationENS36IPv4Configuration.png)

#### Step 33 — Finish network configuration

All four interfaces now show **static** with their configured address.
Select **Done** to continue.

![Ubuntu Network Configuration Completed](20260427222844UbuntuNetworkConfigurationCompleted.png)

<br>

### Proxy, mirror, storage, and account

#### Step 34 — Proxy configuration

Leave the **Proxy address** blank unless your network requires an HTTP proxy to reach the internet, then select **Done**.

![Ubuntu Proxy Configuration](20260427222903UbuntuProxyConfiguration.png)

#### Step 35 — Ubuntu archive mirror configuration

Keep the default **Mirror address** (`http://archive.ubuntu.com/ubuntu/`), unless you use an alternative mirror.
The installer tests the location and reports **This mirror location passed tests**, then fetches the package lists.
Select **Done**.

![Ubuntu Archive Mirror Configuration](20260427222943UbuntuArchiveMirrorConfiguration.png)

#### Step 36 — Installer update available

The installer may report a newer version of itself (here: 26.10-devel, while 26.04 is running).
This guide selects **Continue without updating**, so the installation proceeds with the installer that shipped on the `.iso`.

![Ubuntu Installer Update Available](20260427223026UbuntuInstallerUpdateAvailable.png)

#### Step 37 — Guided storage configuration

Keep **Use an entire disk** selected, with the VM's virtual disk (here: `/dev/sda`, 1.000T) chosen below it.
**Set up this disk as an LVM group** is checked, which allows the root filesystem to be resized later.
**Encrypt the LVM group with LUKS** is left unchecked in this guide; enable it if the disk should be encrypted at rest.
Select **Done**.

![Ubuntu Guided Storage Configuration](20260427223142UbuntuGuidedStorageConfiguration.png)

#### Step 38 — Review the storage layout

The guided layout creates a 2 GB `/boot` partition and a 100 GB `/` logical volume inside an `ubuntu-vg` LVM volume group, leaving the remaining space (about 922 GB here) as free space in the volume group for later use (for example, to extend `/` with `lvextend` and `resize2fs`/`resizefs` once the system is running).
Select **Done**.

![Ubuntu Storage Configuration](20260427223226UbuntuStorageConfiguration.png)

#### Step 39 — Confirm the destructive action

The installer warns that continuing will format the selected disk and that this step cannot be undone.
Select **Continue**.

![Ubuntu Storage Configuration Confirmation](20260427223309UbuntuStorageConfigurationConfirmation.png)

#### Step 40 — Profile configuration

Enter **Your name**, **Your servers name** (the hostname), **Pick a username**, and a password (entered twice).
This guide uses `ubuntu` for the name, server name, and username; use your own values for anything other than a disposable lab/demo VM.
Select **Done**.

![Ubuntu Profile Configuration](20260427223359UbuntuProfileConfiguration.png)

#### Step 41 — Skip the Ubuntu Pro upgrade

Ubuntu Pro extends security patch coverage and adds compliance tooling; **About Ubuntu Pro** summarizes what it includes.
This guide selects **Skip for now** — it can be enabled later with `sudo pro attach`.
Select **Continue**.

![Ubuntu Upgrade To Pro](20260427223422UbuntuUpgradeToPro.png)
![Ubuntu Upgrade To Pro About](20260427223510UbuntuUpgradeToProAbout.png)
![Ubuntu Upgrade To Pro Skip 4 Now](20260427223613UbuntuUpgradeToProSkip4Now.png)

#### Step 42 — SSH configuration

Check **Install OpenSSH server** so the VM can be reached remotely once it reboots.
**Allow password authentication over SSH** is left checked, and no key is imported under **Import SSH key**.
Select **Done**.

> **Note:** Allowing password authentication is convenient for a lab/demo VM but is not a hardened setting. For anything beyond local testing, consider importing a public key here instead and disabling password authentication afterwards.

![Ubuntu SSH Configuration](20260427223700UbuntuSSHConfiguration.png)

#### Step 43 — Featured server snaps

None of the optional snaps (Kubernetes, Nextcloud, Docker-adjacent tools, and so on) are selected in this guide.
Select **Done** to skip them; they can be installed later with `snap install`.

![Ubuntu Featured Server Snaps](20260427223801UbuntuFeaturedServerSnaps.png)

<br>

### Finish the installation and reboot

#### Step 44 — Watch the installation progress

The installer partitions and formats the disk, extracts the system, installs the kernel and OpenSSH server, configures cloud-init, and applies security updates.
**View full log** shows the complete output if you want to check the details of any step.

![Ubuntu Installation Complete](20260427224141UbuntuInstallationComplete.png)
![Ubuntu Installation Complete Full Log](20260427224103UbuntuInstallationCompleteFullLog.png)
![Ubuntu Installation Complete](20260427224018UbuntuInstallationComplete.png)

#### Step 45 — Reboot

Once the log reaches the end, select **Reboot Now**.

![Ubuntu Installation Complete Reboot Now](20260427224206UbuntuInstallationCompleteRebootNow.png)

#### Step 46 — Remove the installation medium

The VM halts briefly with **Please remove the installation medium, then press ENTER**.
Switch to VMware Workstation to detach the `.iso` before continuing (see the next step); do not press **Enter** yet.

![Ubuntu Remove Installation Medium](20260427224237UbuntuRemoveInstallationMedium.png)

#### Step 47 — Detach the ISO in VMware

Open **Edit virtual machine settings > Hardware > CD/DVD (SATA)** and detach the installer image, either or both of:
- Under **Connection**, switch from **Use ISO image file** to **Use physical drive: Auto detect**, so the virtual drive no longer points at the installer `.iso`.
- Uncheck **Connect at power on** (and, if the VM is running, also **Connected**) under **Device status**, so the CD/DVD device is not attached to the VM at all.

Either change stops the VM from booting the installer again; this guide applies both.
Click **OK**.

![VMware Hardware DVD](20260427225638VMwareHardwareDVD.png)

#### Step 48 — Continue the boot

Back in the VM console, press **Enter** at the same prompt to continue booting into the freshly installed system.

![Ubuntu Remove Installation Medium](20260427224357UbuntuRemoveInstallationMedium.png)

#### Step 49 — First login

The VM boots Ubuntu 26.04 LTS.
On this first boot, `cloud-init` generates the host's SSH keys (ECDSA and Ed25519 shown here) before presenting the `ubuntu login:` prompt on `tty1`.
Log in with the username and password set in Step 40.

![Ubuntu Login](20260427224438UbuntuLogin.png)

#### Step 50 — Connect over SSH

From the host (or another machine on the same network), connect to one of the VM's static addresses — here, `192.168.123.38` (`ens33`, VMnet8) on port 22 with PuTTY.

![PuTTY Session](20260427224523PuTTYSession.png)

On the first connection to a given address, PuTTY has no cached host key and shows a **PuTTY Security Alert** with the server's key fingerprint.
This is expected on a first connection: the fingerprint matches the Ed25519 key generated at first boot in Step 49.
Select **Accept** to cache the key and continue (or **Connect Once** to skip caching just for this session).

![PuTTY Security Alert](20260427224536PuTTYSecurityAlert.png)

After entering the username and password, the VM's message of the day confirms the Ubuntu version, kernel, disk usage, memory usage, and one of its IPv4 addresses.

![PuTTY Ubuntu](20260427224604PuTTYUbuntu.png)

<br>

#### Summary of the installed Ubuntu Guest OS

| Item | Value |
|---|---|
| Hostname / username | `ubuntu` / `ubuntu` |
| Network (`ens33`) | `192.168.123.38/24` on VMnet8, No Gateway |
| Network (`ens34`) | `192.168.101.38/24` on VMnet9, NAT Default Gateway/DNS `192.168.101.8` |
| Network (`ens35`) | `192.168.111.38/24` on VMnet7, No Gateway |
| Network (`ens36`) | `192.168.222.38/24` on VMnet6, No Gateway |
| Storage | LVM on `/dev/sda`: `/boot` 2 GB, `/` 100 GB (ext4), ~922 GB free in `ubuntu-vg` |
| Encryption | None (LUKS not enabled) |
| Ubuntu Pro | Not enabled |
| SSH | OpenSSH server installed, password authentication allowed, no key imported |
| Snaps | None of the featured snaps installed |

The virtual machine is now installed, network-configured, and reachable over SSH, which is the starting point for cloning it into multiple nodes (covered in the next section).


<br><br><br>

***

## Replicate Main Node into Multiple Nodes

> **Note:** This section is a text-only placeholder, written without screen captures or a CLI transcript, so it can be replaced once those are available. It describes the idea and the commands involved; adapt the exact values to your own node names and addresses.

Cloning the VM created above is the fast way to produce several Ubuntu Server guests without repeating the installer wizard for each one. The main node was configured with fixed values (hostname `ubuntu`, addresses ending in `.38` on four networks, and SSH host keys generated at first boot), so a straight file copy would give every clone the exact same identity. The steps below are about creating the copy in VMware, and then giving each copy its own identity before it goes on the network.

#### 1. Shut down the main node cleanly

Before cloning, shut down the VM properly (`sudo shutdown now`, or **Power > Shut Down Guest** in VMware) rather than suspending it, so the clone starts from a clean filesystem state.

#### 2. Clone the VM in VMware Workstation

With the main node selected and powered off, use **VM > Manage > Clone...**.
- A **Linked clone** is fast to create and saves disk space, but depends on the original VM's disk staying in place.
- A **Full clone** copies the entire virtual disk and can run independently of the original, which is usually the better choice for nodes meant to be kept long-term.

Give each clone its own name and location, and repeat for as many nodes as you need.

#### 3. Give each clone a unique MAC address

Each cloned network adapter can keep the original's MAC address, which would create a conflict as soon as two VMs are on the same virtual network. In each clone's **Hardware > Network Adapter > Advanced...**, click **Generate** to assign a new MAC address to every adapter.

#### 4. Boot the clone and set a unique hostname

Log in with the same username and password as the main node, then set a new hostname:

```
sudo hostnamectl set-hostname <new-hostname>
sudo nano /etc/hosts   # update the 127.0.1.1 line to match
```

#### 5. Regenerate the machine ID and SSH host keys

Both the machine ID and the SSH host keys were generated once, on the main node's first boot, and are carried over verbatim by the clone. Left alone, this means multiple nodes would share the same identity on the network (in particular, SSH clients would flag every node after the first as a possible impersonation, since they all present the same host key).

```
sudo rm /etc/machine-id
sudo systemd-machine-id-setup

sudo rm /etc/ssh/ssh_host_*
sudo dpkg-reconfigure openssh-server
```

#### 6. Update the static IP addresses

Edit the netplan configuration (typically `/etc/netplan/50-cloud-init.yaml` or similar, depending on how it was written during installation) and change the `addresses` for each interface to the values planned for this node, keeping the same gateway and DNS server on the interface that had them in Step 30 of the main node. Apply the change with:

```
sudo netplan apply
```

#### 7. Reconnect and verify

Connect over SSH to the new address. Since the host key was regenerated in Step 5, the client will treat this as a new host and show a fresh fingerprint prompt (as in Step 50 of the main node install), rather than a warning about a changed key.

Repeat Steps 3 to 7 for each additional node, giving each one its own hostname, MAC addresses, machine ID, host keys, and IP addresses.

<br><br><br>

***

<br><br><br>
```
╔═╦═════════════════╦═╗
╠═╬═════════════════╬═╣
║ ║ End of Document ║ ║
╠═╬═════════════════╬═╣
╚═╩═════════════════╩═╝
```
<br><br><br>


