# Ubuntu Template on WSL2

<br><br><br>
```
╔═╦═══════════════════════════════════════════════════════╦═╗
╠═╬═══════════════════════════════════════════════════════╬═╣
║ ║ Content of this Folder was Last Updated on 2026 07 22 ║ ║
╠═╬═══════════════════════════════════════════════════════╬═╣
╚═╩═══════════════════════════════════════════════════════╩═╝
```
<br><br><br>

Having Ubuntu node(s) on WSL2 have some benefits and also disadvantages. The main one being:
- [&#x1F44D;] Access to GPU. In this document we test and use PyTorch.
- [&#x1F44E;] Much more complicated control/configuration of networking, and networking/subnetting are done from Windows host side, not from Ubuntu guest VM side (unlike Ubuntu VM on VMware where you configure the networking/subnetting on the Ubuntu guest VM itself, pretty much like the bare-metal unit).

With the above in mind, at the moment, this document describes only the "how to" install and configure Ubuntu node(s) on WSL2, to be used independently from other nodes within the WSL2 environment (i.e. avoiding the complexity of configuring virtual networking of the WSL2).

Note that this document focus only on [WSL2](https://learn.microsoft.com/en-us/windows/wsl/compare-versions).

<br><br><br>

***

## WSL2 Installation on Windows 11

Blah Blah Blah.

<br><br><br>

***

## Ubuntu Installation on WSL2

### Ensure the WSL2 virtualization framework is working

Open **Windows PowerShell** or **Command Prompt** and run: `wsl --list --verbose` or `wsl -l -v`.

If you've never used the WSL2 virtualization framework before, typically the output will be similar to below CLI dump:

```
PS C:\Users\hchandra> wsl --list --verbose
Windows Subsystem for Linux has no installed distributions.
You can resolve this by installing a distribution with the instructions below:

Use 'wsl.exe --list --online' to list available distributions
and 'wsl.exe --install <Distro>' to install.
PS C:\Users\hchandra>
```

This means the underlying WSL2 engine is ready, only the actual Ubuntu Linux OS isn't installed yet.

If you used the WSL2 virtualization framework before on your machine, you ***may*** see a Linux OS like `Ubuntu` listed.
In this case, pay attention at the **VERSION** column. It must say **2** to support GPU acceleration.

```
PS C:\Users\hchandra> wsl --list --verbose
  NAME                 STATE           VERSION
* Ubuntu-24.04-Base    Stopped         2
PS C:\Users\hchandra>
```

Furthermore, you can also get more versioning information with command: `wsl --version`.

```
PS C:\Users\hchandra> wsl --version
WSL version: 2.7.3.0
Kernel version: 6.6.114.1-1
WSLg version: 1.0.73
MSRDC version: 1.2.6676
Direct3D version: 1.611.1-81528511
DXCore version: 10.0.26100.1-240331-1435.ge-release
Windows version: 10.0.26200.8390
PS C:\Users\hchandra>
```

A help "menu" is also available with command: `wsl --help`.

```
PS C:\Users\hchandra> wsl --help
Copyright (c) Microsoft Corporation. All rights reserved.
For privacy information about this product please visit https://aka.ms/privacy.

Usage: wsl.exe [Argument] [Options...] [CommandLine]

Arguments for running Linux binaries:

    If no command line is provided, wsl.exe launches the default shell.

    --exec, -e <CommandLine>
        Execute the specified command without using the default Linux shell.

    --shell-type <standard|login|none>
        Execute the specified command with the provided shell type.

    --
        Pass the remaining command line as-is.

Options:
    --cd <Directory>
        Sets the specified directory as the current working directory.
        If ~ is used the Linux user's home path will be used. If the path begins
        with a / character, it will be interpreted as an absolute Linux path.
        Otherwise, the value must be an absolute Windows path.

    --distribution, -d <DistroName>
        Run the specified distribution.

    --distribution-id <DistroGuid>
        Run the specified distribution ID.

    --user, -u <UserName>
        Run as the specified user.

    --system
        Launches a shell for the system distribution.

Arguments for managing Windows Subsystem for Linux:

    --help
        Display usage information.

    --debug-shell
        Open a WSL2 debug shell for diagnostics purposes.

    --install [Distro] [Options...]
        Install a Windows Subsystem for Linux distribution.
        For a list of valid distributions, use 'wsl.exe --list --online'.

        Options:
            --enable-wsl1
                Enable WSL1 support.

            --fixed-vhd
                Create a fixed-size disk to store the distribution.

            --from-file <Path>
                Install a distribution from a local file.

            --legacy
                Use the legacy distribution manifest.

            --location <Location>
                Set the install path for the distribution.

            --name <Name>
                Set the name of the distribution.

            --no-distribution
                Only install the required optional components, does not install a distribution.

            --no-launch, -n
                Do not launch the distribution after install.

            --version <Version>
                Specifies the version to use for the new distribution.

            --vhd-size <MemoryString>
                Specifies the size of the disk to store the distribution.

            --web-download
                Download the distribution from the internet instead of the Microsoft Store.

    --manage <Distro> <Options...>
        Changes distro specific options.

        Options:
            --move <Location>
                Move the distribution to a new location.

            --set-sparse, -s <true|false>
                Set the VHD of distro to be sparse, allowing disk space to be automatically reclaimed.

            --set-default-user <Username>
                Set the default user of the distribution.

            --resize <MemoryString>
                Resize the disk of the distribution to the specified size.

    --mount <Disk>
        Attaches and mounts a physical or virtual disk in all WSL 2 distributions.

        Options:
            --vhd
                Specifies that <Disk> refers to a virtual hard disk.

            --bare
                Attach the disk to WSL2, but don't mount it.

            --name <Name>
                Mount the disk using a custom name for the mountpoint.

            --type <Type>
                Filesystem to use when mounting a disk, if not specified defaults to ext4.

            --options <Options>
                Additional mount options.

            --partition <Index>
                Index of the partition to mount, if not specified defaults to the whole disk.

    --set-default-version <Version>
        Changes the default install version for new distributions.

    --shutdown
        Immediately terminates all running distributions and the WSL 2
        lightweight utility virtual machine.

        Options:
            --force
                Terminate the WSL 2 virtual machine even if an operation is in progress. Can cause data loss.

    --status
        Show the status of Windows Subsystem for Linux.

    --unmount [Disk]
        Unmounts and detaches a disk from all WSL2 distributions.
        Unmounts and detaches all disks if called without argument.

    --uninstall
        Uninstalls the Windows Subsystem for Linux package from this machine.

    --update
        Update the Windows Subsystem for Linux package.

        Options:
            --pre-release
                Download a pre-release version if available.

    --version, -v
        Display version information.

Arguments for managing distributions in Windows Subsystem for Linux:

    --export <Distro> <FileName> [Options]
        Exports the distribution to a tar file.
        The filename can be - for stdout.

        Options:
            --format <Format>
                Specifies the export format. Supported values: tar, tar.gz, tar.xz, vhd.

    --import <Distro> <InstallLocation> <FileName> [Options]
        Imports the specified tar file as a new distribution.
        The filename can be - for stdin.

        Options:
            --version <Version>
                Specifies the version to use for the new distribution.

            --vhd
                Specifies that the provided file is a .vhd or .vhdx file, not a tar file.
                This operation makes a copy of the VHD file at the specified install location.

    --import-in-place <Distro> <FileName>
        Imports the specified VHD file as a new distribution.
        This virtual hard disk must be formatted with the ext4 filesystem type.

    --list, -l [Options]
        Lists distributions.

        Options:
            --all
                List all distributions, including distributions that are
                currently being installed or uninstalled.

            --running
                List only distributions that are currently running.

            --quiet, -q
                Only show distribution names.

            --verbose, -v
                Show detailed information about all distributions.

            --online, -o
                Displays a list of available distributions for install with 'wsl.exe --install'.

    --set-default, -s <Distro>
        Sets the distribution as the default.

    --set-version <Distro> <Version>
        Changes the version of the specified distribution.

    --terminate, -t <Distro>
        Terminates the specified distribution.

    --unregister <Distro>
        Unregisters the distribution and deletes the root filesystem.
PS C:\Users\hchandra>
```

<br><br><br>

***

### Install Ubuntu 24.04 LTS on WSL2

The list of supported Linux OS on WSL2 can be obtained with command: `wsl --list --online`.

```
PS C:\windows\system32> wsl --list --online
The following is a list of valid distributions that can be installed.
Install using 'wsl.exe --install <Distro>'.

NAME                            FRIENDLY NAME
Ubuntu                          Ubuntu
Ubuntu-26.04                    Ubuntu 26.04 LTS
Ubuntu-24.04                    Ubuntu 24.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
openSUSE-Tumbleweed             openSUSE Tumbleweed
openSUSE-Leap-16.0              openSUSE Leap 16.0
SUSE-Linux-Enterprise-15-SP7    SUSE Linux Enterprise 15 SP7
SUSE-Linux-Enterprise-16.0      SUSE Linux Enterprise 16.0
kali-linux                      Kali Linux Rolling
Debian                          Debian GNU/Linux
AlmaLinux-8                     AlmaLinux OS 8
AlmaLinux-9                     AlmaLinux OS 9
AlmaLinux-Kitten-10             AlmaLinux OS Kitten 10
AlmaLinux-10                    AlmaLinux OS 10
archlinux                       Arch Linux
FedoraLinux-44                  Fedora Linux 44
FedoraLinux-43                  Fedora Linux 43
eLxr                            eLxr 12.12.0.0 GNU/Linux
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_10                Oracle Linux 8.10
OracleLinux_9_5                 Oracle Linux 9.5
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
PS C:\windows\system32>
```

From the list above, we picked ***Ubuntu 24.04 LTS*** as at the time of writing *Ubuntu 26.04 LTS* is still very new, and we need to use a latest stable Ubuntu OS (i.e. having no compatibility issues with the GPU drivers).

Once you decide which Linux OS, you can install it with the following command:
- [ ] `wsl --install --distribution Ubuntu-24.04 --name Ubuntu-24.04-Base --no-launch`

  or

- [ ] `wsl --install -d Ubuntu-24.04 --name Ubuntu-24.04-Base -n`

  Notes on the command's options:

- [ ] the `--distribution` or `-d` option requires a string value from the `NAME` column of the list above.
- [ ] the `--name` option requires a string value of any continuous-string name you'd like to name the downloaded/installed Linux OS at your local environment.

```
PS C:\Users\hchandra> wsl --install --distribution Ubuntu-24.04 --name Ubuntu-24.04-Base --no-launch
Downloading: Ubuntu 24.04 LTS
Installing: Ubuntu 24.04 LTS
Distribution successfully installed. It can be launched via 'wsl.exe -d Ubuntu-24.04-Base'
PS C:\Users\hchandra>
```

The command will download and install the targeted Linux OS to the standard/default folder which typically be `%USERPROFILE%\AppData\Local\wsl\{01234567-89ab-cdef-0123-456789abcdef}\` folder.
- [ ] The `%USERPROFILE%` part is your *Home Path* within Windows 11 OS environment.
- [ ] The `{01234567-89ab-cdef-0123-456789abcdef}` is a 32-digits of hexadecimal-number formatted as per shown.

And as we issue the WSL command with `--no-launch` option, the WSL2 will do only download and install the targeted Linux OS, and NOT launch it.

Once you have downloaded and installed a Linux OS onto the WSL, you can list your installed Linux OS(es) with command: `wsl --list --verbose` or `wsl -l -v`.

```
PS C:\Users\hchandra> wsl --list --verbose
  NAME                 STATE           VERSION
* Ubuntu-24.04-Base    Stopped         2
PS C:\Users\hchandra>
```

To launch (or to run) the installed Linux OS, issue the command: `wsl --distribution Ubuntu-24.04-Base`.
Note that now the `--distribution` option refers to the `NAME` you have given to the installed Linux OS, at/for your local environment (i.e. the `NAME` column on the installed Linux OS list above).

If you launch/run your installed Ubuntu OS the first time, you will be asked to create default account and the corresponding password.

```
PS C:\Users\hchandra> wsl --distribution Ubuntu-24.04-Base
Provisioning the new WSL instance Ubuntu-24.04-Base
This might take a while...
Create a default Unix user account: ubuntu
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

Note that the CLI Prompt after the command does NOT return to Windows 11's *Windows PowerShell* or *Command Prompt*, but the prompt now is held by the Ubuntu Linux OS prompt.

For the subsequent launch/run of the installed Ubuntu OS, you will be directly prompted with Ubuntu Linux OS prompt.

```
PS C:\Users\hchandra> wsl --distribution Ubuntu-24.04-Base
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

Notes on Ubuntu Linux OS Prompt:
- [ ] `F1NB7G4` part will be your Windows 11 Device/Host Name.
- [ ] Your `%USERPROFILE%` or *Home Path* is mounted as `/mnt/%HOMEDRIVE%/%HOMEPATH%`, and that will be the landing folder every time you launch/run the installed Ubuntu Linux OS.













```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ nvidia-smi
Wed May 27 09:17:47 2026
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 590.51                 Driver Version: 591.64         CUDA Version: 13.1     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA RTX PRO 1000 Blac...    On  |   00000000:01:00.0 Off |                  N/A |
| N/A   40C    P4             11W /   43W |       0MiB /   8151MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```


```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ top
top - 09:20:47 up 5 min,  1 user,  load average: 0.01, 0.02, 0.00
Tasks:  22 total,   1 running,  21 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  31822.1 total,  30850.9 free,    697.1 used,    583.0 buff/cache
MiB Swap:   8192.0 total,   8192.0 free,      0.0 used.  31125.0 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0   21960  13208   9564 S   0.0   0.0   0:00.54 systemd
      2 root      20   0    3172   2212   2072 S   0.0   0.0   0:00.01 init-systemd(Ub
      7 root      20   0    3204   2140   2008 S   0.0   0.0   0:00.01 init
     59 root      19  -1   50360  16096  15024 S   0.0   0.0   0:00.08 systemd-journal
    119 root      20   0   25396   6832   5028 S   0.0   0.0   0:00.09 systemd-udevd
    129 systemd+  20   0   21460  12876  10624 S   0.0   0.0   0:00.04 systemd-resolve
    130 systemd+  20   0   91028   8080   7112 S   0.0   0.0   0:00.02 systemd-timesyn
    199 root      20   0    4236   2688   2432 S   0.0   0.0   0:00.00 cron
    200 message+  20   0    9628   5288   4592 S   0.0   0.0   0:00.04 dbus-daemon
    208 root      20   0   17968   8724   7672 S   0.0   0.0   0:00.05 systemd-logind
    210 root      20   0 1756620  13684  11164 S   0.0   0.0   0:00.06 wsl-pro-service
    221 syslog    20   0  222508   5596   4520 S   0.0   0.0   0:00.04 rsyslogd
    232 root      20   0    3116   1924   1788 S   0.0   0.0   0:00.00 agetty
    239 root      20   0  107024  22756  13300 S   0.0   0.1   0:00.07 unattended-upgr
    412 root      20   0    3180   1116    980 S   0.0   0.0   0:00.00 SessionLeader
    414 root      20   0    3196   1252   1108 S   0.0   0.0   0:00.01 Relay(417)
    417 ubuntu    20   0    6072   5296   3572 S   0.0   0.0   0:00.02 bash
    515 root      20   0    6824   4668   3880 S   0.0   0.0   0:00.00 login
    556 ubuntu    20   0   20312  11400   9272 S   0.0   0.0   0:00.07 systemd
    557 ubuntu    20   0   21152   3556   1832 S   0.0   0.0   0:00.00 (sd-pam)
    581 ubuntu    20   0    6056   5244   3592 S   0.0   0.0   0:00.00 bash
    693 ubuntu    20   0    9328   5608   3384 R   0.0   0.0   0:00.01 top
```














Blah Blah Blah.

<br><br><br>

***

## Configure the Ubuntu/Python to Access GPU

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


