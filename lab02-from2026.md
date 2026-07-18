[Lab 4]: /DclJIDNxQtO40T8TnOOvEg
[COSC301 lab book]: https://www.cs.otago.ac.nz/cosc301/student2019.pdf
[ReactOS Live CD ZIP file]: https://sourceforge.net/projects/reactos/files/ReactOS/0.4.15/ReactOS-0.4.15-x86-live.zip/download

## Preliminaries

:::info
:information_source: 
Dynamically updating lab exercises...

When we discover bugs, typos, or areas that need elaboration in any of the lab exercises, Dave, Aaron,  may well be fixing things _in_ the lab class. If you log in to https://hackmd.io you will be able to edit the lab exercises yourself, and will have any changes anyone makes to this page pushed to your web browser dynamically. If you do not log in, before seeking help, try reloading the web page to receive any recent updates to its content.
:::

:::warning
:warning: Before starting lab 2... please register for AWS Academy

Before starting on the material for lab 2, please work through the sign up process for AWS Academy. You will need to have completed the COSC349 "quiz" on Aoroa that gices me (Dave) permission to upload your student email address to AWS Academy for use of AWS cloud resources---so do that as soon as practical, if you haven't already.

Joining AWS Academy may require some manual approval on the AWS side, so you want to be doing this ahead of when you need to use AWS cloud resources in labs and assignments.
:::

## Lab 2—Virtualisation tools: VirtualBox and Vagrant

Throughout the exercises, "VM" will be used as an abbreviation of "virtual machine".

### Lab objectives

1. (Initiate AWS Academy account access so that you are ready for future labs and beyond.)
2. Learn how to create and manage virtual machines using VirtualBox.
3. Understand that VirtualBox operations can be automated using its command line tools (e.g., `VBoxManage`)
4. Learn how to use a the Vagrant software tool to automate creation of virtual machines
5. Configure provisioning of virtual machines using Vagrant, i.e., running Unix shell commands on the virtual machine after it is created, to get it ready for its intended use.

## Caution: VirtualBox may create large files

As is typical with most (full / hardware) virtualisation systems, working with VirtualBox will involve working with large files. You should keep an eye on where these large files accumulate, regardless of whether you are using your own computer, or using Owheo Lab computers. The typical sources of large files include:
- VMs' hard disk image files;
- Files that record snapshots of VM state (e.g., memory), or facilitate rolling-back changes to VMs' hard disk image files;
- Snapshots or clones of VMs; or
- ISO images used to boot virtual machines.

:::warning
:warning: 
In some circumstances, these large files may remain on your computer even if you uninstall VirtualBox. (Although you should be able to find documentation regarding how to cleanly remove all files.)
:::

## Initial configuration of VirtualBox in the Ōwheo SoC Labs

:::success
:pencil2:
Note that I am showing screen captures from some of my laptops, which here are both macOS devices, one with an Arm CPU and one with an Intel CPU. Both are running VirtualBox 7 (7.2.12). The SoC Labs are running a similar version of VirtualBox (7.2.10). It may be that the SoC Lab version stays fixed as it is presently for the semester, even if new VirtualBox versions are released, so just be aware that you may end up with a slightly newer version of VirtualBox on your own laptop if you install it in future weeks.
:::

:::warning
:warning: The ISO assumes an Intel CPU. On my M-series Mac I have enabled an experimental VirtualBox option that emulates Intel CPUs. From a Terminal window you can run—
```shell=
VBoxManage setextradata global "VBoxInternal2/EnableX86OnArm" 1
```
—although this has apparently been quite unstable in the past, so I wouldn't rely on this for assignment work unless you enjoy living dangerously. You can disable this option by changing the `1` to `0` in the above command.
:::

:::danger
:warning:
The backups for your home drive on the SoC lab computers may not include your virtual machine disk images. Thus, you should be careful to ensure that you can easily restore the content of any VM disks that you create.
:::

## Let's create a new VirtualBox VM

The instructions below lead you to create a new VM running ReactOS.

- Download the [ReactOS Live CD ZIP file] (an 80MB(ish) ZIP file) to a location on your lab/home computer where you can find it later. (I just used the usual macOS 'Downloads' folder.)
- Decompress the ZIP file you downloaded to extract the ~265MB ISO file (ISO files are serialisations of the contents of a CD/DVD). (You can probably just double-click the ZIP file in a Windows/macOS/Linux GUI desktop to get at the ISO file within the ZIP file.)
- Start VirtualBox and you should be greeted with the welcome screen shown, or something that looks similar.

![image](https://hackmd.io/_uploads/HyEMRadVMe.png)

- Select the "New" button and the following pane should appear.

![image](https://hackmd.io/_uploads/rJ6URa_NMg.png)

- When you fill in the name "ReactOS test", VirtualBox should guess that the type of the machine should be "Microsoft Windows", specifically version "Windows Server 2003 (32-bit)". Otherwise select these options from the pull-down menus. Note that your "Machine Folder" will be different from what's shown in the above screenshot.

- In the "ISO Image" section, select the ISO file you extracted previously: use the right-hand-side pull-down, choose "Other..." and then navigate to the ISO file.

![image](https://hackmd.io/_uploads/SkBZy0O4zl.png)

- You can use the "Specify virtual hardware" pull down to see the RAM memory and CPU options.

![image](https://hackmd.io/_uploads/HJA4yAO4Gg.png)

- The default memory size and CPU allocation is OK, so you can proceed to select the "Specify virtual hard disk" pull-down.

![image](https://hackmd.io/_uploads/S12tyC_NMe.png)

- For the virtual hard disk, you will see from the above that I have elected not to add a virtual hard disk at all. This means that the VM will have no conventional device for persistent storage, but this is not a problem for our initial test. (Also, real VMs and servers can operate like this if they, for example, use network-based storage.)
- You can select "Finish". (You may need to dismiss a warning message about not having a hard disk in your new (virtual) computer.)
- You will be returned to the main VirtualBox window.

![image](https://hackmd.io/_uploads/ryh-eAO4Gx.png)

- You can now see your newly created VM on the left-most list of VMs. Note that it is "Powered Off".

:::info
:confused: This note is presently waiting a more logical home position within the document. It's not wrong, so is lingering, but was added in response to previous versions of VirtualBox, and is no longer required in present versions.

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

To release your keyboard and mouse, you need to press the VirtualBox "host key", which is shown at the bottom-right of the console window. It's the left-<kbd>command</kbd> key on my Mac systems: i.e., pressing that key once will "release" my keyboard and mouse back to the host operating system (macOS in my case).

If you are using a guest operating system such as ReactOS that understands that it is being virtualised (e.g., it has "guest extensions" installed or supports paravirtualised hardware—we explain what this is in lectures), then your host computer's keyboard and mouse will integrate with the guest windows without this keyboard/mouse capture.
:::

- You should see VirtualBox's power-on self-test proceed (in as much as it makes sense for such a thing, in a VM!), and then the "CD" drive should boot.

![image](https://hackmd.io/_uploads/SyGtEC_4ze.png)

- I selected the normal "LiveCD" option and pressed <kbd>enter</kbd>.
- You should see ReactOS load its protected mode components...

:::warning
:warning: 
At least once in the lab environment the VM failed to start up correctly for me. This might have been a network gremlin, but closing the console window (and powering off the VM) then starting it again fixed it for me.
:::

![image](https://hackmd.io/_uploads/HyjhNAONGx.png)

- ... and then detect the (virtual) devices attached to the VM.

![image](https://hackmd.io/_uploads/SkHbr0OEGl.png)

- When this process completes, you will be greeted with the welcome screen that checks your language choice (the US defaults are OK).

![image](https://hackmd.io/_uploads/Sy6QSRuVGl.png)

- Click "Next".

![image](https://hackmd.io/_uploads/SkSPrCdEze.png)

- You can then select the "Run ReactOS Live CD" button.
- ReactOS should then complete starting up, showing you a desktop interface that widely resembles (past versions of) a common commercial operating system.

:::info
:bulb: 
Aside: ReactOS ended up [in the tech news](https://www.theregister.co.uk/2019/07/03/reactos_a_ripoff_of_the_windows_research_kernel_claims_microsoft_kernel_engineer/) soon after I had first decided to use it with COSC349 labs. While the news item highlights questions regarding the provenance of the source code of ReactOS, the overall mission to create an open-source Win32 operating system is commendable, in my opinion. Such initiatives can extend the life of perfectly functional hardware, despite the need for commercial profits having causing such equipment to be deserted by commercial vendors. (I'm not blaming the vendors, but it would be good for governments and peoples globally to evolve to avoid much of the pointless wastefulness currently embodied within the technology sector... Your mission, should you choose to accept it...)
:::

![image](https://hackmd.io/_uploads/ByT_8CdVGl.png)

- When you are finished experimenting with ReactOS, you can click the top-left close button in the macOS window that contains the VM's monitor, or the window close button on the top-right of a Windows window.

![image](https://hackmd.io/_uploads/BkFqLRu4Gx.png)

- From the pane that appears, you can click "Power off the machine" to effectively yank the power-plug out of the virtual computer.

:::success
:heavy_check_mark: 
What have we achieved so far in this lab?

You have now shown that you can start up and interact with a virtual machine—in this case running an experimental operating system—without interference to your host operating system.
:::

## VirtualBox's virtual network choices

VirtualBox provides a rich set of ways to manage virtual networks. VirtualBox [provides good documentation](https://www.virtualbox.org/manual/ch06.html) that you can consult, but for a quick summary of the main types:

- **Not attached**. The guest VM will see a virtual network card (NIC) but it is effectively disconnected from any network.
- **Network Address Translation (NAT)**. (:point_left: NAT is the default for new VMs.) In this mode the guest's outward network traffic will be remapped to appear as requests from the host. This makes the guest invisible (kind of) from the rest of the Internet: incoming requests toward the guest will simply look like requests to connect to the host. "Port forwarding" can be set up to allow these connections to the host to be forwarded back into the guest's network.
- **NAT Network**. Extends NAT to allow multiple VMs assigned to the same NAT Network to talk to each other, as well as being able to access the internet, and be accessed from the host or beyond if port forwarding is set up.
- **Bridged networking**. **Do not use this mode in the Owheo labs!** In this case, the guest exposes its own Ethernet MAC address directly to the local-area network—the guest will appear as another computer on the network. Thus the guest can take on its own Internet identity, however this will not work within the UoOtago network, and may cause technical problems (as well as possibly leading to staff in ITS to wail and gnash their teeth).
- **Internal networking**. Creates a LAN that multiple VMs can connected to, but that is not connected to the Internet.
- **Host-only networking**. Like internal networking, but the host is also able to send network traffic to and from the virtual network. (I think that this form of networking is going to be deprecated in favour of NAT Network, so I'm dropping out the material that I have previously included about it within this lab.)

## Cleaning up

If you do not explicitly need to keep a VM, you should remove it, so as to free up storage space—particularly if it is in your home directory on a Owheo Lab computer.

To remove a VM and its virtual hard disks, ensure that the VM is stopped. This can be done by clicking the top-left window close icon of the VM's console window, and choosing "Power off the machine".

You can then select the VM in the VirtualBox Manager window, and from the Machine menu (or by right-clicking the VM's entry), select "Remove..." and ensure that the "Delete the virtual machine files" option is selected, such as shown in the following screen capture:

![image](https://hackmd.io/_uploads/r1hKPR_Vfg.png)

## VirtualBox has a command line interface

So far we have controlled VirtualBox using its GUI. However VirtualBox has an extremely rich command line interface, too.

You can explore the [VirtualBox documentation][VBoxManage documentation] for more details, but some exploratory examples are shown as follows—although note that the real output has been filtered to make it more readable.

- List the running VMs (none, at the time asked)
  ```
  $ VBoxManage list runningvms
  ```
- List the VMs defined, whether or not they are currently running. The right-hand column contains unique identifiers for each VM instance (yours should definitely be different from mine).
  ```
  $ VBoxManage list vms
  "test-netboot" {fbbe7c81-b536-4256-a315-c9d8d7321a66}
  "ReactOS test" {686dde2f-56c2-4e24-907a-86c3fc1afa13}
  ```
- Provide information about the host computer
  - Here's my old Intel Mac laptop's information:
  ```
  $ VBoxManage list hostinfo
  Host Information:
  
  Host time: 2026-07-18T10:59:27.398000000Z
  Processor online count: 8
  Processor count: 8
  Processor online core count: 4
  Processor core count: 4
  Processor supports HW virtualization: yes
  Processor supports PAE: yes
  Processor supports long mode: yes
  Processor supports nested paging: no
  Processor supports unrestricted guest: yes
  Processor supports nested HW virtualization: no
  Processor supports virt. vmsave/vmload: no
  Processor#0 speed: 2600 MHz
  Processor#0 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#1 speed: 2600 MHz
  Processor#1 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#2 speed: 2600 MHz
  Processor#2 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#3 speed: 2600 MHz
  Processor#3 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#4 speed: 2600 MHz
  Processor#4 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#5 speed: 2600 MHz
  Processor#5 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#6 speed: 2600 MHz
  Processor#6 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Processor#7 speed: 2600 MHz
  Processor#7 description: Intel(R) Core(TM) i7-6700HQ CPU @ 2.60GHz
  Memory size: 16384 MByte
  Memory available: 6435 MByte
  Operating system: Darwin
  Operating system version: 24.6.0
  ```
  - Here's the equivalent for my Arm Mac:
  ```
  $ VBoxManage list hostinfo
  
  Host Information:
  
  Host time: 2026-07-18T11:01:40.523000000Z
  Processor online count: 12
  Processor count: 12
  Processor online core count: 12
  Processor core count: 12
  Processor supports HW virtualization: yes
  Processor supports PAE: no
  Processor supports long mode: yes
  Processor supports nested paging: yes
  Processor supports unrestricted guest: yes
  Processor supports nested HW virtualization: no
  Processor supports virt. vmsave/vmload: no
  Processor#0 speed: unknown
  Processor#0 description: Apple M3 Pro E (sawtooth)
  Processor#1 speed: unknown
  Processor#1 description: Apple M3 Pro E (sawtooth)
  Processor#2 speed: unknown
  Processor#2 description: Apple M3 Pro E (sawtooth)
  Processor#3 speed: unknown
  Processor#3 description: Apple M3 Pro E (sawtooth)
  Processor#4 speed: unknown
  Processor#4 description: Apple M3 Pro E (sawtooth)
  Processor#5 speed: unknown
  Processor#5 description: Apple M3 Pro E (sawtooth)
  Processor#6 speed: 450971566 MHz
  Processor#6 description: Apple M3 Pro P (everest)
  Processor#7 speed: unknown
  Processor#7 description: Apple M3 Pro P (everest)
  Processor#8 speed: unknown
  Processor#8 description: Apple M3 Pro P (everest)
  Processor#9 speed: unknown
  Processor#9 description: Apple M3 Pro P (everest)
  Processor#10 speed: unknown
  Processor#10 description: Apple M3 Pro
  Processor#11 speed: unknown
  Processor#11 description: Apple M3 Pro
  Memory size: 36864 MByte
  Memory available: 15881 MByte
  Operating system: Darwin
  Operating system version: 25.5.0
  ```
- List the NAT Networks (there is a longer version of Lab 2 we've used in the past that relied on NAT Networks).
  ```
  $ VBoxManage natnetwork list
  NAT Networks:
  
  Name:         NatNetwork
  Network:      10.0.2.0/24
  Gateway:      10.0.2.1
  DHCP Server:  Yes
  IPv6:         No
  IPv6 Prefix:  fd17:625c:f037:2::/64
  IPv6 Default: No
  Enabled:      Yes
  
  1 network found
  ```

If you explore the `VBoxManage` command's [documentation][VBoxManage documentation], you will usually find ways to achieve whatever you might have done through the GUI. Indeed there are a number of functions that are only easily available through the command line interface.

[VBoxManage documentation]: https://www.virtualbox.org/manual/ch08.html

## Vagrant for automating virtualisation

:::info
:bulb: 
This is another lab for which the most consistent experience is with Intel CPUs. Apple Mac users with Arm CPUs _may_ run into difficulties, because VirtualBox (which sits beneath Vagrant) only recently began supporting Arm CPUs. But now that the teaching team have Arm Macs ourselves, we're working to update the instructions where we can. I (Dave) was able to work through the lab on my Arm-based Mac successfully.

If VirtualBox fails to work on your Arm-CPU-based Mac, you can take a related path, if you are willing to try it, using Vagrant with a different virtualisation provider: Docker, rather than VirtualBox. You can try the instructions at https://altitude.otago.ac.nz/cosc412/demo-vm/-/tree/docker?ref_type=heads to the point of getting `vagrant ssh` to work, and then see what lines up and what doesn't in the exercise below. The teaching team can provide more context as to what's going on (Dave and Aaron worked through this originally).
:::

:::warning
:warning: 
If you are working on the SoC Lab computers, my experience was that some `vagrant` commands did not work when I ran them within the old Windows command line (`cmd.exe`). They did work within Git Bash, however, so I'd suggest using that.
:::

## Motivation for the Vagrant tool

[Vagrant] is a developer tool that allows you to conveniently interact with "headless" VMs, i.e., VMs that do not rely on you needing to see anything displayed on the "monitor" connected to their "graphics card". (It actually does far more than just support interacting with headless VMs, but we'll get on to that later...)

Vagrant does not provide a virtualisation system itself, it instead uses an existing virtualisation system (or will explain that it can't run because it can't find a virtualisation system to use). The most common setup has Vagrant controlling headless VirtualBox VMs. Some of Vagrant's more common use patterns appear as a convenience layer over the VirtualBox command line interface.

## Ensure that Vagrant is working ##

- Check that Vagrant is installed. You can do so by running the following command from a terminal (a small difference in version number may not cause issues):

```bash
$ vagrant --version
Vagrant 2.4.9
```

:::info
:eyes: 
Vagrant and VirtualBox can sometimes fail to work together depending on their specific versions. This will usually be documented online.

The exercises are tested on SoC Lab computers to try to ensure that you don't run into version problems. Sometimes there may be minor differences in the output shown in the lab exercises from the versions of software that you are using, however.
:::

- If you run into problems within the SoC Labs and have a computer that you can administer yourself, you can [install Vagrant][Vagrant] on it. Note, however that because Vagrant uses VirtualBox, if you're on a Mac with an Arm CPU, you may run into difficulties with either tool (Arm support is growing over time).

[Vagrant]: https://www.vagrantup.com

## Set up from scratch a `Vagrantfile`, i.e., a Vagrant environment

In a command shell (e.g., Git Bash on a SoC Lab computer), create a new directory on your `J:` drive in which to store your Vagrant environment, and change into that new directory.

Now run the `vagrant init` command. Vagrant has been written to try to provide useful feedback, so please do read it, and indeed it has indicated what the `vagrant init` command actually did. (Of course the notice abut there being a new Vagrant version will probably be different in your environment.)

```
$ vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

Let's list our working directory with `ls -a` to see hidden files too—

```
$ ls -a
.           ..          Vagrantfile
```

—and indeed we see no change other than the creation of the `Vagrantfile`.

### The purpose of a `Vagrantfile`

A `Vagrantfile` is intended to sit at the top-level directory of a project that software developers, such as yourselves, are working on. It seems likely that the name is analogous to a `Makefile`, which also (often) sits at the top-level of a project, and specifies how to build the software that makes up that project.

A very common and convenient working model is that the `Vagrantfile` is contained in the top-level of a Git repository. It is likely that this way of working will be useful for your first COSC349 assignment.

The context of many `vagrant` command invocations is determined by Vagrant searching the current working directory and each parent directory in turn until it first finds a `Vagrantfile` (Git does something similar to locate `.git` directories). This means that the Vagrant commands are run in the context of a particular project. If Vagrant can't find a `Vagrantfile`, the tool with present an error message.

The `Vagrantfile` specifies the configuration, initial (virtual) hard-disk content (a so-called Vagrant "box"), and scripting to set up one or more VMs that are relevant to a project. 

As noted above, most common `vagrant` command invocations determine their context from the current working directory (or its parent directories, until a `Vagrantfile` is found), and thus you can easily check out two copies of a project into different directories, e.g., a stable version and an unstable one that you are working on, and the same `vagrant` command invocation will refer to independent instances of the VMs for that project.

## A tour of Vagrant's features via the default `Vagrantfile`

We will explore a number of Vagrant's key features and concepts by working through the default `Vagrantfile`.

Although you can often treat a `Vagrantfile` just as a static configuration file, it's in fact valid code in the [Ruby] programming language. This can be used to positive effect (e.g., easy ability to use expressions, conditionals and loops in your configurations), although there is a risk that complex code in your `Vagrantfile` may make it very difficult for others to understand. (On one occasion I created a `Vagrantfile` so complex in its use of Ruby features that I subsequently found it extremely difficult to understand how it worked—so try not to do that!)

[Ruby]: https://www.ruby-lang.org/en/

It turns out that the default `Vagrantfile` is almost entirely comments aimed at helping you orient yourself. Some additional context will be provided, below.

These following header lines tell editors that the file is Ruby source code (editors would typically use the extension of the filename to determine whether a source file is Ruby).

```ruby=
# -*- mode: ruby -*-
# vi: set ft=ruby :

```

The following code can be considered immutable magic... along with a helpful URL and comment or two.

```ruby=+
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

```
### Vagrant "box" files

Vagrant uses its "box" files as the starting point for a VM's storage. Essentially Vagrant boxes are virtual hard disk files, but with some important considerations:
- boxes are made conveniently available for sharing at the https://app.vagrantup.com/boxes/search cloud service, and
- box files—certainly the official ones—are likely to have been carefully cut down in size, just to contain what's necessary to run a Vagrant VM. For example, the Vagrant box I use for Ubuntu Linux leaves out the parts of the distribution that support graphics, as Vagrant VMs are intended only to be interacted with using a command line.
:::info
:bulb: 
Note that Vagrant boxes not supporting graphical output devices is a pragmatic limitation rather than a technical one: it is usually technically possible to start with a Vagrant box that doesn't support a GUI and then install all the files necessary to support GUI graphics... but to do so would feel (pragmatically) like using the wrong tool for the job... (e.g., perhaps just use VirtualBox's GUI to setup the VM of yours that provides a GUI?)
:::

One key, required configuration parameter is the Vagrant "box" to use. It is likely that you will change the box to something other than `base`, as shown in the default `Vagrantfile`. For general purpose development, Ubuntu Linux boxes are popular and useful.

```ruby=+
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "base"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

```

### Vagrant will configure network port forwarding

Another important feature that Vagrant expects to configure for your VMs is network port forwarding. In general VMs will be configured with a network that uses NAT (search/see above), since this provides an easy way for the VMs to be able to pull data from the internet.

However in NAT modes of networking, port forwarding is required to allow the host machine (or computers elsewhere on the Internet) to connect to servers running on the VM. We will explore this further, below.

Note that all of the parameters suggested by Vagrant are actually commented out, here.

```ruby=+
  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

```

### Vagrant can define virtual networks that you need

As noted above, Vagrant VMs usually use NAT to reach the Internet. Vagrant can configure additional private or public networks for your VMs to use—this is particularly useful if your `Vagrantfile` creates multiple VMs, and those VMs should be able to communicate with each other, e.g., on a private network, in addition to how they connect to the outside world.

:::warning
:warning: 
Note that in the SoC Labs, you should not create `public_networks`. This is for the same reasons (see above) that you should not create VirtualBox "bridged" network adapters.
:::

```ruby=+
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

```

### Synchronised folders

Vagrant supports a really useful developer feature: the directory containing the `Vagrantfile` is normally set up as a shared folder between the host, and the guest VM, where it is set up to appear in the filesystem under the `/vagrant` subdirectory.

We will explore this feature below. The configuration parameters here, that are commented out, relate to creating extra shared folders, in addition to the one that appears at `/vagrant`.

```ruby=+
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessable to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

```

### Provider-specific parameters

As mentioned above, Vagrant doesn't provide a virtualisation engine itself. It instead uses what it terms to be a "provider". The default provider is VirtualBox.

Vagrant is intended to allow you to change provider and still do useful work for you, e.g., you should be able to set up your environment, and then change the provider from VirtualBox to VMware, or change to a provider that deploys directly to cloud services.

Nonetheless, it can be useful to include configuration parameters that are specific for a given virtualisation provider. The VirtualBox-specific parameters shown here allow you to turn on display of the "monitor" (i.e., virtual display device) of the VirtualBox VM, and/or to change the memory allocated (although in this case you would probably first need to first look up in the Vagrant documentation what units that "1024" number is using!).

```ruby=+
  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

```

### VM provisioning

Another key feature of Vagrant is its extensive support for integration with provisioning systems. These systems configure computers (i.e., the VMs that Vagrant has created, in our case here) that are running to match a particular specification.

:::info
:thought_balloon: 
As a specific example of a provisioning system, CFEngine used to be used many years ago back in the days of the Department of Computer Science to install software, and generally check that the machines are running as expected.
:::

The simplest provisioning approach is to run a shell script as soon as the VM has been created and has booted. The commented-out lines of shell script here (74 and 75) would install the Apache Web Server (version 2) on a Linux system that uses the `apt` package management system, such as Debian or Ubuntu.

The point of provisioning is that you automate steps you'd otherwise have to repeat each time you set up a new instance of a particular design of VM you want to use. A particular point of use is during collaborative development: instead of providing written instructions to your teammates as to how to set up a matching VM environment to yours, you can automate all the setup within the provisioning sections.

A good way of checking that your provisioner is functioning as intended and that your scripting is complete is to create another working directory, `git clone` (or otherwise copy over) the content of your repository, and try deploying fresh VMs temporarily. The behaviour of the fresh, temporary VMs and those associated with your working copy should match.

:::info
:bulb: 
Note that configuring provisioning scripts can take a significant amount of time: many commands may not work in precisely the same way in a provisioning script as they do when you run them manually on the command-line. Nonetheless, the effort expended typically pays off rapidly, when you need to share or rebuild the systems that you have provisioned.
:::

```ruby=+
  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```
## Let's create a Vagrant VM

In your `Vagrantfile` let's change the box specified from ``"base"`` (which is just a stub) to a choice of substance: Ubunu Linux. Specifically let's use ``"bento/ubuntu-22.04"`` which is a particular organisation's packaging of Ubuntu for use with Vagrant. That should be on around line 15 of the `Vagrantfile` shown here, but your line number may differ, depending on software versions.
(Past labs on Intel machines suggested using ``"ubuntu/focal64"``, which may appear in older output snippets.)

You should now be able to have Vagrant set up a Ubuntu 64-bit VM for you. Run the `vagrant up` command. An example interaction is shown, below.

:::warning
:warning: 
The first time you use a Vagrant box, it needs to be downloaded (it's about 270 megabytes (Arm image is more like 700 megabytes...), and stored locally. After that time, it will not be downloaded again: any other 
VMs that you request that use the same box name will reuse the box file that has already been cached.
:::

```
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'bento/ubuntu-22.04' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'bento/ubuntu-22.04'
    default: URL: https://vagrantcloud.com/api/v2/vagrant/bento/ubuntu-22.04
==> default: Adding box 'bento/ubuntu-22.04' (v202510.26.0) for provider: virtualbox (amd64)
    default: Downloading: https://vagrantcloud.com/bento/boxes/ubuntu-22.04/versions/202510.26.0/providers/virtualbox/amd64/vagrant.box
==> default: Successfully added box 'bento/ubuntu-22.04' (v202510.26.0) for 'virtualbox (amd64)'!
```

At this point the Vagrant box will have been cached, so the output below relates to creating your specific VM. Hopefully you can get the general gist of what is being done from the output below. You do not need to understand all of the output that is produced in order to use the VM you've requested.

:::info
:eyes: 
Some of the output shown here will not match exactly what you see, but the general shape should match.
:::

```
==> default: Importing base box 'bento/ubuntu-22.04'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'bento/ubuntu-22.04' version '202510.26.0' is up to date...
==> default: Setting the name of the VM: cosc349-testing_default_1784375450199_8965
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /Users/dme26/tmp/cosc349-testing => /vagrant
```

After the `vagrant up` command completes you are returned to your shell, and nothing obvious seems to have happened. That's because the VirtualBox VM is "headless": it is running on your computer, but it has no (emulated) monitor attached to it, so you see no obvious evidence that it is running.

If you run the VirtualBox command to list running VMs, you should see the above-mentioned VM in your list:

```
$ VBoxManage list runningvms
"cosc349-testing_default_1784375450199_8965" {fda4f76a-b0ed-427b-aa54-a8cb9ad1eb1b}
```
Note also that if you start the main VirtualBox application, you will also see Vagrant's VMs displayed there. 

Alternatively you can ask Vagrant about the status of the VM associated with the current directory (which contains a `Vagrantfile`, so Vagrant can determine the context).

```
$ vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

To interact with a Unix shell on your new VM, run the `vagrant ssh` command. An example interaction is shown, but note that there is now a mix of macOS / Git Bash shell interaction and interaction with your new VM.

```
$ vagrant ssh
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-160-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sat Jul 18 11:54:50 AM UTC 2026

  System load:           0.08
  Usage of /:            15.4% of 30.34GB
  Memory usage:          7%
  Swap usage:            0%
  Processes:             157
  Users logged in:       0
  IPv4 address for eth0: 10.0.2.15
  IPv6 address for eth0: fd17:625c:f037:2:a00:27ff:fe16:ddef


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.

vagrant@vagrant:~$ uname -a
Linux vagrant 5.15.0-160-generic #170-Ubuntu SMP Wed Oct 1 10:06:56 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
vagrant@vagrant:~$ whoami
vagrant
vagrant@vagrant:~$ ls /vagrant
Vagrantfile
vagrant@vagrant:~$ echo "Hello from Ubuntu!" >/vagrant/new-file.txt
vagrant@vagrant:~$ logout
Laptopofdme112  cosc349-testing  →  uname -a
Darwin Laptopofdme112 24.6.0 Darwin Kernel Version 24.6.0: Tue Apr 21 20:17:54 PDT 2026; root:xnu-11417.140.69.710.16~1/RELEASE_X86_64 x86_64
Laptopofdme112  cosc349-testing  →  cat new-file.txt
Hello from Ubuntu!
```

Note that we created `new-file.txt` within the VM, and were able to read it from the host (macOS in my case). This is due to the shared folder that Vagrant gets VirtualBox to create, sharing between the host (macOS / Windows / Linux / etc.) file system, and the guest (Linux) file system.

:::success
:pencil: 
Exercise: change the file on your host and ensure that you can indeed see the changes reflected within your VM.
:::

:::info
:bulb: 
In the days before the Windows Subsystem for Linux (WSL), the above type of Vagrant-powered interaction with Linux systems was a handy way to get access to Unix commands from Windows hosts.
:::

While Vagrant has hopefully already shown itself to be useful, it is typical to manage `Vagrantfiles`—and thus Vagrant environments—within Git repositories. We will explore doing so in the next section.

We are done with our VM for the moment, so let's remove it using the `vagrant destroy` command.

```
$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
```

We will not reuse the working directory that you created, so feel free to delete it now.

## Deploy a Git repository containing an existing `Vagrantfile`

A common development workflow is to place `Vagrantfile`s within Git repositories. The working directory, and thus Git working copy will be accessible both from the host—where you `git clone` it—and also from the guest (VM)—via `/vagrant`. Thus you can use both host and guest tools on the repository.

:::info
:bulb: 
Note that this workflow has been useful for a number of other SoC papers, including COSC345, COSC312, COSC412, etc.
:::

Move to a directory in which you are happy to check out an example Git repository (for example, you may have a `checkouts` directory that contains all your Git working copies, or a `cosc349` directory containing files relevant to the COSC349 labs).

Clone the Git repository at https://altitude.otago.ac.nz/cosc349/lab03-apache and change into the directory that is created. Note the contents of the repository—as below, you can use the `ls -a` command.

```
$ git clone https://altitude.otago.ac.nz/cosc349/lab03-apache cosc349-lab03-apache
Cloning into 'cosc349-lab03-apache'...
warning: redirecting to https://altitude.otago.ac.nz/cosc349/lab03-apache.git/
remote: Enumerating objects: 24, done.
remote: Total 24 (delta 0), reused 0 (delta 0), pack-reused 24
Receiving objects: 100% (24/24), 4.30 KiB | 880.00 KiB/s, done.
Resolving deltas: 100% (7/7), done.
$ cd cosc349-lab03-apache 
$ ls -a
.                 .git              Vagrantfile       www
..                .gitignore        test-website.conf
```

Since a `Vagrantfile` is present, you can create the specified VM using `vagrant up`.

:::success
:pencil: 
Exercise: examine the `Vagrantfile` to see if you can determine where the differences are from the default `Vagrantfile` created by the `vagrant init` command, and the purpose of these changes.
:::

```
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'bento/ubuntu-22.04'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'bento/ubuntu-22.04' version '202510.26.0' is up to date...
==> default: Setting the name of the VM: cosc349-lab03-apache_default_1784376457165_77958
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 80 (guest) => 8080 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /Users/dme26/checkouts/cosc349-lab03-apache => /vagrant
==> default: Detected mount owner ID within mount options. (uid: 1000 guestpath: /vagrant)
==> default: Detected mount group ID within mount options. (gid: 1000 guestpath: /vagrant)
```

Note that at this point, the VM is created, and booted. The output then switches to showing what the provisioning script shell commands are doing, starting with the command that updates the Ubuntu packages.

```
==> default: Running provisioner: shell...
    default: Running: inline script
    default: Get:1 http://security.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
    default: Hit:2 http://us.archive.ubuntu.com/ubuntu jammy InRelease
    default: Get:3 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
    default: Get:4 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [3,361 kB]
    default: Get:5 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
    default: Get:6 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [3,646 kB]
    default: Get:7 http://us.archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [547 kB]
    default: Get:8 http://us.archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [6,245 kB]
    default: Get:9 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [475 kB]
    default: Get:10 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [5,988 kB]
    default: Get:11 http://us.archive.ubuntu.com/ubuntu jammy-updates/restricted Translation-en [1,195 kB]
    default: Get:12 http://us.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1,278 kB]
    default: Get:13 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [1,146 kB]
    default: Get:14 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [1,044 kB]
    default: Get:15 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [233 kB]
    default: Get:16 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [64.3 kB]
    default: Get:17 http://security.ubuntu.com/ubuntu jammy-security/multiverse Translation-en [12.6 kB]
    default: Get:18 http://us.archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [322 kB]
    default: Get:19 http://us.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [71.6 kB]
    default: Get:20 http://us.archive.ubuntu.com/ubuntu jammy-updates/multiverse Translation-en [15.5 kB]
    default: Get:21 http://us.archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [70.2 kB]
    default: Get:22 http://us.archive.ubuntu.com/ubuntu jammy-backports/main Translation-en [11.4 kB]
    default: Get:23 http://us.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [30.8 kB]
    default: Get:24 http://us.archive.ubuntu.com/ubuntu jammy-backports/universe Translation-en [16.9 kB]
    default: Fetched 26.2 MB in 11s (2,449 kB/s)
    default: Reading package lists...
    default: Reading package lists...
    default: Building dependency tree...
    default: Reading state information...
    default: The following additional packages will be installed:
    default:   apache2-bin apache2-data apache2-utils libapr1 libaprutil1
    default:   libaprutil1-dbd-sqlite3 libaprutil1-ldap liblua5.3-0 mailcap mime-support
    default:   ssl-cert
    default: Suggested packages:
    default:   apache2-doc apache2-suexec-pristine | apache2-suexec-custom www-browser
    default: The following NEW packages will be installed:
    default:   apache2 apache2-bin apache2-data apache2-utils libapr1 libaprutil1
    default:   libaprutil1-dbd-sqlite3 libaprutil1-ldap liblua5.3-0 mailcap mime-support
    default:   ssl-cert
    default: 0 upgraded, 12 newly installed, 0 to remove and 162 not upgraded.
    default: Need to get 2,122 kB of archives.
    default: After this operation, 8,450 kB of additional disk space will be used.
    default: Get:1 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libapr1 amd64 1.7.0-8ubuntu0.22.04.2 [108 kB]
    default: Get:2 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libaprutil1 amd64 1.6.1-5ubuntu4.22.04.2 [92.8 kB]
    default: Get:3 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libaprutil1-dbd-sqlite3 amd64 1.6.1-5ubuntu4.22.04.2 [11.3 kB]
    default: Get:4 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libaprutil1-ldap amd64 1.6.1-5ubuntu4.22.04.2 [9,170 B]
    default: Get:5 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 liblua5.3-0 amd64 5.3.6-1build1 [140 kB]
    default: Get:6 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 apache2-bin amd64 2.4.52-1ubuntu4.23 [1,362 kB]
    default: Get:7 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 apache2-data all 2.4.52-1ubuntu4.23 [165 kB]
    default: Get:8 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 apache2-utils amd64 2.4.52-1ubuntu4.23 [90.1 kB]
    default: Get:9 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 mailcap all 3.70+nmu1ubuntu1.22.04.1 [23.9 kB]
    default: Get:10 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 mime-support all 3.66 [3,696 B]
    default: Get:11 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 apache2 amd64 2.4.52-1ubuntu4.23 [97.9 kB]
    default: Get:12 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 ssl-cert all 1.1.2 [17.4 kB]
    default: dpkg-preconfigure: unable to re-open stdin: No such file or directory
    default: Fetched 2,122 kB in 3s (768 kB/s)
    default: Selecting previously unselected package libapr1:amd64.
(Reading database ... 45823 files and directories currently installed.)
    default: Preparing to unpack .../00-libapr1_1.7.0-8ubuntu0.22.04.2_amd64.deb ...
    default: Unpacking libapr1:amd64 (1.7.0-8ubuntu0.22.04.2) ...
    default: Selecting previously unselected package libaprutil1:amd64.
    default: Preparing to unpack .../01-libaprutil1_1.6.1-5ubuntu4.22.04.2_amd64.deb ...
    default: Unpacking libaprutil1:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Selecting previously unselected package libaprutil1-dbd-sqlite3:amd64.
    default: Preparing to unpack .../02-libaprutil1-dbd-sqlite3_1.6.1-5ubuntu4.22.04.2_amd64.deb ...
    default: Unpacking libaprutil1-dbd-sqlite3:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Selecting previously unselected package libaprutil1-ldap:amd64.
    default: Preparing to unpack .../03-libaprutil1-ldap_1.6.1-5ubuntu4.22.04.2_amd64.deb ...
    default: Unpacking libaprutil1-ldap:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Selecting previously unselected package liblua5.3-0:amd64.
    default: Preparing to unpack .../04-liblua5.3-0_5.3.6-1build1_amd64.deb ...
    default: Unpacking liblua5.3-0:amd64 (5.3.6-1build1) ...
    default: Selecting previously unselected package apache2-bin.
    default: Preparing to unpack .../05-apache2-bin_2.4.52-1ubuntu4.23_amd64.deb ...
    default: Unpacking apache2-bin (2.4.52-1ubuntu4.23) ...
    default: Selecting previously unselected package apache2-data.
    default: Preparing to unpack .../06-apache2-data_2.4.52-1ubuntu4.23_all.deb ...
    default: Unpacking apache2-data (2.4.52-1ubuntu4.23) ...
    default: Selecting previously unselected package apache2-utils.
    default: Preparing to unpack .../07-apache2-utils_2.4.52-1ubuntu4.23_amd64.deb ...
    default: Unpacking apache2-utils (2.4.52-1ubuntu4.23) ...
    default: Selecting previously unselected package mailcap.
    default: Preparing to unpack .../08-mailcap_3.70+nmu1ubuntu1.22.04.1_all.deb ...
    default: Unpacking mailcap (3.70+nmu1ubuntu1.22.04.1) ...
    default: Selecting previously unselected package mime-support.
    default: Preparing to unpack .../09-mime-support_3.66_all.deb ...
    default: Unpacking mime-support (3.66) ...
    default: Selecting previously unselected package apache2.
    default: Preparing to unpack .../10-apache2_2.4.52-1ubuntu4.23_amd64.deb ...
    default: Unpacking apache2 (2.4.52-1ubuntu4.23) ...
    default: Selecting previously unselected package ssl-cert.
    default: Preparing to unpack .../11-ssl-cert_1.1.2_all.deb ...
    default: Unpacking ssl-cert (1.1.2) ...
    default: Setting up libapr1:amd64 (1.7.0-8ubuntu0.22.04.2) ...
    default: Setting up ssl-cert (1.1.2) ...
    default: Setting up liblua5.3-0:amd64 (5.3.6-1build1) ...
    default: Setting up apache2-data (2.4.52-1ubuntu4.23) ...
    default: Setting up mailcap (3.70+nmu1ubuntu1.22.04.1) ...
    default: Setting up libaprutil1:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Setting up mime-support (3.66) ...
    default: Setting up libaprutil1-ldap:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Setting up libaprutil1-dbd-sqlite3:amd64 (1.6.1-5ubuntu4.22.04.2) ...
    default: Setting up apache2-utils (2.4.52-1ubuntu4.23) ...
    default: Setting up apache2-bin (2.4.52-1ubuntu4.23) ...
    default: Setting up apache2 (2.4.52-1ubuntu4.23) ...
    default: Enabling module mpm_event.
    default: Enabling module authz_core.
    default: Enabling module authz_host.
    default: Enabling module authn_core.
    default: Enabling module auth_basic.
    default: Enabling module access_compat.
    default: Enabling module authn_file.
    default: Enabling module authz_user.
    default: Enabling module alias.
    default: Enabling module dir.
    default: Enabling module autoindex.
    default: Enabling module env.
    default: Enabling module mime.
    default: Enabling module negotiation.
    default: Enabling module setenvif.
    default: Enabling module filter.
    default: Enabling module deflate.
    default: Enabling module status.
    default: Enabling module reqtimeout.
    default: Enabling conf charset.
    default: Enabling conf localized-error-pages.
    default: Enabling conf other-vhosts-access-log.
    default: Enabling conf security.
    default: Enabling conf serve-cgi-bin.
    default: Enabling site 000-default.
    default: Created symlink /etc/systemd/system/multi-user.target.wants/apache2.service → /lib/systemd/system/apache2.service.
    default: Created symlink /etc/systemd/system/multi-user.target.wants/apache-htcacheclean.service → /lib/systemd/system/apache-htcacheclean.service.
    default: Processing triggers for ufw (0.36.1-4ubuntu0.1) ...
    default: Processing triggers for man-db (2.10.2-1) ...
    default: Processing triggers for libc-bin (2.35-0ubuntu3.11) ...
    default:
    default: Running kernel seems to be up-to-date.
    default:
    default: No services need to be restarted.
    default:
    default: No containers need to be restarted.
    default:
    default: No user sessions are running outdated binaries.
    default:
    default: No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

By now the Apache webserver has been installed and has started. The remaining output comes from the commands that switch the website configuration being used.

```
    default: Enabling site test-website.
    default: To activate the new configuration, you need to run:
    default:   systemctl reload apache2
    default: Site 000-default disabled.
    default: To activate the new configuration, you need to run:
    default:   systemctl reload apache2
```

:::warning
:warning: 
Since 2020 the SoC lab environment seemed to require some extra tweaking: what worked on my SoC lab account didn't work on student accounts for reasons that were not immediately clear to our sysadmins.

Back then I pushed a change to the Git repository to fix the issue. This should mean that your `Vagrantfile` contains a line including "dmode".

Now that the SoC Labs have moved to Microsoft Windows, I can likely remove the lines I added, but have not done so yet...

In any case, if I ever need to make further changes to the repository you can always run `git pull` to update your clone of the repository.
:::

Now open in your web browser http://127.0.0.1:8080/ and you should see a test page. The HTML for this test page is contained within the `www` directory of the Git repository that you cloned. Note that you have not needed to use `vagrant ssh` to set anything up—all the functionality required was set up by the shell provisioner in the `Vagrantfile`.

:::success
:pencil: 
Exercise---update files that your VM's web browser can serve to your website: Update the "???" on that page to something more personal, reloading the web-page to check that you changes have taken effect. Try to update the page using both an editor on your host system, and also using an editor such as `nano` within the VM. Run the `git status` command on your host—the changes that you have made are able to be committed back to the repository (although in this case you do not have direct write access to that Git repository).

Recall that when you first `vagrant ssh` into your VM, you will be in the `/home/vagrant` directory. As [described above](#Synchronised-folders), the files shared between macOS and your VM are instead under the similar-