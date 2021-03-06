---
title: Migrating a VM from VMware Workstation to Oracle VirtualBox
author: Karim Elatov
layout: post
permalink: /2013/04/migrating-a-vm-from-vmware-workstation-to-oracle-virtualbox/
categories: ['os', 'vmware']
tags: ['fedora', 'vmware_workstation', 'linux', 'ovftool', 'vmdk', 'virtualbox']
---

Recently I had migrated laptops and I wanted to move my VMware workstation VMs to VirtualBox. All of my VMs were in the **.vmware** folder so I just copied that to my laptop. Here are the contents of that folder:

    [elatov@klaptop ~]$ ls -1 .vmware/
    ace.dat
    dndlogs
    favorites.vmls
    inventory.vmls
    playerUploadedData.log
    preferences
    preferences-private
    shortcuts
    UCSPM
    unity-helper.conf
    view-preferences
    Windows XP Professional
    workstationUploadedData.log


There are only two VMs: UCSPM (the UCS Manager Emulator) and Windows XP Professional (My Windows XP machine). I am just going to convert my Windows XP machine. Inside that folder, I saw the following:

    [elatov@klaptop ~]$ ls -1 .vmware/Windows\ XP\ Professional/
    caches
    vmware-0.log
    vmware-1.log
    vmware-2.log
    vmware.log
    Windows XP Professional.nvram
    Windows XP Professional-s001.vmdk
    Windows XP Professional-s002.vmdk
    Windows XP Professional-s003.vmdk
    Windows XP Professional-s004.vmdk
    Windows XP Professional-s005.vmdk
    Windows XP Professional-s006.vmdk
    Windows XP Professional-s007.vmdk
    Windows XP Professional-s008.vmdk
    Windows XP Professional-s009.vmdk
    Windows XP Professional-s010.vmdk
    Windows XP Professional-s011.vmdk
    Windows XP Professional.vmdk
    Windows XP Professional.vmsd
    Windows XP Professional.vmx
    Windows XP Professional.vmxf


Pretty standard stuff, but I realized that when I created that VM I used the 2GB Split Sparse VMDK format. I don't even know why I did that. I was probably thinking that if I was backing up to a FAT32 partition then this might help out. But who uses FAT partitions anymore? :)

So the first thing I wanted to do, was to convert the 2GB Split Spare VMDKs into a single monolithic VMDK (also reffered to as Monolithic Sparse VMDK). You can check out all the VMDK formats in the [Virtual Disk Programming Guide Virtual Disk Development Kit (VDDK) 5.1](http://pubs.vmware.com/vsphere-51/topic/com.vmware.ICbase/PDF/vddk51_programming.pdf). Here is a table from that pdf:

![VMDK Types Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/VMDK_Types.png)

### Convert Split Sparse VMDK to a Monolithic Sparse VMDK

Looking over a couple of sites, it looks like **vmware-vdiskmanager** is the way to go:

*   [VMWARE: Converting Multiple VMDK files into one](http://www.eknori.de/2012-11-23/vmware-converting-multiple-vmdk-files-into-one/)
*   [Converting Multiple VMDK (Virtual Machine Disk) files into one](http://technonstop.com/convert-multiple-vmdks-to-one)

The **vmware-vdiskmanager** is packaged with the "Virtual Disk Development Kit" which you can download from [here](http://www.vmware.com/support/developer/vddk/). Here is screenshot of the available downloads after I logged into the VMware portal:

![vddk download Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/vddk_download.png)

After I downloaded the archive, I had the following file:

    [elatov@klaptop downloads]$ ls *vix*
    VMware-vix-disklib-5.1.0-774844.x86_64.tar.gz


So I went ahead and extracted the contents:

    [elatov@klaptop downloads]$ tar xvzf VMware-vix-disklib-5.1.0-774844.x86_64.tar.gz


Now let's go ahead and install the package:

    [elatov@klaptop downloads]$ cd vmware-vix-disklib-distrib
    [elatov@klaptop vmware-vix-disklib-distrib]$ sudo ./vmware-install.pl
    Creating a new VMware VIX DiskLib API installer database using the tar4 format.

    Installing VMware VIX DiskLib API.

    You must read and accept the VMware VIX DiskLib API End User License Agreement to continue.
    Press enter to display it.
    ...
    ...
    What prefix do you want to use to install VMware VIX DiskLib API?

    The prefix is the root directory where the other folders such as man, bin, doc, lib, etc. will be placed. [/usr]

    The installation of VMware VIX DiskLib API 5.1.0 build-774844 for Linux completed successfully. You can decide to remove this software from your system at any time by invoking the following command: "/usr/bin/vmware-uninstall-vix-disklib.pl".

    Enjoy,

    ## --the VMware team


After the install, I tried to check the consistency of my files and I ran the following:

    [elatov@klaptop Windows XP Professional]$ vmware-vdiskmanager -R Windows\ XP\ Professional.vmdk
    VixDiskLib: Failed to load libvixDiskLibVim.so : Error = libvixDiskLibVim.so: cannot open shared object file:
    No such file or directory.
    No errors were found on the virtual disk, 'Windows XP Professional.vmdk'.


I then re-read the "[Virtual Disk API Programming Guide](http://pubs.vmware.com/vsphere-50/topic/com.vmware.ICbase/PDF/vddk_prog_guide.pdf)", and saw the following:

> **To Install the package on Linux**
>
> 1.  On the Download page, choose the binary tar.gz for either 32‐bit Linux or 64‐bit Linux.
> 2.  Unpack the archive, which creates the vmware-vix-disklib-distrib subdirectory.
>
>         tar xvzf VMware-vix-disklib.*.tar.gz
>
>
> 3.  Change to that directory and run the installation script as root:
>
>         cd vmware-vix-disklib-distrib
>         sudo ./vmware-install.pl
>
>
> 4.  Read the license terms and type **yes** to accept them.
>     Software components install in /usr unless you specify otherwise.
>
>     You might need to edit your **LD_LIBRARY_PATH** environment to include the library installation path, **/usr/lib/vmware-vix-disklib/lib32** (or lib64) for instance. Alternatively, you can add the library location to the list in **/etc/ld.so.conf** and run **ldconfig** as root.

So I went ahead and created a file **/etc/ld.so.conf.d/vmware-vix-64.conf** with the following contents:

    [elatov@klaptop ~]$ cat /etc/ld.so.conf.d/vmware-vix-64.conf
    /usr/lib/vmware-vix-disklib/lib64


I then ran **ldconfig** to apply the changes:

    [elatov@klaptop ~]$ sudo ldconfig -v | grep vix
    /usr/lib/vmware-vix-disklib/lib64:
       libvixDiskLib.so.5 -> libvixDiskLib.so.5.1.0
       libvixDiskLibVim.so.5 -> libvixDiskLibVim.so.5.1.0
       libvixMntapi.so.1 -> libvixMntapi.so.1.1.0


and I can see the new libraries are now included. If you don't want to mess with the system's libraries you can always just set the **LB_LIBRARY_PATH** variable. Another person had issues by changing the **ld.so.conf** files and ended going that route. The issue and steps around it are described in [this](http://communities.vmware.com/thread/303517) VMware's Communities page.

Now re-running my consistency check, I saw the following:

    [elatov@klaptop Windows XP Professional]$ vmware-vdiskmanager -R Windows\ XP\ Professional.vmdk
    No errors were found on the virtual disk, 'Windows XP Professional.vmdk'.


That looks good. Now let's go ahead and convert it. Checking out the `vmware-vdiskmanager --help` output, I saw the following:

     -r <source -disk/>     : convert the specified disk; need to specify
                            destination disk-type.  For local destination disks
                            the disk type must be specified.


And here are the available disk types:

    Disk types:
          0                   : single growable virtual disk
          1                   : growable virtual disk split in 2GB files
          2                   : preallocated virtual disk
          3                   : preallocated virtual disk split in 2GB files
          4                   : preallocated ESX-type virtual disk
          5                   : compressed disk optimized for streaming
          6                   : thin provisioned virtual disk - ESX 3.x and above


So the command for the conversion, looked like this:

    [elatov@klaptop Windows XP Professional]$ vmware-vdiskmanager -r Windows\ XP\ Professional.vmdk -t 0 Windows_XP.vmdk
    Creating disk 'Windows_XP.vmdk'
    Convert: 4% done.


and that went on for a little bit. Notice that the destination VMDK is called *Windows_XP.vmdk*. After the conversion is finished we will see this:

    Convert: 100% done.
    Virtual disk conversion successful.


Checking out the final file, I saw the following:

    [elatov@klaptop Windows XP Professional]$ ls -lh Windows_XP.vmdk
    -rw------- 1 elatov elatov 18G Mar 22 15:10 Windows_XP.vmdk


and then checking the old files:

    [elatov@klaptop Windows XP Professional]$ du -hsc *-s0*
    2.0G    Windows XP Professional-s001.vmdk
    2.0G    Windows XP Professional-s002.vmdk
    2.0G    Windows XP Professional-s003.vmdk
    2.0G    Windows XP Professional-s004.vmdk
    2.0G    Windows XP Professional-s005.vmdk
    5.1M    Windows XP Professional-s006.vmdk
    2.0G    Windows XP Professional-s007.vmdk
    2.0G    Windows XP Professional-s008.vmdk
    2.0G    Windows XP Professional-s009.vmdk
    1.1G    Windows XP Professional-s010.vmdk
    1022M   Windows XP Professional-s011.vmdk
    18G total


The size matched up, which is perfect.

From this point on, I could've probably just added the vmdk to VirtualBox, but I wanted to preserve all the Memory and CPU settings. To save those settings we can package the VMX and VMDK into an OVA template and then import it into VirtualBox.

## Create an OVA Template from a VMware Workstation VM

The VMX file is where all the configurations are stored, and my VMX file was still pointing at the old Split Sparse VMDK:

    [elatov@klaptop Windows XP Professional]$ grep vmdk *.vmx
    scsi0:0.fileName = "Windows XP Professional.vmdk"


I edited the VMX file and pointed to the newly converted VMDK. After I was done, I had the following:

    [elatov@klaptop Windows XP Professional]$ grep vmdk Windows\ XP\ Professional.vmx
    scsi0:0.fileName = "Windows_XP.vmdk"


Now that my VM is ready to be imported into an OVA, let's check out the [OVF Tool User Guide](http://www.vmware.com/support/developer/ovf/ovf301/ovftool-301-userguide.pdf). From the guide, here is the install process:

> **To install the VMware OVF Tool**
>
> 1.  Download VMware OVF Tool as an installer or an archive (zipped/compressed) file:
> 2.  Install using the method for your operating system:

[Here](http://www.vmware.com/support/developer/ovf/) is the download page for the *ovftool* application. After I logged in, here were the downloads that I saw:

![Download Ovftool Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/Download_Ovftool.png)

When I was done with the download, I had the following file:

    [elatov@klaptop downloads]$ ls *ovf*
    VMware-ovftool-3.0.1-801290-lin.x86_64.txt


Now let's install the ovf tool:

    [elatov@klaptop downloads]$ chmod +x VMware-ovftool-3.0.1-801290-lin.x86_64.txt
    [elatov@klaptop downloads]$ sudo ./VMware-ovftool-3.0.1-801290-lin.x86_64.txt
    Extracting VMware Installer.


At this point a GUI installer will come up, like so:

![OVF Installer Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/OVF_Installer.png)

Just hit "Install" and the process with go through pretty quickly. After it's finished you will see the following:

![OVF Installer Finished Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/OVF_Installer_Finished.png)

In the [OVF User Guide](http://www.vmware.com/support/developer/ovf/ovf301/ovftool-301-userguide.pdf) there are good examples of how to use *ovftool*. Here is what I ran to create my OVA template:

    [elatov@klaptop Windows XP Professional]$ ovftool Windows\ XP\ Professional.vmx Win_XP.ova
    Opening VMX source: Windows XP Professional.vmx
    Opening OVA target: Win_XP.ova
    Writing OVA package: Win_XP.ova
    Progress: 2%


It started to create the template. The extension is actually important, if I specify an "ovf" extension then it will create an OVF file along with VMDKs. Where the '.ova' extension encompasses both. From the guide:

> **Converting a VMX to an OVF**
>
> To convert a virtual machine in VMware runtime format (.vmx) to an OVF package, use the following syntax:
>
>     ovftool /vms/my_vm.vmx /ovfs/my_vapp.ovf
>
>
> The result is located in /ovfs/my_vapp.[ovf|vmdk]
>
> **Converting a VMX to an OVA**
>
> To convert a VMX to an OVA file, use the following syntax:
>
>     ovftool vmxs/Nostalgia.vmx ovfs/Nostalgia.ova
>

Also from the same guide:

> Supports both import and generation of OVA packages (OVA is part of the OVF standard, and contains all the files of a virtual machine or vApp in a single file.)

Here is also a table comparison:

![OVF VS OVA Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/OVF_VS_OVA.png)

After the process was done, I saw the following:

    [elatov@klaptop Windows XP Professional]$ ovftool Windows\ XP\ Professional.vmx Win_XP.ova
    Opening VMX source: Windows XP Professional.vmx
    Opening OVA target: Win_XP.ova
    Writing OVA package: Win_XP.ova
    Transfer Completed
    Completed successfully


Now checking for the OVA file:

    [elatov@klaptop Windows XP Professional]$ ls -lh Win_XP.ova
    -rw------- 1 elatov elatov 11G Mar 22 13:54 Win_XP.ova


To get information about the OVA file you can also use *ovftool* in "probe mode":

    [elatov@klaptop Windows XP Professional]$ ovftool Win_XP.ova
    OVF version:   1.0
    VirtualApp:    false
    Name:          Windows XP Professional

    Download Size:    10.66 GB

    Deployment Sizes:
      Flat disks:     18.00 GB
      Sparse disks:   17.80 GB

    Networks:
      Name:        nat
      Description: The nat network

    Virtual Hardware:
      Family:       vmx-09
      Disk Types:   SCSI-buslogic


As a last note, an OVA file is just a tar archive, and you can check the contents of the OVA file like so:

    [elatov@klaptop Windows XP Professional]$ tar tvf Win_XP.ova
    -rw-r--r-- someone/64 6284 2013-03-22 13:42 Win_XP.ovf
    -rw-r--r-- someone/64 125 2013-03-22 13:42 Win_XP.mf
    -rw-r--r-- someone/64 11445539328 2013-03-22 13:54 Win_XP-disk1.vmdk


All of the above looks good, now let's import the OVA template into VirtualBox.

### Import an OVA Template into VirtualBox

We can use the **VBoxManage** to import an OVA Template. From the command line:

    [elatov@klaptop ~]$  VBoxManage import
    Usage:

    VBoxManage import           <ovf /ova>
                                [--dry-run|-n]
                                [--options keepallmacs|keepnatmacs]
                                [more options]
                                (run with -n to have options displayed
                                 for a particular OVF)


So let's use -n to see all the options:

    [elatov@klaptop ~]$ VBoxManage import -n .vmware/Windows\ XP\ Professional/Win_XP.ova 0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
    Interpreting /home/elatov/.vmware/Windows\ XP\ Professional/Win_XP.ova...
    OK.
    Disks:  vmdisk1 18  19109117952 http://www.vmware.com/interfaces/specifications/vmdk.html#streamOptimized   Win_XP-disk1.vmdk   11445539328 -1
    Virtual system 0:
     0: Suggested OS type: "WindowsXP"
        (change with "--vsys 0 --ostype <type>"; use "list ostypes" to list all possible values)
     1: Suggested VM name "vm"
        (change with "--vsys 0 --vmname <name>")
     2: Number of CPUs: 1
        (change with "--vsys 0 --cpus <n>")
     3: Guest memory: 512 MB
        (change with "--vsys 0 --memory <mb>")
     4: USB controller
        (disable with "--vsys 0 --unit 4 --ignore")
     5: Network adapter: orig nat, config 2, extra type=nat
     6: CD-ROM
        (disable with "--vsys 0 --unit 6 --ignore")
     7: SCSI controller, type BusLogic
        (change with "--vsys 0 --unit 7 --scsitype {BusLogic|LsiLogic}";
        disable with "--vsys 0 --unit 7 --ignore")
     8: IDE controller, type PIIX4
        (disable with "--vsys 0 --unit 8 --ignore")
     9: Hard disk image: source image=Win_XP-disk1.vmdk, target path=/home/elatov/.virt/vm/Win_XP-disk1.vmdk, controller=7;channel=0
        (change target path with "--vsys 0 --unit 9 --disk path";
        disable with "--vsys 0 --unit 9 --ignore")


That looks perfect. There is only one Virtual Disk and the Memory, CPU, and other settings are there as well.

Running without the -n (dry run) option looked like this:

    9: Hard disk image: source image=Win_XP-disk1.vmdk, target path=/home/elatov/.virt/vm/Win_XP-disk1.vmdk, controller=7;channel=0
        (change target path with "--vsys 0 --unit 9 --disk path";
        disable with "--vsys 0 --unit 9 --ignore")
    0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
    Successfully imported the appliance.


Initially I had an issue with the OVA template, another user ran into the issues and it's described in [this](https://forums.virtualbox.org/viewtopic.php?f=8&t=49682) VirtualBox forum. I got around the issue by re-creating another OVA template. If you still have issues just extract the OVA into a folder and then import the OVF instead. That process seems to be more stable, here is the command you would run if you had extracted the OVA template under a folder called **test**:

    [elatov@klaptop ~]$ VBoxManage import test/Win_XP.ovf

After the import is finished, start VirtualBox and you will see you newly imported VM:

![VirtualBox with VM Imported Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/VirtualBox_with_VM_Imported.png)

If you want to keep it command line, you can run:

    VBoxManage list -l vms


And that will show you a lot of information regarding the VM that you just imported. Here is a shorter version:

    [elatov@klaptop ~]$ VBoxManage list vms
    "vm" {26008c65-d6ef-40d8-8a2e-093a5eb8baa5}


After powering on the VM, I saw the following:

![WIN Boot Error Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/WIN_Boot_Error.png)

## Fixing "Error loading operating system" After Migrating XP VM

The first that I did was change the Disk Controller from SCSI to IDE. Here is how the Storage settings initially after the migration:

![Storage Settings after Conversion Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/Storage_Settings_after_Conversion.png)

Then I changed the Contoller to IDE and also added my XP ISO, so I could boot from it. Here is how the settings looked like after the changes:

![storage settings to ide Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/storage_settings_to_ide.png)

Reboot the VM and you will see "Press any key to boot from the CD..". I pressed "Enter" and it started booting from the CD and then you will see this screen:

![xp welcome screen Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/xp_welcome_screen.png)

At this point I typed "R" to bring up the "Recovery Command Prompt". At the command prompt, I ran the following:

    fixmbr c:
    fixboot c:


Here is how that looks like:

![fixboot fixmbr Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/fixboot_fixmbr.png)

That should re-install the MBR on the disk. After I rebooted the error was gone, but it just showed a black screen like so:

![blank screen after fix boot Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/blank-screen-after-fix-boot.png)

At this point I booted from the Vista Recovery Disk. The disk used to be available for free from "[Windows Vista Recovery Disc Download](http://neosmart.net/blog/2008/windows-vista-recovery-disc-download/)", but now you have to pay for it. If you have a Vista Install CD, you can use that as well. Here is how my storage settings looked after I added the new ISO:

![storage settings with vista recovery Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/storage_settings_with_vista_recovery.png)

Rebooting the VM and pressing "Enter" at the "Press any key to boot from CD....", I saw the following screen:

![win vista install windows Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/win_vista_install_windows.png)

I then clicked "Next" and saw the following screen:

![vista repair your computer Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/vista_repair_your_computer.png)

Then I clicked "Repair your Computer" and I saw the following:

![vista repair options Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/vista_repair-options.png)

At that screen I clicked "Next" and that yielded this screen:

![vista rec options command prompt Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/vista_rec-options_command_prompt.png)

I then clicked "Command Prompt" and in the command prompt I fixed the MBR again (just for good measure) with following commands:

    bootrec /fixmbr
    bootrec /fixboot


Here is how it looked like:

![bootrec vista Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/bootrec_vista.png)

Looking around some settings, I noticed that the partition is not active, I then ran the following to activate the partition:

    c:\diskpart
    DISKPART>select disk 0
    DISKPART>select partition 1
    DISKPART>active


Here is how it looked like in the prompt:

![vista mark partition active Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/vista_mark_partition_active.png)

Notice the "*" (star) next to the partition after making it active. Then I rebooted and I saw a successful Windows boot process:

![xp successful boot Migrating a VM from VMware Workstation to Oracle VirtualBox](https://github.com/elatov/uploads/raw/master/2013/03/xp_successful_boot.png)
