---
tags: cosc349
---
# COSC349 Lab 2—Cloud Architecture—2022

[Lab 4]: /DclJIDNxQtO40T8TnOOvEg
[COSC301 lab book]: https://www.cs.otago.ac.nz/cosc301/student2019.pdf

## Preliminaries

### Dynamically updating lab exercises...

When we discover bugs, typos, or areas that need elaboration in any of the lab exercises, Dave or Pradeesh may well be fixing things _in_ the lab class. If you log in to https://hackmd.io you will be able to edit the lab exercises yourself, and will have any changes anyone makes to this page pushed to your web browser dynamically. If you do not log in, before seeking help, try reloading the web page to receive any updates to its content.

### Before starting lab 2... please register for AWS Educate

Before starting on the material for lab 2, please work through the sign up process for AWS Academy. You will need to have completed the COSC349 "test" on Blackboard that gices me (Dave) permission to upload your student email address to AWS Academy for use of AWS cloud resources.

Joining AWS Academy may require some manual approval on the AWS side, so you want to be doing this ahead of when you need to use AWS cloud resources in labs and assignments.

## Lab 2—Virtualisation with VirtualBox

The computers in Lab E and Lab F can be (re)booted into macOS or Linux. This lab requires you to boot into macOS, since VirtualBox is not installed on the CS Lab Linux environment.

Note that this lab exercise has a small amount of overlap with part of the COSC301 labs—both use VirtualBox—although key points of difference is that COSC349 labs are self-paced, and not assessed. Some specific references may be made to material from the [COSC301 lab book] and you are of course welcome to browse the rest of the lab book should it be useful to you. (This link is to the 2019 version of the COSC301 lab book, which is sufficiently recent for our neds, although the screen captures may look slightly different.)

Throughout the exercises, "VM" will be used as an abbreviation of "virtual machine".

### Lab objectives

1. Initiate AWS Academy account access for lab 4.
2. Learn how to create and manage virtual machines using VirtualBox.
3. Understand at a high-level what an OVA file contains, and how to import VMs into VirtualBox.
4. Add a new web page on a LAMP stack running within a VirtualBox VM that you import.
5. Connect to a VirtualBox VM that you have created, using Secure Shell (`ssh`), so that you can perform development and administrative tasks.

## Caution: VirtualBox creates large files

As is typical with most (full / hardware) virtualisation systems, working with VirtualBox will involve working with large files. You should keep an eye on where these large files accumulate, regardless of whether you are using your own computer, or using CS Lab computers. The typical sources of large files are:
- VMs' hard disk image files
- Files that record snapshots of VM state (e.g., memory), or facilitate rolling-back changes to VMs' hard disk image files
- Snapshots or clones of VMs
- ISO images used to boot virtual machines

:::warning
In some circumstances, these large files may remain on your computer even if you uninstall VirtualBox.
:::

## Initial configuration of VirtualBox in the CS labs

We recommend that you follow the same guidance as in the [COSC301 lab book] for setting up VirtualBox in the CS labs, as quoted below (with minor tweaks) from procedure 1.1 in section 1.4. This is not a strict requirement, but doing so means that you may be able to follow advice and documentation within the [COSC301 lab book] in addition to the instructions within this lab class.

> Creating a “myvms” folder on your Desktop is a reasonable place to store your virtual machines.
> You must first configure VirtualBox appropriately such that the location of the virtual machine files will be stored in the "myvms" folder
> 1. Start VirtualBox, which you will find in the Applications folder or the Dock on your macOS machine.
> 2. From the menu, choose VirtualBox→Preferences....
> 3. Under the General section, use the pull down menu at the right-hand side of the "Default Machine Folder:", choose "Other...", and then select the "myvms" folder that you created. For me, the resulting value is `/home/cshome/d/dme/Desktop/myvms`, and you can type in the equivalent for your user account directly.

:::info
The CS Labs are running version 6.1.16. When you first start up VirtualBox, you may need to click "Details" on the welcome window that is shown on the right-hand part of the VirtualBox Manager window, so that you see the same sort of display that I do.

If you are running VirtualBox on your own computer, I assume that you will be using something in the version 6 series, from which the screen captures in this lab were recorded.

Note that previous screen captures will sometimes be reused (e.g., some are from version 6.1.2), assuming that you are comfortable to make up for small differences in the user interface.
:::

:::danger
Note that the backups for your Computer Science network home will **not** include your virtual machine disk images. Thus, you should be careful to ensure that you can easily restore the content of any VM disks that you create.
:::

## Let's create a new VirtualBox VM

The instructions below lead you to create a new VM running ReactOS.

:::info
Note that the rectangles with dashed outlines in the screenshots below are redacted content, given that all this imagery is publicly available in the cloud, and releasing the screenshots verbatim might accidentally expose sensitive information.
:::

- Start VirtualBox and you should be greeted with the welcome screen shown, or something that looks similar.

![](https://i.imgur.com/Dvm679c.png)

- Select the "New" button and the following pane should appear.

![](https://i.imgur.com/nPqO0Uu.png)

- When you type in the name "ReactOS test", VirtualBox will probably guess that the type of the machine should be "Microsoft Windows", specifically version "Windows 2003 (32-bit)". Otherwise select these options from the pull-down menus. Note that your "Machine Folder" will be different from what's shown in the above screenshot.
- Select "Continue".

![](https://i.imgur.com/cMr7jmm.png)

- The default memory size is OK, so you can click "Continue" again.

![](https://i.imgur.com/YfvWxsP.png)

- For the virtual hard disk, I have elected not to add a virtual hard disk at all. This means that the VM will have no conventional device for persistent storage, but this is not a problem for our initial test.
- Click "Create", and you will be shown the following alert.

![](https://i.imgur.com/imKCKIC.png)

- ... but you can just click "Continue"

![](https://i.imgur.com/vSwTja5.png)

- You can now see your newly created VM on the left-most list of VMs. Note that it is "Powered Off".
- You need to change the configuration of the VM before you can do anything useful with it, since at the moment it has no configured way to boot an operating system.
- Click on the VM in the left-hand-side list.
- Click on the "Settings" button in the toolbar.
- In the settings window that appears, click the "Storage" tab to show the following window.

![](https://i.imgur.com/a9URTsO.png)

- Select the "Empty" CD-drive.
- The right-hand pane of the window indicates that the storage device that you clicked is an "Optical Drive" attached to the "IDE Secondary Master". Note: on newer VirtualBox versions the terminology has been updated to say "IDE Secondary Device 0" and "IDE Secondary Device 1" instead of "IDE Secondary Master" and "IDE Secondary Slave".
:::info
Many technology projects have tried to moved away from potentially sensitive terms, and "master"/"slave" have been quite commonly used. There is plenty [you can read online](https://en.wikipedia.org/wiki/Master/slave_(technology)#Terminology_concerns) about this.
:::
- Click the CD icon to the right of the pull-down menu that contains the text "IDE Secondary Device 0" (was "IDE Secondary Master").

![](https://i.imgur.com/ybduxIj.png)

- A pop up will appear showing recently used virtual CDs (your list will most likely be empty, compared to mine).

![](https://i.imgur.com/XVUGKB1.png)

- You can choose a virtual "CD" to insert into your VM's virtual CD drive. Select "Choose Virtual Optical Disk File..." and the standard file selector panel should appear.
- In the CS Labs, navigate to `/home/cshome/scratch/dme/cosc349/ReactOS-0.4.11-Live.iso`. You can change to an explicit directory by typing <kbd>⌘</kbd><kbd>shift</kbd><kbd>g</kbd> and then typing, or pasting in a pathname.

:::info
If you are doing these labs on a computer other than a CS lab machine, you can find the ReactOS Live-CD ISO file on their website, and [download it from there](https://reactos.org/download/). Indeed ReactOS is at version 0.4.14, but this difference won't matter here.
:::


![](https://i.imgur.com/wvWnKPC.png)

- Then click open, and you should see in the resulting window that the "CD drive" is now no longer "empty".
- Click "OK", and you will be returned to the main VirtualBox screen.

![](https://i.imgur.com/5AZ9sJ6.png)

- Select your VM and click the "Start" button in the top toolbar.

![](https://i.imgur.com/ECeulXU.png)

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

![](https://i.imgur.com/d4XOjpD.png)

- I selected the normal "LiveCD" option and pressed <kbd>enter</kbd>.
- You should see ReactOS load its protected mode components...

:::warning
At least once in the lab environment the VM failed to start up correctly for me. This might have been a network gremlin, but closing the console window (and powering off the VM) then starting it again fixed it for me.
:::

![](https://i.imgur.com/l3opyVZ.png)

- ... and then detect the (virtual) devices attached to the VM.

![](https://i.imgur.com/45K4zQE.png)

- When this process completes, you will be greeted with the welcome screen that checks your language choice (the US defaults are OK).

![](https://i.imgur.com/sfvLvYs.png)

- Click "OK".

![](https://i.imgur.com/TRWnFVC.png)

- You can then select the "Run ReactOS Live CD" button.
- ReactOS should then complete starting up, showing you a desktop interface that widely resembles (past versions of) a common commercial operating system.

:::info
:bulb: 
Aside: ReactOS ended up [in the tech news](https://www.theregister.co.uk/2019/07/03/reactos_a_ripoff_of_the_windows_research_kernel_claims_microsoft_kernel_engineer/) soon after I had first decided to use it with COSC349 labs. While the news item highlights questions regarding the provenance of the source code of ReactOS, the overall mission to create an open-source Win32 operating system is commendable, in my opinion. Such initiatives can extend the life of perfectly functional hardware, despite the need for commercial profits having causing such equipment to be deserted by commercial vendors. (I'm not blaming the vendors, but it would be good for governments and peoples globally to evolve to avoid much of the pointless wastefulness currently embodied within the technology sector... Your mission, should you choose to accept it...)
:::

![](https://i.imgur.com/1Lz2f2A.png)

- When you are finished experimenting with ReactOS, you can click the top-left close button in the macOS window that contains the VM's monitor.

![](https://i.imgur.com/o98ACdX.png)

- From the pane that appears, you can click "Power off the machine" to effectively yank the power-plug out of the virtual computer.

:::success
:heavy_check_mark: 
What have we achieved so far in this lab?

You have now shown that you can start up and interact with a virtual machine—in this case running an experimental operating system—without interference to your host operating system.
:::

## VirtualBox's virtual network choices

VirtualBox provides a rich set of ways to manage virtual networks. VirtualBox [provides good documentation](https://www.virtualbox.org/manual/ch06.html) that you can consult, but for a quick summary of the main types:

- **Not attached**. The guest VM will see a virtual network card (NIC) but it is effectively disconnected from any network.
- **Network Address Translation (NAT)**. In this mode the guest's outward network traffic will be remapped to appear as requests from the host. This makes the guest invisible from the rest of the Internet: incoming requests toward the guest will simply look like requests to connect to the host. "Port forwarding" can be set up to allow these connections to the host to be forwarded back into the guest's network.
- **NAT Network**. Extends NAT to allow multiple VMs assigned to the same NAT Network to talk to each other, as well as being able to access the internet, and be accessed from the host or beyond if port forwarding is set up.
- **Bridged networking**. **Do not use this mode in the CS labs!** In this case, the guest exposes its own MAC address directly to the local-area network—the guest will appear as another computer on the network. Thus the guest can take on its own Internet identity, however this will not work within the UoOtago network, and may cause technical problems.
- **Internal networking**. Creates a LAN that multiple VMs can connected to, but that is not connected to the Internet.
- **Host-only networking**. Like internal networking, but the host is also able to send network traffic to and from the virtual network.

### Setting up a VirtualBox "host only network"

:::danger
:warning: 
At least for me, host-only networking does not function correctly in the CS labs.

You should thus just skip to the next section "Let’s import a VM from an OVA file", and read about host-only networks only if you expect to use them on your own computers.
:::

I have found host-only networking to be useful when working on my laptop, allowing me to add VMs to an internal network where they can communicate with each other, but which also has convenient access to the host network interface. I leave this documentation here largely for your interest.

From the main VirtualBox Manager window, you can select the "File" menu choose "Host Network Manager...". This will bring up a window such as the following (the details will probably be different from mine). Click the "Properties" button in the tool-bar to see the details shown on my screen capture. You will need to click "Create" before you are able to click "Properties" if there are no networks in your list.

![](https://i.imgur.com/Bmywb9H.png)

It is usually best to use a network (e.g., vboxnet0) that has a tick in the "DHCP Server" column. I have shown a host-only network configuration that was useful to me, on my laptop. You can click the "Create" button to add a network if your list is blank. 

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

For testing, we will use an instance of a LAMP stack: namely a VM running Linux that provides the Apache web server, MySQL and the PHP language for web development. We will be working with the OVA file that has been cached at:`/home/cshome/scratch/dme/cosc349/bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ova`

:::info
:bulb: 
If you are doing this lab exercise somewhere other than the CS Labs, you can visit the [Bitnami application catalog] to download an OVA file similar to that above. (Minor differences in versions should make no significant difference... although you shouldn't use the old versions for real applicaions, as they probably contain security flaws.)
:::

### Examining an OVA file (for your interest)

Before we import from the OVA file, you may be interested to see what is inside this type of file. OVA files are actually just compressed TAR files (If you ask "What's a TAR file?", [Wikipedia explains](https://en.wikipedia.org/wiki/Tar_(computing)), but its usual role is akin to ZIP files), so we can use the `tar` command in a shell window to examine the contents of the OVA we are intending to use:

```
$ tar -tf /home/cshome/scratch/dme/cosc349/bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ova
bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ovf
bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.mf
bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64-disk1.vmdk
```

You can add the `v` flag if you want to see the filesizes of these compressed files: you will discover that the vast bulk of the OVA file is made up from the single VMDK file within the archive. [VMDK files][VMDK] are the file format that VMware developed for virtual hard-disk files. The [VMDK file][VMDK] format is now an open standard.

[VMDK]: https://en.wikipedia.org/wiki/VMDK

The OVF file describes the type of information that we manually selected when we created a new VM manually in the previous part of this lab exercise. We can view its content by decompressing just the OVF file from the OVA file:

```
$ tar -xOf /home/cshome/scratch/dme/cosc349/bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ova bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ovf
```

The above command will produce a pile of XML, which isn't particularly human-readable, but does contain some recognisable configuration parameters regarding the virtual hard-disk, the network configuration required, etc.

<details>
<summary>Lump of XML from the OVF file that you can expand—personally, I feel that nobody should have to look at long XML listings without prior warning...</summary>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Generated by VMware ovftool 2.1.0 (build-467744), UTC time: 2019-06-19T11:18:18.779912Z-->
<Envelope vmw:buildId="build-467744" xmlns="http://schemas.dmtf.org/ovf/envelope/1" xmlns:cim="http://schemas.dmtf.org/wbem/wscim/1/common" xmlns:ovf="http://schemas.dmtf.org/ovf/envelope/1" xmlns:rasd="http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/CIM_ResourceAllocationSettingData" xmlns:vmw="http://www.vmware.com/schema/ovf" xmlns:vssd="http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/CIM_VirtualSystemSettingData" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <References>
    <File ovf:href="bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64-disk1.vmdk" ovf:id="file1" ovf:size="702942208"/>
  </References>
  <DiskSection>
    <Info>Virtual disk information</Info>
    <Disk ovf:capacity="16000000000" ovf:capacityAllocationUnits="byte" ovf:diskId="vmdisk1" ovf:fileRef="file1" ovf:format="http://www.vmware.com/interfaces/specifications/vmdk.html#streamOptimized" ovf:populatedSize="2148663296"/>
  </DiskSection>
  <NetworkSection>
    <Info>The list of logical networks</Info>
    <Network ovf:name="bridged">
      <Description>The bridged network</Description>
    </Network>
  </NetworkSection>
  <VirtualSystem ovf:id="bitnami-lampstack">
    <Info>A virtual machine</Info>
    <Name>bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64</Name>
    <OperatingSystemSection ovf:id="102" vmw:osType="otherGuest">
      <Info>The kind of installed guest operating system</Info>
    </OperatingSystemSection>
    <VirtualHardwareSection>
      <Info>Virtual hardware requirements</Info>
      <System>
        <vssd:ElementName>Virtual Hardware Family</vssd:ElementName>
        <vssd:InstanceID>0</vssd:InstanceID>
        <vssd:VirtualSystemIdentifier>bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64</vssd:VirtualSystemIdentifier>
        <vssd:VirtualSystemType>vmx-08</vssd:VirtualSystemType>
      </System>
      <Item>
        <rasd:AllocationUnits>hertz * 10^6</rasd:AllocationUnits>
        <rasd:Description>Number of Virtual CPUs</rasd:Description>
        <rasd:ElementName>1 virtual CPU(s)</rasd:ElementName>
        <rasd:InstanceID>1</rasd:InstanceID>
        <rasd:ResourceType>3</rasd:ResourceType>
        <rasd:VirtualQuantity>1</rasd:VirtualQuantity>
      </Item>
      <Item>
        <rasd:AllocationUnits>byte * 2^20</rasd:AllocationUnits>
        <rasd:Description>Memory Size</rasd:Description>
        <rasd:ElementName>512MB of memory</rasd:ElementName>
        <rasd:InstanceID>2</rasd:InstanceID>
        <rasd:ResourceType>4</rasd:ResourceType>
        <rasd:VirtualQuantity>512</rasd:VirtualQuantity>
      </Item>
      <Item>
        <rasd:Address>0</rasd:Address>
        <rasd:Description>SCSI Controller</rasd:Description>
        <rasd:ElementName>scsiController0</rasd:ElementName>
        <rasd:InstanceID>3</rasd:InstanceID>
        <rasd:ResourceSubType>lsilogic</rasd:ResourceSubType>
        <rasd:ResourceType>6</rasd:ResourceType>
      </Item>
      <Item>
        <rasd:AddressOnParent>0</rasd:AddressOnParent>
        <rasd:ElementName>disk1</rasd:ElementName>
        <rasd:HostResource>ovf:/disk/vmdisk1</rasd:HostResource>
        <rasd:InstanceID>4</rasd:InstanceID>
        <rasd:Parent>3</rasd:Parent>
        <rasd:ResourceType>17</rasd:ResourceType>
      </Item>
      <Item>
        <rasd:AddressOnParent>2</rasd:AddressOnParent>
        <rasd:AutomaticAllocation>true</rasd:AutomaticAllocation>
        <rasd:Connection>bridged</rasd:Connection>
        <rasd:Description>PCNet32 ethernet adapter on &quot;bridged&quot;</rasd:Description>
        <rasd:ElementName>ethernet0</rasd:ElementName>
        <rasd:InstanceID>5</rasd:InstanceID>
        <rasd:ResourceSubType>PCNet32</rasd:ResourceSubType>
        <rasd:ResourceType>10</rasd:ResourceType>
      </Item>
      <vmw:Config ovf:required="false" vmw:key="cpuHotAddEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="memoryHotAddEnabled" vmw:value="false"/>
      <vmw:Config ovf:required="false" vmw:key="tools.syncTimeWithHost" vmw:value="false"/>
    </VirtualHardwareSection>
  </VirtualSystem>
</Envelope>
```
</details>

### Importing the OVA into VirtualBox

VirtualBox knows how to create VMs from OVAs.

From the main VirtualBox Manager window select the menu item `File` → `Import Appliance...` and in the file selection box that appears, navigate to `/home/cshome/scratch/dme/cosc349/bitnami-lampstack-7.2.19-2-r56-linux-debian-9-x86_64.ova` (or wherever you downloaded the OVA file to, if you are not using a CS Lab computer). Note that when you select the file a panel will appear similar to the following one, that has initialised the VM configuration from the OVF file. Note that your pathname at the top will be different. 

Note also that I double-clicked the "Name" field to add the number `1` at the end, as in `bitnami-lampstack-1` so that I could experiment with adding multiple VM instances from the same OVA.

![](https://i.imgur.com/HCkzUv0.png)

Click the "Import" button, and VirtualBox will create the VM specification and extract the hard-disk file.

![](https://i.imgur.com/Gf7i1Nn.png)

When the import is complete, you will be returned to the VirtualBox main window, where you will now see your new VM listed.

However before starting this VM, we need to reconfigure the network. Scroll the right hand pane down so that you see the "Network" section, and note that it is "Bridged Adaptor", which is the type of VirtualBox network that we should not use within the CS labs.

![](https://i.imgur.com/GXAMEl4.png)

Click on the "Network" heading to reveal the following panel.

![](https://i.imgur.com/FRh5OOS.png)

Change the "Bridged Adapter" pull-down menu to "NAT".

![](https://i.imgur.com/EVFrRId.png)

When you see something similar to the above you can click "OK". Note that the "Invalid settings detected" warning relates to the graphics card, but I didn't run into any problems just ignoring the warning...

Now from the VirtualBox main screen, select your Bitnami VM and click the "Start" button. A window should appear showing Bitnami starting up (as viewed from the "monitor" of your VM).

![](https://i.imgur.com/SAgYpOz.png)

After letting Linux boot, I reached a console page that indicated that the VM's networking was not configured correctly...

![](https://i.imgur.com/EfhbRfl.png)

...but after waiting a few seconds the network sorted itself out and the display updated to what follows. Note that in this instance including the password in the screen-shot is not a problem, as the VM here is on a NAT connection, so is not accessible from the Internet.

![](https://i.imgur.com/qUoRIUt.png)

The above screen states that you can use the web address shown, which is http://10.0.2.15 for me, to access the Bitnami web application.

:::warning
:warning: 
However this will not work, as the particular IP address 10.0.2.15 here only exists "behind" the NAT mapping, so browsers on your host computer cannot connect to that address. (Originally I tried using a host-only network for this part of the lab, which was straightforward on my laptop, but did not work in the CS Labs.)
:::

We need to set up "port forwarding" so that some network ports on our host computer get mapped back through to the virtual network that our VM is on, behind the NAT mapping.

As discussed previously, NAT means your VM's outward network connections appear to come from the host machine. However this means any inward connections will only reach your host machine.

We need to set up "port forwarding" to cause some of your host's network ports to get connected back to network ports on your guest VM.

Click on the network icon at the bottom of the console window: it is the third icon from the left on the previous screen capture. A drop-down menu should appear—

![](https://i.imgur.com/FZATbRQ.png)

—from which you should click "Network Settings...", which will take you back to the network configuration screen. Not all settings can be changed now that the VM is actually running, but port forwarding _can_ be (re-)configured while the VM is "powered on".

Click on the "Advanced" spinner, to reveal the "Port Forwarding" button, which you should select.

![](https://i.imgur.com/ZvHmeoI.png)

In the window that appears, you can edit a table that contains a row per port forwarding rule. Each rule will cause a given port on a given IP address on the host, to be mapped back to a given port on a given IP address of your guest.

Enter data to match the screen capture below, but note that you need to insert the IP address in the "Guest IP" column of _your_ VM, which will be shown on the console for `bitnami-lampstack-1` on your computer. The Host IP should be entered as shown, as `127.0.0.1` is a special IP address meaning the host itself.

:::info
:bulb: 
Just for your interest, if you enter your host's IP address on its LAN connection, instead of `127.0.0.1`, this means that computers *elsewhere on the internet* can potentially connect to that port and reach your VM—which is almost certainly **not** what you want to do, within this lab exercise, but can be useful in other contexts.
:::

![](https://i.imgur.com/bM2SCyH.png)

:::warning
:warning: 
Note that you need to click "OK" on all the settings windows you've opened before the port forwarding will actually be activated! (Clicking "OK" on the window showing the table of NAT rules is not enough.)
:::

Now if you open http://127.0.0.1:8180 in your web browser (or whatever port you used for the port forwarding configuration), you should see the Bitnami welcome screen.

![](https://i.imgur.com/nlLM2I1.png)

The preceding page is generated by the Apache web server running on the Linux VM that you imported.

:::warning
:loudspeaker: 
Please let me know if you have absolutely no experience or comfort with web technology such as HTML. While we will be using web technologies in the lab exercises, we will not focus on best practice in using these web technologies, so a prerequisite of COSC212, or even COMP112, would have been overkill. You can access the materials for those papers should you need reference material, and there is also a vast amount of high quality learning material available on the web. 

Computer Science is also this year (2022) in the process of launching our new 200-level papers, which are intended to eventually provide the authoritative information source about web technologies and networking.
:::

Now let's login to the console of the VM (follow its instructions in terms of the initial password, required changes to the password, etc.).

:::info
:cloud: 
In my case, I fumbled entering the existing, default password, and thus the first-login requirement to change password didn't actually successfully change my password! You can always later change your password within your VM by using the `passwd` command within a shell running on the VM.
:::

![](https://i.imgur.com/572EX1i.png)

Copy the shell commands on the following screen-shot into your VM, taking care to match the syntax precisely.

Note that the `cat` command is just being used to check that the contents of the file `t.php` are as expected.

![](https://i.imgur.com/fzUtTxe.png)

What you have achieved is creation of a new PHP script that runs the `phpinfo()` function. You should be able to see the output of this script by modifying the URL you visited to show your Bitnami web application above, to have a `t.php` at the end of it. I get output that includes the following.

![](https://i.imgur.com/TQ3aZGo.png)

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

![](https://i.imgur.com/GuaFqzf.png)

After doing so, you can use `ssh` from your host computer to connect to your VM. The IP address you connect to on the host is what you set up in the port forwarding to map to the VM's port 22 (where SSH runs), and the `-p` switch is used to select the port number on the host to connect to (8122 here).

If you try to run the `ssh` command and get the message "Connection refused", or "Connection closed by remote host" then this usually means there isn't a server running on the network port that you're trying to connect to.

![](https://i.imgur.com/7mAL4l6.png)

You can see from the `cat` command invocation that I have been able to access the files on the VM, from the convenience of a Terminal.app window on my macOS host.

## Create another LAMP stack VM

Your VirtualBox Manager window should look similar to this:

![](https://i.imgur.com/l1FxL9P.png)

Meanwhile your VM `bitnami-lampstack-1` should have console output similar to (you may need to "wake up" the console by typing a key):

![](https://i.imgur.com/q24selZ.png)

Let's now import the Bitnami LAMP stack again, but to a second VM, while the first continues to run. We will call `bitnami-lampstack-2`, which requires that you double-click, change then press <kbd>enter</kbd>, so that your import page looks like:

![](https://i.imgur.com/I5P2X0Z.png)

Returning to the VirtualBox Manager window should now look similar to:

![](https://i.imgur.com/clv7TSL.png)

We need to change the networking options for the new VM, and again we will chose "NAT" for the type of network that the NIC (Adapter 1) is attached to. Click on the "Network" heading and change the network type to NAT:

![](https://i.imgur.com/YNdxsFu.png)

So now the Manager page should look like:

![](https://i.imgur.com/S2XmmHv.png)

Start your VM, and wait for the console to show functioning networking details (this can take a minute or so). For example:

![](https://i.imgur.com/emyq1uc.png)

Note that the IP address is exactly the same as for the first VM! This is possible because the internal networks the two VMs are on are independent.

You can repeat the port forwarding setup, but this time use ports 8280 and 8222. (I'm actually using an explicit pattern for these port numbers, although this is just for my convenience. The pattern is 8MPP where M is the virtual machine number (1 or 2) and PP is the target port on the VM for the port forwarding.)

Now try to view the web server on your second VM.

![](https://i.imgur.com/wI9dwy9.png)

Were you to try to show `t.php` as for `bitnami-lampstack-1`, though,, you should find that that file isn't present.

You will need to again use the steps you went through above to enable SSH on your second VM. After doing so, you can connect to your `bitnami-lampstack-2` VM using SSH.

```
$ ssh -p 8222 bitnami@127.0.0.1
bitnami@127.0.0.1's password: 
Linux debian 4.9.0-9-amd64 #1 SMP Debian 4.9.168-1+deb9u3 (2019-06-16) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
       ___ _ _                   _
      | _ |_) |_ _ _  __ _ _ __ (_)
      | _ \ |  _| ' \/ _` | '  \| |
      |___/_|\__|_|_|\__,_|_|_|_|_|

  *** Welcome to the Bitnami LAMP 7.2.19-2 ***
  *** Documentation:  https://docs.bitnami.com/virtual-machine/infrastructure/lamp/ ***
  ***                 https://docs.bitnami.com/virtual-machine/ ***
  *** Bitnami Forums: https://community.bitnami.com/ ***
Last login: Tue Jul 16 13:11:30 2019 from 10.0.2.2
bitnami@debian:~$ echo 'This is bitnami-lampstack-2' >test.txt
bitnami@debian:~$ cat test.txt
This is bitnami-lampstack-2
bitnami@debian:~$ logout
Connection to 127.0.0.1 closed.
```

Note that in the above listing, a command was included to create a file `test.txt`, which you should execute within your VM, also.

## Connect your VMs together

There are many ways that VirtualBox can connect together the two VMs that you have created, using a virtual network. The simplest approach is to use a VirtualBox host-only network, but as noted, this did not work as expected in the CS labs.

A more complicated approach—that we will not use today—is to give each of your VMs a second network card. The first network card can be configured using NAT, as we've done above, and this allows for the VM to get internet access via your host, and if you set up port forwarding, you can get access from your host back to your VM (for some small number of explicitly port forwarded ports). The second network card can use an VirtualBox internal network (choose the same internal network name!), and you can assign different IP addresses to each VM.

However, we will try a different way to get the two VMs connected, that was introduced in VirtualBox version 5 (I think): a NAT Network. This may fail to work for you in the CS labs, and if it does, just continue with the exercises in the next section. It did work for me when I tested it.

We need to create a new NAT Network. To do so, from the VirtualBox Manager window, select the "VirtualBox" menu, and "Preferences...". Choose the "Network" tab.

![](https://i.imgur.com/GBdHCEb.png)

Select the small NIC icon with a + on the right-hand side of the window. You can use the bottom-most right-hand side icon to view and change the configuration of that new NAT Network. Note the "Port Forwarding" button.

![](https://i.imgur.com/Jj3tg2G.png)

Now return to each of your VM console windows, and shut down your VMs. You can do this cleanly by logging into the VM and running `sudo poweroff`. Or you can just click the top-left window close icon and choose "Power off the machine" (virtually yanking out the power cord). Note that you can also shut down your VMs from the VirtualBox Manager window, e.g., by clicking on the machine and then using items within the "Machine" menu ("Close" -> "Power Off").

Open the network configuration of each VM and change the network to "NAT Network", ensuring that both machines have the same NatNetwork selected, that you created above.

Start up your two VMs, and check that they show different IP addresses. In the CS labs mine came up as:
- bitnami-lampstack-1 10.0.2.4
- bitnami-lampstack-2 10.0.2.5

If you see different IP addresses for your two VMs, great! You can skip the next section, and continue from the section entitled "DHCP worked..."

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
Remember that your IP addresses are likely to be different from the ones I see, so you will need to replace the values I see with the values on your system.
:::

Your two VMs should now be able to reach each other, using the virtual network. You can test this by using the `ping` command (`ping` performs a simple network connectivity test between where it is run, and some other point on the internet). I logged into my `bitnami-lampstack-1` VM using its console window and could successfully ping both my own address (10.0.2.15) and that of the other VM (10.0.2.16). 

![](https://i.imgur.com/9GHOvAl.png)

:::success
Try to SSH from one machine to the other. You can use the console of one VM to run the `ssh` command to reach the other.
:::

Finally, let's try to use the port forwarding configuration of the NAT Network to allow us to reach both VMs' web pages from a web browser running on our host. Here is a screen capture of how I configured port forwarding for the NAT Network.

![](https://i.imgur.com/B3oGjxA.png)

Here are my two welcome screens. You can use whatever mapping suits you, but I recommend using the one we adopted above. 

![](https://i.imgur.com/jTtrf3Z.png)

![](https://i.imgur.com/GY6u3SV.png)

I then try to access `t.php`, which is only present on the first VM.

![](https://i.imgur.com/JbnEyZm.png)

![](https://i.imgur.com/Jx01JLX.png)

## Suggested exercises if you have time and/or interest

1. Experiment with deploying a more complicated PHP script into your Bitnami LAMP stack. For example, determine how to use the `scp` command (or equivalent) to copy PHP files to and from your host in order to edit them on your host, and copy back the updated versions to be served up by the guest's webserver.
2. Examine the Bitnami stack and see how effectively you can deploy other Bitnami applications from their OVA files.
3. Explore your ReactOS VM, and read up on the project. The goal of creating an open source Windows NT compatible operating system is a worthy one, albeit ambitious in scale.
4. (Challenge) Build a demonstrator of a system that produces output based on multiple VMs interacting.

## Cleaning up

If you do not explicitly need to keep a VM, you should remove it, so as to free up storage space—particularly if it is in your home directory on a CS Lab computer.

To remove a VM and its virtual hard disks, ensure that the VM is stopped. This can be done by clicking the top-left window close icon of the VM's console window, and choosing "Power off the machine".

You can then select the VM in the VirtualBox Manager window, and from the Machine menu (or by right-clicking the VM's entry), select "Remove..." and select "Delete all files", such as shown in the following screen capture:

![](https://i.imgur.com/xbte9aZ.png)
