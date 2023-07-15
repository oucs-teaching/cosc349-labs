---
tags: cosc349
---
[Lab 4]: /DclJIDNxQtO40T8TnOOvEg
[COSC301 lab book]: https://www.cs.otago.ac.nz/cosc301/student2019.pdf
[ReactOS Live CD ZIP file]: https://downloads.sourceforge.net/reactos/ReactOS-0.4.14-release-77-gf6507c2-live.zip

## Preliminaries

:::info
:information_source: 
Dynamically updating lab exercises...

When we discover bugs, typos, or areas that need elaboration in any of the lab exercises, Dave or Pradeesh may well be fixing things _in_ the lab class. If you log in to https://hackmd.io you will be able to edit the lab exercises yourself, and will have any changes anyone makes to this page pushed to your web browser dynamically. If you do not log in, before seeking help, try reloading the web page to receive any updates to its content.
:::

:::warning
:warning: Before starting lab 2... please register for AWS Academy

Before starting on the material for lab 2, please work through the sign up process for AWS Academy. You will need to have completed the COSC349 "test" on Blackboard that gices me (Dave) permission to upload your student email address to AWS Academy for use of AWS cloud resources---so do that as soon as practical, if you haven't already.

Joining AWS Academy may require some manual approval on the AWS side, so you want to be doing this ahead of when you need to use AWS cloud resources in labs and assignments.
:::

## Lab 2—Virtualisation with VirtualBox

:::warning
:warning: 
From this year, the computers in Lab E and Lab F are running Microsoft Windows (even if on Apple hardware). Thus there may be some transitional bumps.
:::

(Note that this lab exercise has a small amount of overlap with part of the COSC301 labs—both use VirtualBox—although key points of difference include that COSC349 labs are self-paced, and not assessed... We may try to refer to some material from the COSC301 Lab Bok where it is relevant and useful.)

Throughout the exercises, "VM" will be used as an abbreviation of "virtual machine".

### Lab objectives

1. Initiate AWS Academy account access so that you are ready for lab 4 and beyond.
2. Learn how to create and manage virtual machines using VirtualBox.
3. Understand at a high-level what an OVA file contains, and how to import VMs into VirtualBox.
4. Add a new web page on a LAMP stack running within a VirtualBox VM that you import.
5. Connect to a VirtualBox VM that you have created, using Secure Shell (`ssh`), so that you can perform development and administrative tasks.

## Caution: VirtualBox creates large files

As is typical with most (full / hardware) virtualisation systems, working with VirtualBox will involve working with large files. You should keep an eye on where these large files accumulate, regardless of whether you are using your own computer, or using CS Lab computers. The typical sources of large files include:
- VMs' hard disk image files;
- Files that record snapshots of VM state (e.g., memory), or facilitate rolling-back changes to VMs' hard disk image files;
- Snapshots or clones of VMs; or
- ISO images used to boot virtual machines.

:::warning
:warning: 
In some circumstances, these large files may remain on your computer even if you uninstall VirtualBox.
:::

## Initial configuration of VirtualBox in the Owheo CS Labs

I have been told that as of early in week one of semester, the VirtualBox application configuration within the CS Labs is now set up to default to a place that allows for fast storage of large files. Like your home directory, this "J" drive should migrate to different computers in the labs that you log into. However, unlike your home directory, you cannot access the "J" drive easily from the remote Otago Student Desktop (but that's just for information, as VirtualBox, Docker, etc., won't run on the Otago Student Desktop anyway).

:::info
:pencil: Note that I am showing screen captures from my laptop, which is a macOS device running a recent version of VirtualBox 7. The CS Lab environment may be slightly behind my version. Just let me know if you encounter any problems that my testing of the CS Lab environment has not uncovered.
:::

:::danger
:warning:
The backups for your Computer Science network home may not include your virtual machine disk images. Thus, you should be careful to ensure that you can easily restore the content of any VM disks that you create.
:::

## Let's create a new VirtualBox VM

The instructions below lead you to create a new VM running ReactOS.

- Download the [ReactOS Live CD ZIP file] (about 80MB) to a location on your lab/home computer where you can find it later. (I just used the usual macOS 'Downloads' folder.)
- Decompress the ZIP file you downloaded to extract the ~265MB ISO file (ISO files are serialisations of the contents of a CD/DVD).

:::info
Note that any rectangles with dashed outlines in the screenshots below are redacted content, given that all this imagery is publicly available in the cloud, and releasing the screenshots verbatim might accidentally expose sensitive information.
:::

- Start VirtualBox and you should be greeted with the welcome screen shown, or something that looks similar.

![](https://hackmd.io/_uploads/HJrHblgq2.png)

- Select the "New" button and the following pane should appear.

![](https://hackmd.io/_uploads/SJnwbxl9n.png)

- When you fill in the name "ReactOS test", VirtualBox should guess that the type of the machine should be "Microsoft Windows", specifically version "Windows 2003 (32-bit)". Otherwise select these options from the pull-down menus. Note that your "Machine Folder" will be different from what's shown in the above screenshot.
- In the "ISO Image" section, select the ISO file you extracted previously: use the right-hand-side pull-down, choose "Other..." and then navigate to the ISO file.

![](https://hackmd.io/_uploads/HyP2Nle52.png)

- Select "Next", and you will see the RAM memory and CPU options.

![](https://hackmd.io/_uploads/HyHlrgxch.png)

- The default memory size and CPU allocation is OK, so you can click "Next" again.

![](https://hackmd.io/_uploads/rkyBSexcn.png)

- For the virtual hard disk, you will see from the above that I have elected not to add a virtual hard disk at all. This means that the VM will have no conventional device for persistent storage, but this is not a problem for our initial test.
- Click "Next", and you will be shown a summary.

![](https://hackmd.io/_uploads/BkovHeec2.png)

- You can select "Finish".

![](https://hackmd.io/_uploads/H1IFBlgq2.png)

- You may need to dismiss a warning message about not having a hard disk in your new (virtual) computer.

![](https://hackmd.io/_uploads/ByjMLee5h.png)

- You will be returned to the main VirtualBox window.

![](https://hackmd.io/_uploads/BytSUxl52.png)

- You can now see your newly created VM on the left-most list of VMs. Note that it is "Powered Off".

:::info
:confused: This note is presently waiting a more logical home position within the document. It's not wrong, so is lingering, but involved steps no longer required in newer VirtualBox versions.

:information_source: 
Many technology projects have tried to move away from potentially sensitive terms, such as "master"/"slave" that have been quite commonly used in the past. There is plenty [you can read online](https://en.wikipedia.org/wiki/Master/slave_(technology)#Terminology_concerns) about this.
:::

- Select your VM and click the "Start" button in the top toolbar.
- A new window should appear that is the "monitor" of your VM, which I will also refer to as the "console window".
- On Apple Macs with retina displays, VirtualBox may present a microscopic window. You can change the window size by clicking on he little picture of a screen in the bottom toolbar of the VM's window (i.e., the one with title "ReactOS test [Running]"). You can choose "Virtual Screen 1" and a setting like "Scale to 200%".
- Note that VirtualBox may pop up some prompts over the top of the console window. You can dismiss them once or permanently by clicking on the icons at the right-hand side of the pop-ups.

:::warning
:warning: 
Note that the VirtualBox "monitor" or "console windows" will potentially "capture" your mouse and/or keyboard. This is because it is trying to pass as much data as possible to the VM. However, this can be surprising if your mouse pointer vanishes, or you can't use your keyboard to switch windows anymore!

To release your keyboard and mouse, you need to press the VirtualBox "host key", which is shown at the bottom-right of the console window. It's the left-<kbd>command</kbd> key on my system: i.e., pressing that key once will "release" my keyboard and mouse.

If you are using an operating system such as ReactOS that understands that it is being virtualised (e.g., it has "guest extensions" installed), then your host computer's keyboard and mouse will integrate with the guest windows without this keyboard/mouse capture.
:::

- You should see VirtualBox's power-on self-test proceed (in as much as it makes sense for such a thing, in a VM!), and then the "CD" drive should boot.

![](https://hackmd.io/_uploads/S1QX_gg53.png)

- I selected the normal "LiveCD" option and pressed <kbd>enter</kbd>.
- You should see ReactOS load its protected mode components...

:::warning
:warning: 
At least once in the lab environment the VM failed to start up correctly for me. This might have been a network gremlin, but closing the console window (and powering off the VM) then starting it again fixed it for me.
:::

![](https://hackmd.io/_uploads/rkZ8_xx53.png)

- ... and then detect the (virtual) devices attached to the VM.

![](https://hackmd.io/_uploads/Hkg1POgl53.png)

- When this process completes, you will be greeted with the welcome screen that checks your language choice (the US defaults are OK).

![](https://hackmd.io/_uploads/r1Qqdxlc3.png)

- Click "Next".

![](https://hackmd.io/_uploads/rkChuelq2.png)

- You can then select the "Run ReactOS Live CD" button.
- ReactOS should then complete starting up, showing you a desktop interface that widely resembles (past versions of) a common commercial operating system.

:::info
:bulb: 
Aside: ReactOS ended up [in the tech news](https://www.theregister.co.uk/2019/07/03/reactos_a_ripoff_of_the_windows_research_kernel_claims_microsoft_kernel_engineer/) soon after I had first decided to use it with COSC349 labs. While the news item highlights questions regarding the provenance of the source code of ReactOS, the overall mission to create an open-source Win32 operating system is commendable, in my opinion. Such initiatives can extend the life of perfectly functional hardware, despite the need for commercial profits having causing such equipment to be deserted by commercial vendors. (I'm not blaming the vendors, but it would be good for governments and peoples globally to evolve to avoid much of the pointless wastefulness currently embodied within the technology sector... Your mission, should you choose to accept it...)
:::

![](https://hackmd.io/_uploads/rkIXYeg5n.png)

- When you are finished experimenting with ReactOS, you can click the top-left close button in the macOS window that contains the VM's monitor.

![](https://hackmd.io/_uploads/BklrYgl5h.png)

- From the pane that appears, you can click "Power off the machine" to effectively yank the power-plug out of the virtual computer.

:::success
:heavy_check_mark: 
What have we achieved so far in this lab?

You have now shown that you can start up and interact with a virtual machine—in this case running an experimental operating system—without interference to your host operating system.
:::

## VirtualBox's virtual network choices

VirtualBox provides a rich set of ways to manage virtual networks. VirtualBox [provides good documentation](https://www.virtualbox.org/manual/ch06.html) that you can consult, but for a quick summary of the main types:

- **Not attached**. The guest VM will see a virtual network card (NIC) but it is effectively disconnected from any network.
- **Network Address Translation (NAT)**. In this mode the guest's outward network traffic will be remapped to appear as requests from the host. This makes the guest invisible (kind of) from the rest of the Internet: incoming requests toward the guest will simply look like requests to connect to the host. "Port forwarding" can be set up to allow these connections to the host to be forwarded back into the guest's network.
- **NAT Network**. Extends NAT to allow multiple VMs assigned to the same NAT Network to talk to each other, as well as being able to access the internet, and be accessed from the host or beyond if port forwarding is set up.
- **Bridged networking**. **Do not use this mode in the CS labs!** In this case, the guest exposes its own Ethernet MAC address directly to the local-area network—the guest will appear as another computer on the network. Thus the guest can take on its own Internet identity, however this will not work within the UoOtago network, and may cause technical problems (as well as possibly leading to staff in ITS to wail and gnash their teeth).
- **Internal networking**. Creates a LAN that multiple VMs can connected to, but that is not connected to the Internet.
- **Host-only networking**. Like internal networking, but the host is also able to send network traffic to and from the virtual network.

### Setting up a VirtualBox "host only network"

:::danger
:warning: 
At least for me, host-only networking does not function correctly in the CS Labs.

You should thus just skip to the next section "Let’s import a VM from an OVA file", and read about host-only networks only if you expect to use them on your own computers.
:::

This section is a bit historical: these days I believe the "NAT Network" option should cover most of the cases that used to suit "host only network" configurations. Thus, perhaps skip this section and return to it only if you cannot get NAT Networks to work for you.

In the past, I have found host-only networking to be useful when working on my laptop, allowing me to add VMs to an internal network where they can communicate with each other, but which also has convenient access to the host network interface. I leave this documentation here largely for your interest.

From the main VirtualBox Manager window, you can select the "File" menu choose "Tools" and "Network Manager", then naviagate to the Host-only Networks tab. In previous VirtualBox versions, "Host Network Manager..." was in the file menu. This will bring up a window such as the following (the details will probably be different from mine). Click the "Properties" button in the tool-bar to see the details shown on my screen capture. You will need to click "Create" before you are able to click "Properties" if there are no networks in your list.

![](https://hackmd.io/_uploads/S1x1yWg53.png)

In present VirtualBox versions, note the term "Legacy" in my migrated settings: Host-only Networks are likely going to be deprecated, I'd guess.

For older VirtualBox versions you can change more settings and see more configuration options. You should ensure that you use a network (e.g., vboxnet0) that has a tick in the "DHCP Server" column. I have shown a host-only network configuration that was useful to me, on my laptop. You can click the "Create" button to add a network if your list is blank. 

On the lower configuration panel, where there is a "Configure Adapter Manually" selected on my system, I have shown settings that should work. You need to click the "Apply" button to cause changes to remain in effect.

On the "DHCP Server" tab near those settings, my network was configured thusly:
- Enable Server
- Server Address: 192.168.56.100
- Server Mask: 255.255.255.0
- Lower Address Bound: 192.168.56.200
- Upper Address Bound: 192.168.56.254

## Let's import a VM from an OVA file

OVA (Open Virtualisation Archive) files collect together the specifications of a VM and also the files that represent its hard disk.

We will use an OVA file from the [Bitnami] ecosystem. The [Bitnami application catalog] provides a vast range of pre-packaged installations of popular open source software.

[Bitnami]: https://bitnami.com
[Bitnami application catalog]: https://bitnami.com/stacks
[Bitnami 7.2.19-2-r56]: https://otagouni.sharepoint.com/:u:/s/COSC3492022/Eb7S04AkgptCqjHfBSHQtyYBE_CwpRwXbf6Dj3qyNjKWiA?e=FGpnGT

For testing, we will use an instance of a LAMP stack: namely a VM running Linux that provides the Apache web server, MySQL and the PHP language for web development. (In the Bitnami application catalog, you can navigate to their LMAP offering by typing "LAMP" in the search bar.) Here is a [direct link to a recent Bitnami LAMP stack OVA file](https://bitnami.com/redirect/to/2334779/bitnami-lamp-8.2.8-r0-debian-11-amd64.ova) (about 500MB) that you should download.

### Examining an OVA file (for your interest)

Before we import from the OVA file, you may be interested to see what is inside this type of file. OVA files are actually just compressed TAR files (If you ask "What's a TAR file?", [Wikipedia explains](https://en.wikipedia.org/wiki/Tar_(computing)), but its usual role is akin to ZIP files), so we can use the `tar` command in a Unix shell window to examine the contents of the OVA we are intending to use. For example:

```
$ tar -tf ~/Downloads/bitnami-lamp-8.2.8-r0-debian-11-amd64.ova
bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64.ovf
bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64.mf
bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64-disk-0.vmdk
```


You can add the `v` flag if you want to see the filesizes of these compressed files: you will discover that the vast bulk of the OVA file is made up from the single VMDK file within the archive. [VMDK files][VMDK] are the file format that VMware developed for virtual hard-disk files. The [VMDK file][VMDK] format is now an open standard.

[VMDK]: https://en.wikipedia.org/wiki/VMDK

The OVF file describes the type of information that we manually selected when we created a new VM manually in the previous part of this lab exercise. We can view its content by decompressing just the OVF file from the OVA file:

```
$ tar -xOf ~/Downloads/bitnami-lamp-8.2.8-r0-debian-11-amd64.ova bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64.ovf
```

The above command will produce a pile of XML, which isn't particularly human-readable, but does contain some recognisable configuration parameters regarding the virtual hard-disk, the network configuration required, etc.

<details>
<summary>Lump of XML from the OVF file that you can expand—personally, I feel that nobody should have to look at long XML listings without prior warning...</summary>

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?><!--Generated by VMware VirtualCenter Server, User: VMC.LOCAL\cloudadmin, UTC time: 2023-07-04T23:56:52.761982Z--><Envelope xmlns="http://schemas.dmtf.org/ovf/envelope/1" xmlns:cim="http://schemas.dmtf.org/wbem/wscim/1/common" xmlns:ovf="http://schemas.dmtf.org/ovf/envelope/1" xmlns:rasd="http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/CIM_ResourceAllocationSettingData" xmlns:vmw="http://www.vmware.com/schema/ovf" xmlns:vssd="http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/CIM_VirtualSystemSettingData" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" vmw:buildId="build-21560480">
  <References>
    <File ovf:href="bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64-disk-0.vmdk" ovf:id="file1" ovf:size="559767040"/>
  </References>
  <DiskSection>
    <Info>Virtual disk information</Info>
    <Disk ovf:capacity="10" ovf:capacityAllocationUnits="byte * 2^30" ovf:diskId="vmdisk1" ovf:fileRef="file1" ovf:format="http://www.vmware.com/interfaces/specifications/vmdk.html#streamOptimized"/>
  </DiskSection>
  <NetworkSection>
    <Info>The list of logical networks</Info>
    <Network ovf:name="sddc-cgw-network-1">
      <Description>The sddc-cgw-network-1 network</Description>
    </Network>
  </NetworkSection>
  <vmw:StorageGroupSection ovf:required="false" vmw:id="group1" vmw:name="VMC Workload Storage Policy - Cluster-1">
    <Info>Storage policy for group of disks</Info>
    <vmw:Description>VMC Workload Storage Policy - Cluster-1 storagepolicy group</vmw:Description>
  </vmw:StorageGroupSection>
  <VirtualSystem ovf:id="bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64">
    <Info>A virtual machine</Info>
    <Name>bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64</Name>
    <OperatingSystemSection ovf:id="96" ovf:version="10" vmw:osType="debian10_64Guest">
      <Info>The kind of installed guest operating system</Info>
      <Description>Debian GNU/Linux 10 (64-bit)</Description>
    </OperatingSystemSection>
    <VirtualHardwareSection ovf:transport="com.vmware.guestInfo">
      <Info>Virtual hardware requirements</Info>
      <System>
        <vssd:ElementName>Virtual Hardware Family</vssd:ElementName>
        <vssd:InstanceID>0</vssd:InstanceID>
        <vssd:VirtualSystemIdentifier>bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64</vssd:VirtualSystemIdentifier>
        <vssd:VirtualSystemType>vmx-14</vssd:VirtualSystemType>
      </System>
      <Item>
        <rasd:AllocationUnits>hertz * 10^6</rasd:AllocationUnits>
        <rasd:Description>Number of Virtual CPUs</rasd:Description>
        <rasd:ElementName>1 virtual CPU(s)</rasd:ElementName>
        <rasd:InstanceID>1</rasd:InstanceID>
        <rasd:ResourceType>3</rasd:ResourceType>
        <rasd:VirtualQuantity>1</rasd:VirtualQuantity>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item>
        <rasd:AllocationUnits>byte * 2^20</rasd:AllocationUnits>
        <rasd:Description>Memory Size</rasd:Description>
        <rasd:ElementName>1024MB of memory</rasd:ElementName>
        <rasd:InstanceID>2</rasd:InstanceID>
        <rasd:Reservation>1024</rasd:Reservation>
        <rasd:ResourceType>4</rasd:ResourceType>
        <rasd:VirtualQuantity>1024</rasd:VirtualQuantity>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item>
        <rasd:Address>0</rasd:Address>
        <rasd:Description>SCSI Controller</rasd:Description>
        <rasd:ElementName>SCSI controller 0</rasd:ElementName>
        <rasd:InstanceID>3</rasd:InstanceID>
        <rasd:ResourceSubType>VirtualSCSI</rasd:ResourceSubType>
        <rasd:ResourceType>6</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="slotInfo.pciSlotNumber" vmw:value="160"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item>
        <rasd:Address>1</rasd:Address>
        <rasd:Description>IDE Controller</rasd:Description>
        <rasd:ElementName>IDE 1</rasd:ElementName>
        <rasd:InstanceID>4</rasd:InstanceID>
        <rasd:ResourceType>5</rasd:ResourceType>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item>
        <rasd:Address>0</rasd:Address>
        <rasd:Description>IDE Controller</rasd:Description>
        <rasd:ElementName>IDE 0</rasd:ElementName>
        <rasd:InstanceID>5</rasd:InstanceID>
        <rasd:ResourceType>5</rasd:ResourceType>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item ovf:required="false">
        <rasd:AutomaticAllocation>false</rasd:AutomaticAllocation>
        <rasd:ElementName>Video card</rasd:ElementName>
        <rasd:InstanceID>6</rasd:InstanceID>
        <rasd:ResourceType>24</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="useAutoDetect" vmw:value="false"/>
        <vmw:Config ovf:required="false" vmw:key="videoRamSizeInKB" vmw:value="4096"/>
        <vmw:Config ovf:required="false" vmw:key="enable3DSupport" vmw:value="false"/>
        <vmw:Config ovf:required="false" vmw:key="use3dRenderer" vmw:value="automatic"/>
        <vmw:Config ovf:required="false" vmw:key="graphicsMemorySizeInKB" vmw:value="262144"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item ovf:required="false">
        <rasd:AutomaticAllocation>false</rasd:AutomaticAllocation>
        <rasd:ElementName>VMCI device</rasd:ElementName>
        <rasd:InstanceID>7</rasd:InstanceID>
        <rasd:ResourceSubType>vmware.vmci</rasd:ResourceSubType>
        <rasd:ResourceType>1</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="allowUnrestrictedCommunication" vmw:value="false"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item>
        <rasd:AddressOnParent>0</rasd:AddressOnParent>
        <rasd:ElementName>Hard disk 1</rasd:ElementName>
        <rasd:HostResource>ovf:/disk/vmdisk1</rasd:HostResource>
        <rasd:InstanceID>8</rasd:InstanceID>
        <rasd:Parent>3</rasd:Parent>
        <rasd:ResourceType>17</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="backing.writeThrough" vmw:value="false"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
        <vmw:StorageGroup ovf:required="false">group1</vmw:StorageGroup>
      </Item>
      <Item>
        <rasd:AddressOnParent>7</rasd:AddressOnParent>
        <rasd:AutomaticAllocation>true</rasd:AutomaticAllocation>
        <rasd:Connection>sddc-cgw-network-1</rasd:Connection>
        <rasd:Description>VmxNet3 ethernet adapter on "sddc-cgw-network-1"</rasd:Description>
        <rasd:ElementName>Network adapter 1</rasd:ElementName>
        <rasd:InstanceID>9</rasd:InstanceID>
        <rasd:ResourceSubType>VmxNet3</rasd:ResourceSubType>
        <rasd:ResourceType>10</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="slotInfo.pciSlotNumber" vmw:value="192"/>
        <vmw:Config ovf:required="false" vmw:key="wakeOnLanEnabled" vmw:value="true"/>
        <vmw:Config ovf:required="false" vmw:key="connectable.allowGuestControl" vmw:value="true"/>
        <vmw:Config ovf:required="false" vmw:key="uptCompatibilityEnabled" vmw:value="true"/>
        <vmw:Config ovf:required="false" vmw:key="uptv2Enabled" vmw:value="false"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item ovf:required="false">
        <rasd:AddressOnParent>0</rasd:AddressOnParent>
        <rasd:AutomaticAllocation>false</rasd:AutomaticAllocation>
        <rasd:ElementName>CD/DVD drive 1</rasd:ElementName>
        <rasd:InstanceID>10</rasd:InstanceID>
        <rasd:Parent>5</rasd:Parent>
        <rasd:ResourceSubType>vmware.cdrom.remotepassthrough</rasd:ResourceSubType>
        <rasd:ResourceType>15</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="backing.exclusive" vmw:value="false"/>
        <vmw:Config ovf:required="false" vmw:key="connectable.allowGuestControl" vmw:value="false"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <Item ovf:required="false">
        <rasd:AddressOnParent>1</rasd:AddressOnParent>
        <rasd:AutomaticAllocation>false</rasd:AutomaticAllocation>
        <rasd:ElementName>CD/DVD drive 2</rasd:ElementName>
        <rasd:InstanceID>11</rasd:InstanceID>
        <rasd:Parent>5</rasd:Parent>
        <rasd:ResourceSubType>vmware.cdrom.remotepassthrough</rasd:ResourceSubType>
        <rasd:ResourceType>15</rasd:ResourceType>
        <vmw:Config ovf:required="false" vmw:key="backing.exclusive" vmw:value="false"/>
        <vmw:Config ovf:required="false" vmw:key="connectable.allowGuestControl" vmw:value="false"/>
        <vmw:CoresPerSocket ovf:required="false">1</vmw:CoresPerSocket>
      </Item>
      <vmw:Config ovf:required="false" vmw:key="cpuHotAddEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="cpuHotRemoveEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="memoryHotAddEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="firmware" vmw:value="bios"/>
      <vmw:Config ovf:required="false" vmw:key="cpuAllocation.shares.shares" vmw:value="1000"/>
      <vmw:Config ovf:required="false" vmw:key="cpuAllocation.shares.level" vmw:value="normal"/>
      <vmw:Config ovf:required="false" vmw:key="simultaneousThreads" vmw:value="1"/>
      <vmw:Config ovf:required="false" vmw:key="tools.syncTimeWithHost" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="tools.syncTimeWithHostAllowed" vmw:value="true"/>
      <vmw:Config ovf:required="false" vmw:key="tools.afterPowerOn" vmw:value="true"/>
      <vmw:Config ovf:required="false" vmw:key="tools.afterResume" vmw:value="true"/>
      <vmw:Config ovf:required="false" vmw:key="tools.beforeGuestShutdown" vmw:value="true"/>
      <vmw:Config ovf:required="false" vmw:key="tools.beforeGuestStandby" vmw:value="true"/>
      <vmw:Config ovf:required="false" vmw:key="tools.toolsUpgradePolicy" vmw:value="manual"/>
      <vmw:Config ovf:required="false" vmw:key="fixedPassthruHotPlugEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="powerOpInfo.powerOffType" vmw:value="soft"/>
      <vmw:Config ovf:required="false" vmw:key="powerOpInfo.resetType" vmw:value="soft"/>
      <vmw:Config ovf:required="false" vmw:key="powerOpInfo.suspendType" vmw:value="hard"/>
      <vmw:Config ovf:required="false" vmw:key="nestedHVEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="vPMCEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="virtualICH7MPresent" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="virtualSMCPresent" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="flags.vvtdEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="flags.vbsEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="bootOptions.efiSecureBootEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="powerOpInfo.standbyAction" vmw:value="checkpoint"/>
      <vmw:ExtraConfig ovf:required="false" vmw:key="nvram" vmw:value="bitnami-lamp-8.2-8.2.8-r0-debian-11-amd64.nvram"/>
    </VirtualHardwareSection>
    <vmw:BootOrderSection vmw:instanceId="8" vmw:type="disk">
      <Info>Virtual hardware device boot order</Info>
    </vmw:BootOrderSection>
    <vmw:StorageSection ovf:required="false" vmw:group="group1">
      <Info>Storage policy group reference</Info>
    </vmw:StorageSection>
  
  <EulaSection>
    <Info>End User License Agreement</Info>
    <License>Please read the terms and conditions at:
https://bitnami.com/agreement</License>
  </EulaSection>
  <ProductSection>
    <Info>VM Arguments</Info>
    <Category>VM Properties</Category>
    <Property ovf:key="va-ssh-public-key" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>Set the SSH public key allowed to access the appliance</Label>
      <Description>This will enable the SSHD service and configure the specified public key for the user 'bitnami'</Description>
    </Property>
    <Property ovf:key="user-data" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>User data to be made available inside the instance</Label>
      <Description>This allows to pass any text to the appliance. It will be executed if it starts with a shebang ("#!"). The value should be encoded in base64</Description>
    </Property>
    <Category>Networking Properties</Category>
    <Property ovf:key="network.ip0" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>Network 1 IP Address</Label>
      <Description>The IP address for the interface (IP/netmask, e.g.: 192.168.1.10/32). Leave blank to enable DHCP.</Description>
    </Property>
    <Property ovf:key="network.gateway" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>Default Gateway</Label>
      <Description>The default network gateway address. Leave blank to enable DHCP.</Description>
    </Property>
    <Property ovf:key="network.dns" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>Domain Name Servers</Label>
      <Description>Comma-separated list of IP addresses to DNS servers.</Description>
    </Property>
    <Property ovf:key="network.domain" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>Domain Name</Label>
      <Description>The fully-qualified domain name.</Description>
    </Property>
    <Property ovf:key="network.searchpath" ovf:type="string" ovf:userConfigurable="true" ovf:value="">
      <Label>DNS Search Path</Label>
      <Description>Comma-separated list of domains to add to the DNS search path.</Description>
    </Property>
  </ProductSection>
</VirtualSystem>
</Envelope>
```
</details>

### Importing the OVA into VirtualBox

VirtualBox knows how to create VMs from OVAs.

From the main VirtualBox Manager window select the menu item `File` → `Import Appliance...` and in the file selection box that appears, navigate to wherever you saved the Bitnami OVA file. Note that when you select the file a panel will appear similar to the following one, that has initialised the VM configuration from the OVF file. Note that your pathname at the top will be different. 

Note also that I double-clicked the "Name" field to shorten it a bit, and to add the number `1` at the end, as in `bitnami-lamp-1` so that I could experiment with adding multiple VM instances from the same OVA later.

![](https://hackmd.io/_uploads/B1vPzWx5h.png)

Click the "Finish" button, read and assuming you accept the terms and conditions, VirtualBox will create the VM specification and extract the hard-disk file.

![](https://hackmd.io/_uploads/ryQpMbl52.png)

When the import is complete, you will be returned to the VirtualBox main window, where you will now see your new VM listed.

However before starting this VM, we need to reconfigure the network. Scroll the right hand pane down so that you see the "Network" section, and note that it is "Bridged Adaptor", which is the type of VirtualBox network that we should not use within the CS labs.

![](https://hackmd.io/_uploads/SkEGQ-gch.png)

Click on the "Network" heading to reveal the following panel.

![](https://hackmd.io/_uploads/HJPXm-lq2.png)

Change the "Bridged Adapter" pull-down menu to "NAT".

![](https://hackmd.io/_uploads/SJWHQZx9n.png)

When you see something similar to the above you can click "OK". Note that the "Invalid settings detected" warning relates to the graphics card, but I didn't run into any problems just ignoring the warning...

Now from the VirtualBox main screen, select your Bitnami VM and click the "Start" button. A window should appear showing Bitnami starting up (as viewed from the "monitor" of your VM).

After letting Linux boot, you may reach a console page that indicates that the VM's networking was not configured correctly ... but usually after waiting a minute or so, the network should sort itself out and the display updated to what follows. Note that in this instance including the password in the screen-shot is not a problem, as the VM here is on a NAT connection, so is not accessible from the Internet.

![](https://hackmd.io/_uploads/H1PlV-g52.png)

The above screen states that you can use the web address shown, which is http://10.0.2.15 for me, to access the Bitnami web application.

:::warning
:warning: 
However this will not work, as the particular IP address 10.0.2.15 here only exists "behind" the NAT mapping, so browsers on your host computer cannot connect to that address. (Originally I tried using a host-only network for this part of the lab, which was reasonably straightforward on my laptop, but did not work in the CS Labs.)
:::

We need to set up "port forwarding" so that some network ports on our host computer get mapped back through to the virtual network that our VM is on, behind the NAT mapping.

As discussed previously, NAT means your VM's outward network connections appear to come from the host machine. However this means any inward connections will only reach your host machine.

We need to set up "port forwarding" to cause some of your host's network ports to get connected back to network ports on your guest VM.

Click on the network icon at the bottom of the console window: it is the third icon from the left on the previous screen capture. A drop-down menu should appear—

![](https://hackmd.io/_uploads/S1iVN-g52.png)

—from which you should click "Network Settings...", which will take you back to the network configuration screen. Not all settings can be changed now that the VM is actually running, but port forwarding _can_ be (re-)configured while the VM is "powered on".

Click on the "Advanced" spinner, to reveal the "Port Forwarding" button, which you should select.

![](https://hackmd.io/_uploads/BJNFVZxc2.png)

In the window that appears, you can edit a table that contains a row per port forwarding rule. Each rule will cause a given port on a given IP address on the host, to be mapped back to a given port on a given IP address of your guest.

Enter data to match the screen capture below, but note that you need to insert the IP address in the "Guest IP" column of _your_ VM, which will be shown on the console for `bitnami-lampstack-1` on your computer. The Host IP should be entered as shown, as `127.0.0.1` is a special IP address meaning the host itself.

:::info
:bulb: 
Just for your interest, if you enter your host's IP address on its LAN connection, instead of `127.0.0.1`, this means that computers *elsewhere on the internet* can potentially connect to that port and reach your VM—which is almost certainly **not** what you want to do, within this lab exercise, but can be useful in other contexts.
:::

![](https://hackmd.io/_uploads/SJe1r-e93.png)

:::warning
:warning: 
Note that you need to click "OK" on all the settings windows you've opened before the port forwarding will actually be activated! (Clicking "OK" on the window showing the table of NAT rules is not enough.)
:::

Now if you open http://127.0.0.1:8180 in your web browser (or whatever port you used for the port forwarding configuration), you should see the Bitnami welcome screen.

![](https://hackmd.io/_uploads/S17HSWl92.png)

The preceding page is generated by the Apache web server running on the Linux VM that you imported.

:::warning
:loudspeaker: 
Please let me know if you have absolutely no experience or comfort with web technology such as HTML. While we will be using web technologies in the lab exercises, we will not focus on best practice in using these web technologies, so a prerequisite of COSC212, or even COMP112, would have been overkill. You can access the materials for those papers should you need reference material, and there is also a vast amount of high quality learning material available on the web. 

Computer Science is also this year (2022) in the process of launching our new 200-level papers, which are intended to eventually provide the authoritative information source about web technologies and networking. Seeking COSC203's material should help!
:::

Now let's login to the console of the VM (follow its instructions in terms of the initial password, required changes to the password, etc.).

:::info
:cloud: 
In my case, I fumbled entering the existing, default password, and thus the first-login requirement to change password didn't actually successfully change my password! You can always later change your password within your VM by using the `passwd` command within a shell running on the VM.
:::

Copy the shell commands on the following screen capture into your VM, taking care to match the syntax precisely.

Note that the `cat` command is just being used to check that the contents of the file `t.php` are as expected.

![](https://hackmd.io/_uploads/HJZa8Wlc2.png)

What you have achieved is creation of a new PHP script that runs the `phpinfo()` function. You should be able to see the output of this script by modifying the URL you visited to show your Bitnami web application above, to have a `t.php` at the end of it. I get output that includes the following.

![](https://hackmd.io/_uploads/rysAU-lq3.png)

:::success
:heavy_check_mark: 
You have now achieved creating a web script—even if it is a small one—on a LAMP stack virtual machine.
:::

## Using Secure Shell (SSH) to access your virtual machine

Using the VirtualBox console to edit files is not ideal, as you cannot easily copy and paste content, transfer files, and so on. Although the console of your VM is being shown within a window on your host computer, analogously to a physical computer display monitor, you can't just peel text and imagery off the physical display monitor itself.

Using Secure Shell (SSH) is often a more convenient way to interact with VMs' when using a command line interface, but by default the SSH server on the Bitnami VMs is disabled, for security reasons.

On the VirtualBox console, you can run the commands shown in the following screen-shot to enable the SSH server. (This command sequence is adapted from [Bitnami's documentation](https://docs.bitnami.com/virtual-machine/faq/get-started/enable-ssh/), which may provide additional links that are useful to you.)

:::info
:eyes: 
Just to emphasise, since some students have missed this in the past: you actually need to look at the commands being typed at the shell prompt within the screen capture that follows!
:::

![](https://hackmd.io/_uploads/S1oa9Wgcn.png)

After doing so, you can use `ssh` from your host computer to connect to your VM. The IP address you connect to on the host is what you set up in the port forwarding to map to the VM's port 22 (where SSH runs), and the `-p` switch is used to select the port number on the host to connect to (8122 here).

If you try to run the `ssh` command and get the message "Connection refused", or "Connection closed by remote host" then this usually means there isn't a server running on the network port that you're trying to connect to.

![](https://hackmd.io/_uploads/H1FIjZlq3.png)

You can see from the `cat` command invocation that I have been able to access the files on the VM, from the convenience of a Terminal.app window on my macOS host.

## Create another LAMP stack VM

Your VirtualBox Manager window should look similar to this:

![](https://hackmd.io/_uploads/SkhjoZx5n.png)

Meanwhile your VM `bitnami-lamp-1` should have console output similar to (you may need to "wake up" the console by typing a key):

![](https://hackmd.io/_uploads/ryWTsWl93.png)

Let's now import the Bitnami LAMP stack again, but to a second VM, while the first continues to run. We will name it `bitnami-lamp-2`, which requires that you double-click, change then press <kbd>enter</kbd>, so that your import page looks like:

![](https://hackmd.io/_uploads/SksX3be52.png)

Returning to the VirtualBox Manager window should now look similar to:

![](https://hackmd.io/_uploads/BkZI3Zlc2.png)

We need to change the networking options for the new VM, and again we will chose "NAT" for the type of network that the NIC (Adapter 1) is attached to. Click on the "Network" heading and change the network type to NAT:

![](https://hackmd.io/_uploads/SyLD3Zlc2.png)

So now the Manager page should look like:

![](https://hackmd.io/_uploads/rJz_hWlqh.png)

Start your VM, and wait for the console to show functioning networking details (this can take a minute or so). For example:

![](https://hackmd.io/_uploads/Hyb62blq2.png)

Note that the IP address is exactly the same as for the first VM! This is possible because the internal networks the two VMs are on are completely independent from each other.

You can repeat the port forwarding setup, but this time use ports 8280 and 8222. (I'm actually using an explicit pattern for these port numbers, although this is just for my convenience. The pattern is 8MPP where M is the virtual machine number (1 or 2) and PP is the target port on the VM for the port forwarding.)

Now try to view the web server on your second VM.

![](https://hackmd.io/_uploads/ryLNaWg5n.png)

Were you to try to show `t.php` as for `bitnami-lampstack-1`, though,, you should find that that file isn't present.

You will need to again use the steps you went through above to enable SSH on your second VM. After doing so, you can connect to your `bitnami-lampstack-2` VM using SSH.

```
$ ssh -p 8222 bitnami@127.0.0.1
The authenticity of host '[127.0.0.1]:8222 ([127.0.0.1]:8222)' can't be established.
ED25519 key fingerprint is SHA256:YWggZf0wlZHeX8CaisZFLz9dTrkDDIUNGwNNMeQjJ9I.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[127.0.0.1]:8222' (ED25519) to the list of known hosts.
bitnami@127.0.0.1's password:
Linux debian 5.10.0-23-amd64 #1 SMP Debian 5.10.179-1 (2023-05-12) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
       ___ _ _                   _
      | _ |_) |_ _ _  __ _ _ __ (_)
      | _ \ |  _| ' \/ _` | '  \| |
      |___/_|\__|_|_|\__,_|_|_|_|_|

  -> Welcome to LAMP packaged by Bitnami 8.2.8
  -> Documentation:   https://docs.bitnami.com/virtual-machine/infrastructure/lamp/
  -> Bitnami Support: https://github.com/bitnami/vms/issues
Last login: Sun Jul 16 00:12:56 2023
_____________________________________________________________________
WARNING! Your environment specifies an invalid locale.
 The unknown environment variables are:
   LC_CTYPE=en_NZ.UTF-8 LC_ALL=
 This can affect your user experience significantly, including the
 ability to manage packages. You may install the locales by running:

 sudo dpkg-reconfigure locales

 and select the missing language. Alternatively, you can install the
 locales-all package:

 sudo apt-get install locales-all

To disable this message for all users, run:
   sudo touch /var/lib/cloud/instance/locale-check.skip
_____________________________________________________________________

bitnami@debian:~$
logout
Connection to 127.0.0.1 closed.
```

## Connect your VMs together

There are many ways that VirtualBox can connect together the two VMs that you have created, using a virtual network. The simplest approach is to use a VirtualBox host-only network, but as noted, this did not work as expected in the CS labs.

A more complicated approach—that we will not use today—is to give each of your VMs a second network card. The first network card can be configured using NAT, as we've done above, and this allows for the VM to get internet access via your host, and if you set up port forwarding, you can get access from your host back to your VM (for some small number of explicitly port forwarded ports). The second network card can use an VirtualBox internal network (choose the same internal network name!), and you can assign different IP addresses to each VM.

However, we will try a different way to get the two VMs connected, that was introduced in VirtualBox version 5 (I think): a NAT Network. This may fail to work for you in the CS labs, and if it does, just continue with the exercises in the next section. It did work for me when I tested it.

We need to create a new NAT Network. To do so, from the VirtualBox Manager window, select the "File", "Tools", "Network Manager" (in previous versions, instead in "VirtualBox" menu, and "Preferences...". Choose the "Network" tab).

![](https://hackmd.io/_uploads/HyGuyzxc2.png)

You will likely see a blank list, and thus should select the "Create" button, and match the settings shown in my screen capture, above.

Now return to each of your VM console windows, and shut down your VMs. You can do this cleanly by logging into the VM and running `sudo poweroff`. Or you can just click the top-left window close icon and choose "Power off the machine" (virtually yanking out the power cord). Note that you can also shut down your VMs from the VirtualBox Manager window, e.g., by clicking on the machine and then using items within the "Machine" menu ("Close" -> "Power Off").

Open the network configuration of each VM and change the network to "NAT Network", ensuring that both machines have the same NatNetwork selected, that you created above.

Start up your two VMs, and check that they show different IP addresses. If you see different IP addresses for your two VMs, great! You can skip the next section, and continue from the section entitled "DHCP worked..."

On my laptop, the two VMs fetched different addresses:
- bitnami-lamp-1 10.0.2.5
- bitnami-lamp-2 10.0.2.4
However, the login banner continued to show 10.0.2.15 on both VMs (!). I was able to check the IP addresses that the VMs were being allocated by logging in to the console and running the `ip addr` command. I actually ran `ip addr | fgrep 10.0.2` to filter out all the lines I did not need to look at.

If the `ip addr` command is _also_ still giving the same IP address for both computers, you can continue directly into the section that follows this one...

### If DHCP doesn't work...

If your IP addresses are shown to be the same, you need them to be different, as we're moving the machines to be on the same internal network (the NatNetwork you created). You can try to assign an IP address to your second VM, `bitnami-lampstack-2`, explicitly, as I did.

My second VM reported IP address 10.0.2.15, so I just manually assigned an IP address one higher: 10.0.2.16. I followed the [directions on how to assign a static IP address](https://docs.bitnami.com/virtual-machine/faq/configuration/configure-static-address/) on the Bitnami website. The following screen capture shows me using the `cat` command (the `nano` editor is not installed within the VMs, although `vi` is installed, if you know how to use that editor) to create a new network description (press <kbd>control</kbd><kbd>d</kbd> to stop typing into the file). I then copy (i.e., `cp`) that file to the location the system expects.

![](https://i.imgur.com/sWJ3TXJ.png)

You should then be able to restart your first VM and check that it is using a different IP address. Mine is behaving as expected, now using `10.0.2.16`, as it states on the login banner.

![](https://i.imgur.com/b9gKHoW.png)

:::info
:bulb:
If you want to temporarily change the IP address of your VM (the change won't survive reboots), you can instead use the `ifconfig` command to find out the name of your network interface (my VM used an interface named `enp0s3` so yours probably will too), then run a command to remove the old address `sudo ip addr del 192.168.56.201/24 dev enp0s3` and add the new address that you want, such as `sudo ip addr add 192.168.56.202/24 dev enp0s3`.
:::


:::warning
:warning:
You shouldn't need to change the IP addresses manually, and I wouldn't expect that you would be entirely comfortable with this process. Future tools that we use should make the whole process easier. If things seem to break for you, just continue with the suggested exercises in the next section.
:::

### DHCP worked to give your VMs different IP addresses

:::warning
:warning:
Remember that your IP addresses mau well be different from the ones I see, so you will need to replace the values I see with the values on your system.
:::

Your two VMs should now be able to reach each other, using the virtual network. You can test this by using the `ping` command (`ping` performs a simple network connectivity test between where it is run, and some other point on the internet). I logged into my `bitnami-lampstack-1` VM using its console window and could successfully ping both my own address (10.0.2.4) and that of the other VM (10.0.2.5). 

![](https://hackmd.io/_uploads/ry3xVMx9n.png)

:::success
:pencil:
Try to SSH from one machine to the other. You can use the console of one VM to run the `ssh` command to reach the other.
:::

Finally, let's try to use the port forwarding configuration of the NAT Network to allow us to reach both VMs' web pages from a web browser running on our host. Here is a screen capture of how I configured port forwarding for the NAT Network.

![](https://hackmd.io/_uploads/rJxLNGg93.png)

Here are my two welcome screens. You can use whatever mapping suits you, but I recommend using the one we adopted above. 

![](https://hackmd.io/_uploads/BJq_Nflcn.png)

![](https://hackmd.io/_uploads/rkpvVMe93.png)

As expected, when I then try to access `t.php`, it is only present on the first VM.

![](https://hackmd.io/_uploads/SJ8jEGl5n.png)

![](https://hackmd.io/_uploads/By43EMxc3.png)

## Suggested exercises if you have time and/or interest

:::success
:pencil:
1. Experiment with deploying a more complicated PHP script into your Bitnami LAMP stack. For example, determine how to use the `scp` command (or equivalent) to copy PHP files to and from your host in order to edit them on your host, and copy back the updated versions to be served up by the guest's webserver.
2. Examine the Bitnami stack and see how effectively you can deploy other Bitnami applications from their OVA files.
3. Explore your ReactOS VM, and read up on the project. The goal of creating an open source Windows NT compatible operating system is a worthy one, albeit ambitious in scale.
4. (Challenge) Build a demonstrator of a system that produces output based on multiple VMs interacting.
:::

## Cleaning up

If you do not explicitly need to keep a VM, you should remove it, so as to free up storage space—particularly if it is in your home directory on a CS Lab computer.

To remove a VM and its virtual hard disks, ensure that the VM is stopped. This can be done by clicking the top-left window close icon of the VM's console window, and choosing "Power off the machine".

You can then select the VM in the VirtualBox Manager window, and from the Machine menu (or by right-clicking the VM's entry), select "Remove..." and select "Delete all files", such as shown in the following screen capture:

![](https://hackmd.io/_uploads/S1AXHGx93.png)
