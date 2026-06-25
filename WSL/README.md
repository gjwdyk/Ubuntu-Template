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

If for some reason, you did NOT run `wsl --list --online` first, you ***may*** run into the following issue.

```
PS C:\Users\hchandra> wsl --install --distribution Ubuntu-24.04 --name Ubuntu-24.04-Base --no-launch
Failed to fetch the distribution list from 'https://raw.githubusercontent.com/microsoft/WSL/master/distributions/DistributionInfo.json'. The server name or address could not be resolved
Error code: Wsl/InstallDistro/WININET_E_NAME_NOT_RESOLVED
PS C:\Users\hchandra>
```

To fix, you just need to run `wsl --list --online` command first, before executing the `wsl --install --distribution Ubuntu-24.04 --name Ubuntu-24.04-Base --no-launch` command.

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

By default, without additional module installation, the *Ubuntu 24.04 LTS* already includes the `nvidia-smi` (NVIDIA System Management Interface), a command-line utility used to monitor and manage NVIDIA GPU. It verifies that your Windows-based NVIDIA graphics driver and GPU are successfully recognized and passed through to your Linux environment.

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

You can check how much CPU and RAM the Ubuntu Linux OS on the WSL2 has claimed from your host by running `top` command on the Ubuntu CLI Prompt. This will show you a live monitor of the virtualized CPU and Memory. You can cross-reference this with what you saw in the **WSL Settings** application (on the host Windows 11) to ensure the Ubuntu Linux OS VM is breathing properly.

![Windows Start Menu - WSL Settings](01WindowsStartMenuWSLSettings.png)

![WSL Settings - Memory and Processor](02WSLSettingsMemoryAndProcessor.png)

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

<br><br><br>

***

## Base Configure the Ubuntu

### sudoers

As per default Ubuntu installation, a `sudo` command will require you to input password.
For some people, this is annoying, a bit distracting and unnecessary for documentation.

To remove this requirement, execute the following command: `echo -e "\n\n\nroot     ALL=(ALL:ALL) NOPASSWD:ALL\nubuntu   ALL=(ALL:ALL) NOPASSWD:ALL\n\n\n" | sudo tee -a /etc/sudoers`.

```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ echo -e "\n\n\nroot     ALL=(ALL:ALL) NOPASSWD:ALL\nubuntu   ALL=(ALL:ALL) NOPASSWD:ALL\n\n\n" | sudo tee -a /etc/sudoers



root     ALL=(ALL:ALL) NOPASSWD:ALL
ubuntu   ALL=(ALL:ALL) NOPASSWD:ALL



ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

The command adds the following two lines to the end of `/etc/sudoers` file, with some white-spaces/empty-lines before and after the added two lines.
- [ ] `root     ALL=(ALL:ALL) NOPASSWD:ALL`
- [ ] `ubuntu   ALL=(ALL:ALL) NOPASSWD:ALL`

You can check whether the lines had been successfully added, with `sudo cat /etc/sudoers` command.

```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo cat /etc/sudoers
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# This fixes CVE-2005-4890 and possibly breaks some versions of kdesu
# (#1011624, https://bugs.kde.org/show_bug.cgi?id=452532)
Defaults        use_pty

# This preserves proxy settings from user environments of root
# equivalent users (group sudo)
#Defaults:%sudo env_keep += "http_proxy https_proxy ftp_proxy all_proxy no_proxy"

# This allows running arbitrary commands, but so does ALL, and it means
# different sudoers have their choice of editor respected.
#Defaults:%sudo env_keep += "EDITOR"

# Completely harmless preservation of a user preference.
#Defaults:%sudo env_keep += "GREP_COLOR"

# While you shouldn't normally run git as root, you need to with etckeeper
#Defaults:%sudo env_keep += "GIT_AUTHOR_* GIT_COMMITTER_*"

# Per-user preferences; root won't have sensible values for them.
#Defaults:%sudo env_keep += "EMAIL DEBEMAIL DEBFULLNAME"

# "sudo scp" or "sudo rsync" should be able to use your SSH agent.
#Defaults:%sudo env_keep += "SSH_AGENT_PID SSH_AUTH_SOCK"

# Ditto for GPG agent
#Defaults:%sudo env_keep += "GPG_AGENT_INFO"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "@include" directives:

@includedir /etc/sudoers.d



root     ALL=(ALL:ALL) NOPASSWD:ALL
ubuntu   ALL=(ALL:ALL) NOPASSWD:ALL



ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

You can also check whether functionality wise the `/etc/sudoers` file is OK (i.e. parse-able) and whether functionality wise things are still working fine. Some of the test commands are:

- [ ] `sudo visudo -c`. Checks the syntax of the sudoers configuration file. It analyzes `/etc/sudoers` (and any files in `/etc/sudoers.d/`) for typos or formatting errors. If everything is correct, it returns parsed OK. If there's an error, it warns you before you accidentally lock yourself out of administrative privileges.

- [ ] `sudo -k`. Kills/invalidates your cached credentials (your sudo "ticket"). By default, once you type your password for sudo, Linux remembers it for a short grace period (usually 15 minutes) so you don't have to keep retyping it. Running sudo -k immediately revokes this privilege, meaning the very next sudo command will strictly require the password again. It's great for security when walking away from your machine. Do this only when `sudo visudo -c` returns all OK.

- [ ] `sudo whoami`. Outputs the username that the command is currently running as, which will always be `root` (because the prefix `sudo` means you're asking superuser privileges, which is `root` user). It’s a classic sanity check to confirm that `sudo` is working properly and that you have effectively assumed `root` execution power.

- [ ] `sudo -l -U root`. Lists the sudo privileges allowed for the user root. The -l flag lists privileges, and -U specifies the target user. Because root is the ultimate superuser, running this will typically show that root can run (ALL : ALL) ALL - meaning they can run any command, anywhere, as any user or group.

- [ ] `sudo -l -U ubuntu`. Lists the sudo privileges allowed for the user ubuntu. This allows an administrator (or the ubuntu user themselves) to check exactly what permissions the ubuntu account has. It will print out the specific commands ubuntu user is authorized to run via sudo, or tell you if they aren't allowed to use sudo at all.

```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo visudo -c
/etc/sudoers: parsed OK
/etc/sudoers.d/README: parsed OK
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo -k
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo whoami
root
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo -l -U root
Matching Defaults entries for root on F1NB7G4:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty
User root may run the following commands on F1NB7G4:
    (ALL : ALL) ALL
    (ALL : ALL) NOPASSWD: ALL
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo -l -U ubuntu
Matching Defaults entries for ubuntu on F1NB7G4:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty
User ubuntu may run the following commands on F1NB7G4:
    (ALL : ALL) ALL
    (ALL : ALL) NOPASSWD: ALL
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

<br><br><br>

***

### Advanced Package Tool (apt) Sources

Sometimes, some Ubuntu sources may perform badly.
You can change where your Ubuntu instance obtains its sources from, by changing the `/etc/apt/sources.list` which has been moved to `/etc/apt/sources.list.d/ubuntu.sources` file.

```
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo cat /etc/apt/sources.list
# Ubuntu sources have moved to the /etc/apt/sources.list.d/ubuntu.sources
# file, which uses the deb822 format. Use deb822-formatted .sources files
# to manage package sources in the /etc/apt/sources.list.d/ directory.
# See the sources.list(5) manual page for details.
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$ sudo cat /etc/apt/sources.list.d/ubuntu.sources
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.

## Ubuntu distribution repository
##
## The following settings can be adjusted to configure which packages to use from Ubuntu.
## Mirror your choices (except for URIs and Suites) in the security section below to
## ensure timely security updates.
##
## Types: Append deb-src to enable the fetching of source package.
## URIs: A URL to the repository (you may add multiple URLs)
## Suites: The following additional suites can be configured
##   <name>-updates   - Major bug fix updates produced after the final release of the
##                      distribution.
##   <name>-backports - software from this repository may not have been tested as
##                      extensively as that contained in the main release, although it includes
##                      newer versions of some applications which may provide useful features.
##                      Also, please note that software in backports WILL NOT receive any review
##                      or updates from the Ubuntu security team.
## Components: Aside from main, the following components can be added to the list
##   restricted  - Software that may not be under a free license, or protected by patents.
##   universe    - Community maintained packages. Software in this repository receives maintenance
##                 from volunteers in the Ubuntu community, or a 10 year security maintenance
##                 commitment from Canonical when an Ubuntu Pro subscription is attached.
##   multiverse  - Community maintained of restricted. Software from this repository is
##                 ENTIRELY UNSUPPORTED by the Ubuntu team, and may not be under a free
##                 licence. Please satisfy yourself as to your rights to use the software.
##                 Also, please note that software in multiverse WILL NOT receive any
##                 review or updates from the Ubuntu security team.
##
## See the sources.list(5) manual page for further settings.
Types: deb
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

## Ubuntu security updates. Aside from URIs and Suites,
## this should mirror your choices in the previous section.
Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
ubuntu@F1NB7G4:/mnt/c/Users/hchandra$
```

As you can see, basically there are only two sections on the `/etc/apt/sources.list.d/ubuntu.sources` file.

- [ ] For general update sources.

  ```
  Types: deb
  URIs: http://archive.ubuntu.com/ubuntu/
  Suites: noble noble-updates noble-backports
  Components: main universe restricted multiverse
  Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
  ```

- [ ] For security update sources.

  ```
  Types: deb
  URIs: http://security.ubuntu.com/ubuntu/
  Suites: noble-security
  Components: main universe restricted multiverse
  Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
  ```

What you need to update/modify on the two sections are only the `URIs` field.
To find out what are the other alternative values for the `URIs` field, you can refer to [Official Archive Mirrors for Ubuntu](https://launchpad.net/ubuntu/+archivemirrors).
Let's take example one of the closest mirrors with one of the largest bandwidth *Taiwan Digital Streaming Co.* which has two versions:
- [ ] *Taiwan Digital Streaming Co. (archive)*. Use this if your instance runs on standard x86_64 / AMD64 / Intel 64-bit hardware (which is true for 95% of standard PCs, servers, and standard cloud VMs).
- [ ] *Taiwan Digital Streaming Co. (ports)*. This site is for Ubuntu Ports. Use this only if your instance runs on alternative architectures like ARM (e.g., Raspberry Pi, Apple Silicon VMs, AWS Graviton instances), POWER, or RISC-V.

![Ubuntu Mirror - Taiwan Digital Streaming](03UbuntuMirrorTaiwanDigitalStreaming.png)

For each site, on the right side following the site name, there are protocols to connect to the site: `https`, `http` and `rsync`.
Generally for standard apt configuration, you'd consider only `https` and `http`, with preferences/recommendations towards `https`.
- [ ] If you right-click the `https` protocol for *Taiwan Digital Streaming Co. (archive)*, select *Copy Link Address* and paste the copied value, you will get "https://mirror.twds.com.tw/ubuntu/".
- [ ] If you right-click the `https` protocol for *Taiwan Digital Streaming Co. (ports)*, select *Copy Link Address* and paste the copied value, you will get "https://mirror.twds.com.tw/ubuntu-ports/". The path part "/ubuntu-ports/" indicates that the site is for Ubuntu Ports (alternative architectures).

With the two aspects above, we have narrow down our choices to only one link: "https://mirror.twds.com.tw/ubuntu/", since we are not using alternative architectures and we don't want to use unsecured protocol.

How about the `URIs` field for the security update sources section?
The sites on the [Official Archive Mirrors for Ubuntu](https://launchpad.net/ubuntu/+archivemirrors) do not have specific section for security update sources.
You can use the same link as for the general update sources section, i.e. "https://mirror.twds.com.tw/ubuntu/" if we follow our example above.













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


