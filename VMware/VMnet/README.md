# Virtual Network Configurations

<br><br><br>
```
╔═╦═══════════════════════════════════════════════════════╦═╗
╠═╬═══════════════════════════════════════════════════════╬═╣
║ ║ Content of this Folder was Last Updated on 2026 09 24 ║ ║
╠═╬═══════════════════════════════════════════════════════╬═╣
╚═╩═══════════════════════════════════════════════════════╩═╝
```
<br><br><br>

Below are the descriptions of the Virtual Networks (VMnets) used within the templates, so readers can understand more on why the configurations of the Guest OS are they way they're described.

All of the VMnets shown here are configured in VMware Workstation's **Virtual Network Editor** (**Edit > Virtual Network Editor...**, which needs administrator rights via **Change Settings**). Every VMnet used by the guest OS (VMnet1 to VMnet9) is set up with **no DHCP** ("Use local DHCP service" left unchecked), so that addresses handed out in the installation guide, in scripts, and on the running Guest OS stay fixed and predictable rather than being reassigned on every boot. VMnet0 is the one exception, since it is a bridge to the physical network, which is normally outside this lab's control and is covered separately below.

| VMnet | Type | External connection | Host connection | DHCP | Subnet |
|---|---|---|---|---|---|
| VMnet0 | Bridged | Intel(R) Wi-Fi 7 BE201 320MHz | – | – | – |
| VMnet1 | Custom (isolated) | – | – | – | 192.168.191.0/24 |
| VMnet2 | Custom (isolated) | – | – | – | 192.168.181.0/24 |
| VMnet3 | Custom (isolated) | – | – | – | 192.168.151.0/24 |
| VMnet4 | Custom (isolated) | – | – | – | 192.168.212.0/24 |
| VMnet5 | Custom (isolated) | – | – | – | 192.168.234.0/24 |
| VMnet6 | Custom (isolated) | – | – | – | 192.168.222.0/24 |
| VMnet7 | Custom (isolated) | – | – | – | 192.168.111.0/24 |
| VMnet8 | Host-only | – | Connected | – | 192.168.123.0/24 |
| VMnet9 | NAT | NAT | Connected | – | 192.168.101.0/24 |

Four purposes sit behind this layout:

- **VMnet8 — administrative access.** A host-only network with the host's virtual adapter connected, used by the administrator (or by the host in general) to reach the Guest OS directly.
- **VMnet9 — outbound / internet access.** A NAT network, so Guest OS instances can reach external networks (typically the internet) through the host's connection. The host's virtual adapter is also connected here, which turns out to be useful for lab and demo troubleshooting: with the host on the same wire, packet captures with tools like tcpdump or Wireshark can be taken directly from the host side of the NAT network.
- **VMnet1 to VMnet7 — inter-Guest OS communication.** Seven isolated networks (no host adapter, no external connection), left free to be assigned to whatever role a given lab or demo needs. For example, in a BIG-IP lab, VMnet7 could carry the control-plane traffic between BIG-IP nodes (such as failover heartbeat), VMnet6 could carry client-side data from a client Guest OS to the active BIG-IP, and VMnet5 could carry the corresponding server-side data from the active BIG-IP to the server Guest OS. Any of VMnet1 to VMnet7 can be repurposed this way; the assignment is a convention of each lab, not a fixed rule of the network itself.
- **VMnet0 — bridged, last resort external access.** Added later, after corporate security policy began filtering both the types and destinations of traffic reachable from the host. That filtering is reasonable for protecting a corporate computer, but it gets in the way of lab and demo traffic. VMnet0 bridges a Guest OS as directly as possible to the physical network, minimizing the host's own involvement in handling that traffic. Because VMnet0 connects straight to an external network that is usually outside this guide's control, it is normally left to whatever DHCP server exists on that external network, rather than being fixed like VMnet1 to VMnet9.








<br><br><br>

***

## VMnet9

VMnet9 is set to **NAT**, sharing the host's IP address with the VMs on it. **Connect a host virtual adapter to this network** is checked, which is what lets the host itself join this network for the troubleshooting use described above. **Use local DHCP service** is left unchecked, so addresses on this network are assigned manually rather than handed out automatically. The subnet is `192.168.101.0/24`.

![Virtual Network Editor VMnet9](20260924083119VirtualNetworkEditorVMnet9.png)

**NAT Settings** fix the **Gateway IP** at `192.168.101.8` — this is the address used as the gateway on the Guest OS side (see `ens34` in the installation guide). Port forwarding is left empty, and the advanced options (active FTP, OUI filtering, UDP timeout, IPv6) are left at their defaults.

![Virtual Network Editor VMnet9 NAT Settings](20260924083141VirtualNetworkEditorVMnet9NATSettings.png)

**DNS Settings** are left on **Auto detect available DNS servers**, so NAT'd traffic uses whichever DNS servers the host itself is configured with.

![Virtual Network Editor VMnet9 NAT Settings DNS](20260924083217VirtualNetworkEditorVMnet9NATSettingsDNS.png)

**NetBIOS Settings** are left at their default timeout and retry values; NetBIOS name resolution is not a concern for this lab.

![Virtual Network Editor VMnet9 NAT Settings NetBIOS](20260924083238VirtualNetworkEditorVMnet9NATSettingsNetBIOS.png)

## VMnet8

VMnet8 is set to **Host-only**, keeping VMs on this network internal with no external connection. **Connect a host virtual adapter to this network** is checked, which is what lets the host (and, through it, the administrator) reach the Guest OS directly on this network. **Use local DHCP service** is left unchecked. The subnet is `192.168.123.0/24`.

![Virtual Network Editor VMnet8](20260924083259VirtualNetworkEditorVMnet8.png)

## VMnet1 to VMnet7

VMnet1 through VMnet7 are all set to **Host-only** as well, but unlike VMnet8, **Connect a host virtual adapter to this network** is left unchecked on each of them — the host itself has no presence on these networks. This isolates them for inter-Guest OS traffic only, with no route to or from the host or the outside world. **Use local DHCP service** is unchecked on all seven, and each has its own fixed `/24` subnet, as listed in the table above.

Which of VMnet1 to VMnet7 is used for what is a convention of the lab or demo being built, not a fixed assignment; the example given above (VMnet7 for BIG-IP control-plane/heartbeat traffic, VMnet6 for client-side data, VMnet5 for server-side data) is one such convention, not a requirement.

![Virtual Network Editor VMnet7](20260924083317VirtualNetworkEditorVMnet7.png)


![Virtual Network Editor VMnet6](20260924083327VirtualNetworkEditorVMnet6.png)


![Virtual Network Editor VMnet5](20260924083337VirtualNetworkEditorVMnet5.png)


![Virtual Network Editor VMnet4](20260924083402VirtualNetworkEditorVMnet4.png)


![Virtual Network Editor VMnet3](20260924083411VirtualNetworkEditorVMnet3.png)


![Virtual Network Editor VMnet2](20260924083420VirtualNetworkEditorVMnet2.png)




![Virtual Network Editor VMnet1](20260924083431VirtualNetworkEditorVMnet1.png)

## VMnet0

VMnet0 is set to **Bridged**, connecting VMs directly to the external network rather than through the host's own IP address. The **Subnet IP** and **Subnet mask** fields are greyed out, since a bridged network's addressing is determined externally, not by VMware.

![Virtual Network Editor VMnet0](20260924083440VirtualNetworkEditorVMnet0.png)

The **Bridged to** drop-down lists the host's available physical and virtual adapters (here, two Wi-Fi adapter instances and a Bluetooth PAN, alongside **Automatic**). This guide bridges to a specific physical adapter (`Intel(R) Wi-Fi 7 BE201 320MHz`) rather than leaving it on **Automatic**, so the bridge always goes out through a known, chosen interface instead of whichever one VMware's automatic bridging picks — one more way of keeping the host's own handling of this traffic as thin as possible.

Because VMnet0 is a direct bridge, any Guest OS on it is subject to whatever DHCP server (or lack of one) exists on that external network — this is the one VMnet in this guide where addressing is intentionally left outside local control.

![Virtual Network Editor VMnet0 Bridge To Selection](20260924083503VirtualNetworkEditorVMnet0BridgeToSelection.png)



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


