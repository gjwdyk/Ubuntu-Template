# Ubuntu Template on VMware WorkStation

<br><br><br>
```
╔═╦═══════════════════════════════════════════════════════╦═╗
╠═╬═══════════════════════════════════════════════════════╬═╣
║ ║ Content of this Folder was Last Updated on 2026 07 22 ║ ║
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



![20260412152806VMwareNewVM.png](20260412152806VMwareNewVM.png)
![20260412153044VMwareNewVMWizardCustom.png](20260412153044VMwareNewVMWizardCustom.png)
![20260412153232VMwareNewVMWizardWorkStation15.x.png](20260412153232VMwareNewVMWizardWorkStation15.x.png)

![20260412153530VMwareNewVMWizardDiscImage.png](20260412153530VMwareNewVMWizardDiscImage.png)
![20260412153846VMwareNewVMWizardDiscImage.png](20260412153846VMwareNewVMWizardDiscImage.png)
![20260424172219VMwareNewVMWizardDiscImage.png](20260424172219VMwareNewVMWizardDiscImage.png)

![20260412153712VMwareNewVMWizardVMName.png](20260412153712VMwareNewVMWizardVMName.png)
![20260424172419VMwareNewVMWizardVMName.png](20260424172419VMwareNewVMWizardVMName.png)

![20260412153929VMwareNewVMWizardProcessor.png](20260412153929VMwareNewVMWizardProcessor.png)
![20260412154007VMwareNewVMWizardMemory.png](20260412154007VMwareNewVMWizardMemory.png)
![20260412154104VMwareNewVMWizardNetwork.png](20260412154104VMwareNewVMWizardNetwork.png)
![20260412154142VMwareNewVMWizardIOController.png](20260412154142VMwareNewVMWizardIOController.png)
![20260412154222VMwareNewVMWizardDiskType.png](20260412154222VMwareNewVMWizardDiskType.png)
![20260412154300VMwareNewVMWizardSelectDisk.png](20260412154300VMwareNewVMWizardSelectDisk.png)
![20260412154340VMwareNewVMWizardDiskCapacity.png](20260412154340VMwareNewVMWizardDiskCapacity.png)

![20260412154430VMwareNewVMWizardDiskFile.png](20260412154430VMwareNewVMWizardDiskFile.png)
![20260424172842VMwareNewVMWizardDiskFile.png](20260424172842VMwareNewVMWizardDiskFile.png)

![20260412154809VMwareNewVMWizardCustomizeHardware.png](20260412154809VMwareNewVMWizardCustomizeHardware.png)
![20260424173035VMwareNewVMWizardCustomizeHardware.png](20260424173035VMwareNewVMWizardCustomizeHardware.png)

![20260412154939VMwareNewVMWizardHardwareNetworkAdapterNAT.png](20260412154939VMwareNewVMWizardHardwareNetworkAdapterNAT.png)

![20260412155036VMwareNewVMWizardHardwareNetworkAdapterVMnet8.png](20260412155036VMwareNewVMWizardHardwareNetworkAdapterVMnet8.png)
![20260424173147VMwareNewVMWizardHardwareNetworkAdapterVMnet8.png](20260424173147VMwareNewVMWizardHardwareNetworkAdapterVMnet8.png)

![20260412155223VMwareNewVMWizardHardwareAddNetworkAdapter.png](20260412155223VMwareNewVMWizardHardwareAddNetworkAdapter.png)

![20260412155321VMwareNewVMWizardHardwareNetworkAdapter2VMnet9.png](20260412155321VMwareNewVMWizardHardwareNetworkAdapter2VMnet9.png)
![20260424173254VMwareNewVMWizardHardwareNetworkAdapter2VMnet9.png](20260424173254VMwareNewVMWizardHardwareNetworkAdapter2VMnet9.png)

![20260412155427VMwareNewVMWizardHardwareNetworkAdapter3VMnet7.png](20260412155427VMwareNewVMWizardHardwareNetworkAdapter3VMnet7.png)
![20260424173335VMwareNewVMWizardHardwareNetworkAdapter3VMnet7.png](20260424173335VMwareNewVMWizardHardwareNetworkAdapter3VMnet7.png)

![20260412155534VMwareNewVMWizardHardwareNetworkAdapter4VMnet6.png](20260412155534VMwareNewVMWizardHardwareNetworkAdapter4VMnet6.png)
![20260412155608VMwareNewVMWizardHardwareUSBController.png](20260412155608VMwareNewVMWizardHardwareUSBController.png)
![20260412155654VMwareNewVMWizardHardwareDisplay.png](20260412155654VMwareNewVMWizardHardwareDisplay.png)

![20260412155740VMwareNewVMWizardHardwareResult.png](20260412155740VMwareNewVMWizardHardwareResult.png)
![20260424173658VMwareNewVMWizardHardwareResult.png](20260424173658VMwareNewVMWizardHardwareResult.png)

![20260412155843VMwareUbuntuServer24.04.png](20260412155843VMwareUbuntuServer24.04.png)
![20260424173901VMwareUbuntuServer26.04.png](20260424173901VMwareUbuntuServer26.04.png)

![20260412160024VMwareVMSettingsOptionsAdvancedDISABLESideChannelMitigations.png](20260412160024VMwareVMSettingsOptionsAdvancedDISABLESideChannelMitigations.png)
![20260424174010VMwareVMSettingsOptionsAdvancedDISABLESideChannelMitigations.png](20260424174010VMwareVMSettingsOptionsAdvancedDISABLESideChannelMitigations.png)






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


