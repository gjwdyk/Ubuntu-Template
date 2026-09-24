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

> **Note:** The example in this section use `ubuntu-26.04-live-server-amd64.iso`. If you downloaded a different Ubuntu Server release, the file name and the "detected" message in this Step 4 will differ accordingly.

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
This is only a placeholder: the network adapters are reconfigured in detail later, in *Customize Hardware* (Steps 15 to 19).

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

The virtual machine is now ready to be powered on and to boot from the `.iso`, which continues with the Ubuntu installation wizard (covered in the next part of this guide).

<br><br><br>

***

## Replicate Main Node into Multiple Nodes

Blah Blah Blah.

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


