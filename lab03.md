---
tags: cosc349
---
# COSC349 Lab 3—Cloud Architecture—2022
[Lab 2]: /h71h3B-3Tda2XUyNyzkanA

## Lab 3—Vagrant for automating virtualisation

The computers in Lab E and Lab F can be (re)booted into macOS or Linux. This lab requires you to boot into macOS.

### Lab objectives

1. Understand that VirtualBox operations can be automated using its command line tools (e.g., `VBoxManage`)
2. Learn how to use a software tool named "Vagrant" to automate creation of virtual machines
3. Configure provisioning of virtual machines using Vagrant, i.e., running Unix shell commands on the virtual machine after it is created, to get it ready for its intended use.

## VirtualBox has a command line interface

In the [previous lab exercise][Lab 2] we controlled VirtualBox using its GUI. However VirtualBox has an extremely rich command line interface, too.

You can explore the [VirtualBox documentation][VBoxManage documentation] for more details, but some exploratory examples are shown as follows—although note that the real output has been filtered to make it more readable.

- List the running VMs (none, at the time asked)
  ```
  $ VBoxManage list runningvms
  ```
- List the VMs defined, whether or not they are currently running. The right-hand column contains unique identifiers for each VM instance (yours should be different from mine).
  ```
  $ VBoxManage list vms
  "UDA2 net boot" {c0877216-a80b-4c75-9e5d-9956a646d06d}
  "ReactOS test" {77fb8dcb-82ae-4870-9e6b-e56f19377f6b}
  ```
- Provide information about the host computer (my laptop, in this case)
  ```
  $ VBoxManage list hostinfo
  Host Information:
  
    Host Information:
  
  Host time: 2022-07-24T07:51:36.172000000Z
  Processor online count: 8
  Processor count: 8
  Processor online core count: 4
  Processor core count: 4
  Processor supports HW virtualization: yes
  Processor supports PAE: yes
  Processor supports long mode: yes
  Processor supports nested paging: yes
  Processor supports unrestricted guest: yes
  Processor supports nested HW virtualization: no
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
  Memory available: 4929 MByte
  Operating system: Darwin
  Operating system version: 19.6.0  
  ```
- List the NAT Networks (recall that you created such a network in order to interact with your VM back in [Lab 2]).
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

## Motivation for the Vagrant tool

[Vagrant] is a developer tool that allows you to conveniently interact with "headless" VMs, i.e., VMs that do not rely on you needing to see anything displayed on the "monitor" connected to their "graphics card". (It actually does far more than just support interacting with headless VMs, but we'll get on to that later...)

Vagrant does not provide a virtualisation system itself, it instead uses an existing virtualisation system (or will explain that it can't run because it can't find a virtualisation system to use). The most common setup has Vagrant controlling headless VirtualBox VMs. Some of Vagrant's more common use patterns appear as a convenience layer over the VirtualBox command line interface.

## Ensure that Vagrant is working ##

- Check that Vagrant is installed. You can do so by running the following command from a terminal (don't worry that your version may not match mine here):

```bash
$ vagrant --version
Vagrant 2.2.7
```

:::info
:eyes: 
Vagrant and VirtualBox can sometimes fail to work together depending on their specific versions. This will usually be documented online.

The exercises are tested on CS lab computers to try to ensure that you don't run into version problems. Sometimes there may be minor differences in the output shown in the lab exercises from the versions of software that you are using, however.
:::

- If you run into problems within the CS labs and have a computer that you can administer yourself, you can [install Vagrant][Vagrant] on it, and attain a newer version of the software.

[Vagrant]: https://www.vagrantup.com

## Set up from scratch a `Vagrantfile`, i.e., a Vagrant environment

In a command shell, create a new directory in which to store your Vagrant environment, and change into that new directory.

Now run the `vagrant init` command. Vagrant has been written to try to provide useful feedback, so please do read it, and indeed it has indicated what the `vagrant init` command actually did. (Of course the notice abut there being a new Vagrant version will probably be different in your environment.)

:::info
:eyes: 
At some times when using Vagrant I have needed to run `vagrant plugin repair`... but I was instructed to do so by Vagrant...
:::

```
$ vagrant init
==> vagrant: A new version of Vagrant is available: 2.2.17 (installed version: 2.2.7)!
==> vagrant: To upgrade visit: https://www.vagrantup.com/downloads.html

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

—and indeed we see no change other than the creation of the Vagrantfile.

### The purpose of a `Vagrantfile`

A `Vagrantfile` is intended to sit at the top-level directory of a project that software developers, such as yourselves, are working on. It seems likely that the name is analogous to a `Makefile`, which also (often) sits at the top-level of a project, and specifies how to build the software that makes up that project.

A very common and convenient working model is that the `Vagrantfile` is contained in the top-level of a Git repository. It is likely that this way of working will be useful for your first COSC349 assignment.

The context of any `vagrant` command invocation is determined by Vagrant searching the current working directory and each parent directory in turn until it first finds a `Vagrantfile`. 

The `Vagrantfile` specifies the configuration, initial (virtual) hard-disk content (a so-called Vagrant "box"), and scripting to set up one or more VMs that are relevant to a project. Because `vagrant` command invocations determine their context from the current working directory (or its parent directories, until a `Vagrantfile` is found), you can easily check out two copies of a project into different directories, e.g., a stable version and an unstable one that you are working on, and the same `vagrant` command invocation will refer to independent instances of the VMs for that project.

## A tour of Vagrant's features via the default `Vagrantfile`

We will explore a number of Vagrant's key features and concepts by working through the default `Vagrantfile`.

Although you can often treat a `Vagrantfile` just as a static configuration file, it's in fact valid code in the [Ruby] programming language. This can be used to positive effect (e.g., easy ability to use expressions, conditionals and loops in your configurations), although there is a risk that complex code in your `Vagrantfile` may make it very difficult for others to understand. (On one occasion I created a `Vagrantfile` so complex in its use of Ruby features that I subsequently found it difficult to understand how it worked—so try not to do that!)

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
- box files—certainly the official ones—are likely to have been carefully cut down in size, just to contain what's necessary to run a Vagrant VM. For example, the Vagrant box for Ubuntu 20.04 leaves out the parts of the distribution that support graphics, as Vagrant VMs are intended only to be interacted with using a command line.
:::info
:bulb: 
Note that Vagrant boxes not supporting graphical output devices is a pragmatic limitation rather than a technical one: it is usually technically possible to start with a box that doesn't support graphics and then install all the files necessary to support graphics... but to do so would feel (pragmatically) like using the wrong tool for the job... (e.g., perhaps just use VirtualBox's GUI?)
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

Another important feature that Vagrant expects to configure for your VMs is network port forwarding. In general VMs will be configured with a network that uses NAT (see [Lab 2]), since this provides an easy way for the VMs to be able to pull data from the internet.

However in NAT modes of networking, port forwarding is required to allow the host machine (or computers elsewhere on the Internet) to connect to servers running on the VM. We will explore this further, below.

Note that all of the suggested parameters are actually commented out, here.

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
Note that in the CS labs, you should not create `public_networks`. This is for the same reasons given in the [Lab 2] regarding not creating VirtualBox "bridged" network adapters.
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

```

### Provider-specific parameters

As mentioned above, Vagrant doesn't provide a virtualisation engine itself. It instead uses what it terms to be a "provider". The default provider is VirtualBox.

Vagrant is intended to allow you to change provider and still do useful work for you, e.g., you should be able to set up your environment, and then change the provider from VirtualBox to VMware, or change to a provider that deploys directly to cloud services.

Nonetheless, it can be useful to include configuration parameters that are specific for a given virtualisation provider. The VirtualBox-specific parameters shown here allow you to turn on display of the "monitor" of the VirtualBox VM, and/or to change the memory allocated (although in this case you would probably first need to first look up in the Vagrant documentation what units that "1024" number is using!).

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
As a specific example of a provisioning system, CFEngine is used in the CS labs to install software, and generally check that the machines are running as expected. Evidence of this can sometimes be seen when you open shell windows on these computers.
:::

The simplest provisioning approach is to run a shell script as soon as the VM has been created and has booted. The commented-out lines of shell script here (67 and 68) would install the Apache Web Server (version 2) on a Linux system that uses the `apt` package management system, such as Debian or Ubuntu.

The point of provisioning is that you automate steps you'd otherwise have to repeat each time you set up a new instance of a particular design of VM you want to use. A particular point of use is during collaborative development: instead of providing written instructions to your teammates as to how to set up a matching VM environment to yours, you can automate all the setup within the provisioning sections.

A good way of checking that your provisioner is functioning as intended and that your scripting is complete is to create another working directory, `git clone` (or otherwise copy over) the content of your repository, and try deploying fresh VMs. The behaviour of the fresh VMs and those associated with your working copy should match.

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

In your `Vagrantfile` change the box specified from ``"base"`` to ``"ubuntu/focal64"``. This is on line 15 of the Vagrantfile shown here, but your line number may differ, depending on software versions.

You should now be able to have Vagrant set up a Ubuntu Focal 64-bit VM for you. Run the `vagrant up` command. An example interaction is shown, below.

:::warning
:warning: 
The first time you use a Vagrant box, it needs to be downloaded (it's about 270 megabytes), and stored locally. After that time, it will not be downloaded again: any other "ubuntu/focal64" VMs that you request will reuse the box file that has already been cached.
:::

:::info
:eyes: 
Back in 2019 the CS Lab environment has Vagrant installed in a way where a non-functional libvirtio "provider" jumped ahead of the expected VirtualBox provider in priority. You can override this behaviour by adding the `--provider` switch, as in `--provider virtualbox`. However, since 2020 the CS labs appear to work without this option. On my laptop, and on installations of Vagrant that you do on your own computers, it is likely that `vagrant up` would be sufficient, without `--provider` switch.
:::

```
$ vagrant up --provider virtualbox
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'ubuntu/focal64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'ubuntu/focal64'
    default: URL: https://vagrantcloud.com/ubuntu/focal64
==> default: Adding box 'ubuntu/focal64' (20220724.0.0) for provider: virtualbox
    default: Downloading: https://vagrantcloud.com/ubuntu/boxes/focal64/versions/20220724.0.0/providers/virtualbox.box
    default: Download redirected to host: cloud-images.ubuntu.com
==> default: Successfully added box 'ubuntu/focal64' (20220724.0.0) for 'virtualbox'!
```

At this point the Vagrant box for `ubuntu/focal64` has been cached, so the output below relates to creating your specific VM. Hopefully you can get the general gist of what is being done from the output below. You do not need to understand all of the output that is produced in order to use the VM you've requested.

:::info
:eyes: 
Some of the output shown here will not match exactly what you see, but the general shape should match.
:::

```
==> default: Importing base box 'ubuntu/focal64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/focal64' version '20220724.0.0' is up to date...
==> default: Setting the name of the VM: tmp_default_1658823911607_73652
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default: Warning: Remote connection disconnect. Retrying...
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
    default: /vagrant => /Users/dme26/tmp/tmp
```

After the `vagrant up` command completes you are returned to your shell, and nothing obvious seems to have happened. That's because the VirtualBox VM is "headless": it is running on your computer, but it has no (emulated) monitor attached to it, so you see no obvious evidence that it is running.

If you run the VirtualBox command to list running VMs, you should see the above-mentioned VM in your list:

```
$ VBoxManage list runningvms
"tmp_default_1658823911607_73652" {ebfac0e9-419e-4402-8539-bd0cc398e967}
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

To interact with a Unix shell on your new VM, run the `vagrant ssh` command. An example interaction is shown, but note that there is now a mix of macOS shell interaction and interaction with your new VM.

```
$ vagrant ssh
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-122-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jul 26 08:28:26 UTC 2022

  System load:  0.11              Processes:               120
  Usage of /:   3.5% of 38.70GB   Users logged in:         0
  Memory usage: 20%               IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%


1 update can be applied immediately.
To see these additional updates run: apt list --upgradable


vagrant@ubuntu-focal:~$ uname -a
Linux ubuntu-focal 5.4.0-122-generic #138-Ubuntu SMP Wed Jun 22 15:00:31 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
vagrant@ubuntu-focal:~$ whoami
vagrant
vagrant@ubuntu-focal:~$ ls /vagrant
Vagrantfile
vagrant@ubuntu-focal:~$ echo "Hello from Ubuntu!" >/vagrant/new-file.txt
vagrant@ubuntu-focal:~$ logout
Connection to 127.0.0.1 closed.
$ uname -a
Darwin Laptop-of-dme-112.local 19.6.0 Darwin Kernel Version 19.6.0: Tue Jun 21 21:18:39 PDT 2022; root:xnu-6153.141.66~1/RELEASE_X86_64 x86_64
$ cat new-file.txt 
Hello from Ubuntu!
```

Note that we created `new-file.txt` within the VM, and were able to read it from macOS. This is due to the shared folder that Vagrant gets VirtualBox to create, sharing between the host (macOS) file system, and the guest (Linux) file system.

:::success
:pencil: 
Exercise: change the file on your host and ensure that you can indeed see the changes reflected within your VM.
:::

:::info
:bulb: 
In the days before the Windows subsystem for Linux (WSL), the above type of Vagrant-powered interaction with Ubuntu Linux was a handy way to get access to Unix commands from Windows hosts.
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
Note that this workflow has been useful for a number of other CS papers, including COSC345, COSC412 and COSC430.
:::

Move to a directory in which you are happy to check out an example Git repository (for example, you may have a `checkouts` directory that contains all your Git working copies, or a `cosc349` directory containing files relevant to the COSC349 labs).

Clone the Git repository at https://altitude.otago.ac.nz/cosc349/lab03-apache and change into the directory that is created. Note the contents of the repository—as below, you can use the `ls -a` command.

```
$ git clone https://altitude.otago.ac.nz/cosc349/lab03-apache cosc349-lab03-apache
Cloning into 'cosc349-lab03-apache'...
warning: redirecting to https://altitude.otago.ac.nz/cosc349/lab03-apache.git/
remote: Enumerating objects: 18, done.
remote: Counting objects: 100% (18/18), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 18 (delta 3), reused 0 (delta 0)
Unpacking objects: 100% (18/18), done.
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
==> default: Importing base box 'ubuntu/focal64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/focal64' version '20220724.0.0' is up to date...
==> default: Setting the name of the VM: cosc349-lab03-apache_default_1658824357440_22641
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 80 (guest) => 8080 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default: Warning: Remote connection disconnect. Retrying...
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 5.1.38
    default: VirtualBox Version: 6.1
==> default: Mounting shared folders...
    default: /vagrant => /home/cshome/d/dme/checkouts/cosc349-lab03-apache
```

Note that at this point, the VM is created, and booted. The output then switches to showing what the provisioning script shell commands are doing, starting with the command that updates the Ubuntu packages.

```
==> default: Running provisioner: shell...
    default: Running: inline script
    default: Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
    default: Hit:2 http://archive.ubuntu.com/ubuntu focal InRelease
    default: Get:3 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
    default: Get:4 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [711 kB]
    default: Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
    default: Get:6 http://security.ubuntu.com/ubuntu focal-security/universe Translation-en [128 kB]
    default: Get:7 http://security.ubuntu.com/ubuntu focal-security/universe amd64 c-n-f Metadata [14.7 kB]
    default: Get:8 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [22.2 kB]
    default: Get:9 http://security.ubuntu.com/ubuntu focal-security/multiverse Translation-en [5376 B]
    default: Get:10 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 c-n-f Metadata [512 B]
    default: Get:11 http://archive.ubuntu.com/ubuntu focal/universe amd64 Packages [8628 kB]
    default: Get:12 http://archive.ubuntu.com/ubuntu focal/universe Translation-en [5124 kB]
    default: Get:13 http://archive.ubuntu.com/ubuntu focal/universe amd64 c-n-f Metadata [265 kB]
    default: Get:14 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [144 kB]
    default: Get:15 http://archive.ubuntu.com/ubuntu focal/multiverse Translation-en [104 kB]
    default: Get:16 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 c-n-f Metadata [9136 B]
    default: Get:17 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [1990 kB]
    default: Get:18 http://archive.ubuntu.com/ubuntu focal-updates/main Translation-en [358 kB]
    default: Get:19 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [924 kB]
    default: Get:20 http://archive.ubuntu.com/ubuntu focal-updates/universe Translation-en [208 kB]
    default: Get:21 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 c-n-f Metadata [20.9 kB]
    default: Get:22 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [24.4 kB]
    default: Get:23 http://archive.ubuntu.com/ubuntu focal-updates/multiverse Translation-en [7336 B]
    default: Get:24 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 c-n-f Metadata [592 B]
    default: Get:25 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages [44.8 kB]
    default: Get:26 http://archive.ubuntu.com/ubuntu focal-backports/main Translation-en [11.3 kB]
    default: Get:27 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 c-n-f Metadata [976 B]
    default: Get:28 http://archive.ubuntu.com/ubuntu focal-backports/restricted amd64 c-n-f Metadata [116 B]
    default: Get:29 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [23.7 kB]
    default: Get:30 http://archive.ubuntu.com/ubuntu focal-backports/universe Translation-en [15.9 kB]
    default: Get:31 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 c-n-f Metadata [860 B]
    default: Get:32 http://archive.ubuntu.com/ubuntu focal-backports/multiverse amd64 c-n-f Metadata [116 B]
    default: Fetched 19.1 MB in 10s (1894 kB/s)
    default: Reading package lists...
    default: Reading package lists...
    default: Building dependency tree...
    default:
    default: Reading state information...
    default: The following additional packages will be installed:
    default:   apache2-bin apache2-data apache2-utils libapr1 libaprutil1
    default:   libaprutil1-dbd-sqlite3 libaprutil1-ldap libjansson4 liblua5.2-0 ssl-cert
    default: Suggested packages:
    default:   apache2-doc apache2-suexec-pristine | apache2-suexec-custom www-browser
    default:   openssl-blacklist
    default: The following NEW packages will be installed:
    default:   apache2 apache2-bin apache2-data apache2-utils libapr1 libaprutil1
    default:   libaprutil1-dbd-sqlite3 libaprutil1-ldap libjansson4 liblua5.2-0 ssl-cert
    default: 0 upgraded, 11 newly installed, 0 to remove and 1 not upgraded.
    default: Need to get 1867 kB of archives.
    default: After this operation, 8095 kB of additional disk space will be used.
    default: Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 libapr1 amd64 1.6.5-1ubuntu1 [91.4 kB]
    default: Get:2 http://archive.ubuntu.com/ubuntu focal/main amd64 libaprutil1 amd64 1.6.1-4ubuntu2 [84.7 kB]
    default: Get:3 http://archive.ubuntu.com/ubuntu focal/main amd64 libaprutil1-dbd-sqlite3 amd64 1.6.1-4ubuntu2 [10.5 kB]
    default: Get:4 http://archive.ubuntu.com/ubuntu focal/main amd64 libaprutil1-ldap amd64 1.6.1-4ubuntu2 [8736 B]
    default: Get:5 http://archive.ubuntu.com/ubuntu focal/main amd64 libjansson4 amd64 2.12-1build1 [28.9 kB]
    default: Get:6 http://archive.ubuntu.com/ubuntu focal/main amd64 liblua5.2-0 amd64 5.2.4-1.1build3 [106 kB]
    default: Get:7 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 apache2-bin amd64 2.4.41-4ubuntu3.12 [1181 kB]
    default: Get:8 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 apache2-data all 2.4.41-4ubuntu3.12 [159 kB]
    default: Get:9 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 apache2-utils amd64 2.4.41-4ubuntu3.12 [84.5 kB]
    default: Get:10 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 apache2 amd64 2.4.41-4ubuntu3.12 [95.6 kB]
    default: Get:11 http://archive.ubuntu.com/ubuntu focal/main amd64 ssl-cert all 1.0.39 [17.0 kB]
    default: dpkg-preconfigure: unable to re-open stdin: No such file or directory
    default: Fetched 1867 kB in 3s (600 kB/s)
    default: Selecting previously unselected package libapr1:amd64.
    default: (Reading database ...
(Reading database ... 55%abase ... 5%
    default: (Reading database ... 60%
    default: (Reading database ... 65%
    default: (Reading database ... 70%
    default: (Reading database ... 75%
    default: (Reading database ... 80%
    default: (Reading database ... 85%
    default: (Reading database ... 90%
    default: (Reading database ... 95%
(Reading database ... 63485 files and directories currently installed.)
    default: Preparing to unpack .../00-libapr1_1.6.5-1ubuntu1_amd64.deb ...
    default: Unpacking libapr1:amd64 (1.6.5-1ubuntu1) ...
    default: Selecting previously unselected package libaprutil1:amd64.
    default: Preparing to unpack .../01-libaprutil1_1.6.1-4ubuntu2_amd64.deb ...
    default: Unpacking libaprutil1:amd64 (1.6.1-4ubuntu2) ...
    default: Selecting previously unselected package libaprutil1-dbd-sqlite3:amd64.
    default: Preparing to unpack .../02-libaprutil1-dbd-sqlite3_1.6.1-4ubuntu2_amd64.deb ...
    default: Unpacking libaprutil1-dbd-sqlite3:amd64 (1.6.1-4ubuntu2) ...
    default: Selecting previously unselected package libaprutil1-ldap:amd64.
    default: Preparing to unpack .../03-libaprutil1-ldap_1.6.1-4ubuntu2_amd64.deb ...
    default: Unpacking libaprutil1-ldap:amd64 (1.6.1-4ubuntu2) ...
    default: Selecting previously unselected package libjansson4:amd64.
    default: Preparing to unpack .../04-libjansson4_2.12-1build1_amd64.deb ...
    default: Unpacking libjansson4:amd64 (2.12-1build1) ...
    default: Selecting previously unselected package liblua5.2-0:amd64.
    default: Preparing to unpack .../05-liblua5.2-0_5.2.4-1.1build3_amd64.deb ...
    default: Unpacking liblua5.2-0:amd64 (5.2.4-1.1build3) ...
    default: Selecting previously unselected package apache2-bin.
    default: Preparing to unpack .../06-apache2-bin_2.4.41-4ubuntu3.12_amd64.deb ...
    default: Unpacking apache2-bin (2.4.41-4ubuntu3.12) ...
    default: Selecting previously unselected package apache2-data.
    default: Preparing to unpack .../07-apache2-data_2.4.41-4ubuntu3.12_all.deb ...
    default: Unpacking apache2-data (2.4.41-4ubuntu3.12) ...
    default: Selecting previously unselected package apache2-utils.
    default: Preparing to unpack .../08-apache2-utils_2.4.41-4ubuntu3.12_amd64.deb ...
    default: Unpacking apache2-utils (2.4.41-4ubuntu3.12) ...
    default: Selecting previously unselected package apache2.
    default: Preparing to unpack .../09-apache2_2.4.41-4ubuntu3.12_amd64.deb ...
    default: Unpacking apache2 (2.4.41-4ubuntu3.12) ...
    default: Selecting previously unselected package ssl-cert.
    default: Preparing to unpack .../10-ssl-cert_1.0.39_all.deb ...
    default: Unpacking ssl-cert (1.0.39) ...
    default: Setting up libapr1:amd64 (1.6.5-1ubuntu1) ...
    default: Setting up libjansson4:amd64 (2.12-1build1) ...
    default: Setting up ssl-cert (1.0.39) ...
    default: Setting up liblua5.2-0:amd64 (5.2.4-1.1build3) ...
    default: Setting up apache2-data (2.4.41-4ubuntu3.12) ...
    default: Setting up libaprutil1:amd64 (1.6.1-4ubuntu2) ...
    default: Setting up libaprutil1-ldap:amd64 (1.6.1-4ubuntu2) ...
    default: Setting up libaprutil1-dbd-sqlite3:amd64 (1.6.1-4ubuntu2) ...
    default: Setting up apache2-utils (2.4.41-4ubuntu3.12) ...
    default: Setting up apache2-bin (2.4.41-4ubuntu3.12) ...
    default: Setting up apache2 (2.4.41-4ubuntu3.12) ...
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
    default: Processing triggers for ufw (0.36-6ubuntu1) ...
    default: Processing triggers for systemd (245.4-4ubuntu3.17) ...
    default: Processing triggers for man-db (2.9.1-1) ...
    default: Processing triggers for libc-bin (2.31-0ubuntu9.9) ...
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
Since 2020 the CS lab environment seemed to require some extra tweaking: what worked on my CS lab account didn't work on student accounts for reasons that were not immediately clear to our sysadmins.

Back then I pushed a change to the Git repository to fix the issue. This should mean that your `Vagrantfile` contains a line including "dmode".

If I ever need to make further changes to the repository you can always run `git pull` to update your clone of the repository.
:::

Now open in your web browser http://127.0.0.1:8080/ and you should see a test page. The HTML for this test page is contained within the `www` directory of the Git repository that you cloned. Note that you have not needed to use `vagrant ssh` to set anything up—all the functionality required was set up by the shell provisioner in the `Vagrantfile`.

:::success
:pencil: 
Exercise---update files that your VM's web browser can serve to your website: Update the "???" on that page to something more personal, reloading the web-page to check that you changes have taken effect. Try to update the page using both an editor on your host system, and also using an editor such as `nano` within the VM. Run the `git status` command on your host—the changes that you have made are able to be committed back to the repository (although in this case you do not have direct write access to that Git repository).

Recall that when you first `vagrant ssh` into your VM, you will be in the `/home/vagrant` directory. As [described above](#Synchronised-folders), the files shared between macOS and your VM are instead under the similar-looking but entirely different `/vagrant` directory.
:::

:::success
:pencil: 
Exercise---test some shell commands on your VM and then "bake" these commands into your provisioning: Modify the provisioning shell script in your `Vagrantfile` to fetch some content from the Internet *at the time that the VM is deployed*, and change a local web page to reflect this content. 

For example, you can use a shell command like `date > my-file.txt` which will execute the `date` command and write the output to the file `my-file.txt`. Or a more complex operation is to use `wget` to retrieve a file from the web, for example `wget 'https://www.otago.ac.nz/_assets/_gfx/logo@2x.png'` will download a file `logo@2x.png` of the Otago crest into the working directory at the time you ran `wget`.

(By default when you view http://127.0.0.1:8080/ in your macOS web browser, your VM will retrieve the `index.html` file from `/vagrant/www`. You can add a filename to the end of the URL, e.g., http://127.0.0.1:8080/my-file.txt will retrieve `/vagrant/www/my-file.txt` and show it as plain text in your web browser.)

One approach to tweaking a provisioning script is to manually run commands from a terminal on the VM (via `vagrant ssh`) to get to the point you want to reach, and then to factor those commands into the provisioning script within the `Vagrantfile`, as described in the following subsection. You are likely to need to `vagrant destroy` your machine and then `vagrant up` it again in order to test your provisioning script fully.
:::

:::success
:pencil: 
Exercise: Create your own Git repository, pushed to a cloud-based Git repository server, that contains a Vagrant file that contains a shell provisioning step that you have configured. Try to clone your Git repository into a new working directory, and test that you can still `vagrant up` and that `vagrant ssh` reaches a different VM from the VM `vagrant ssh` reaches when run from the Git working directory from which you `git push`ed your repository.
:::

## Cleaning up

### Cleaning up running Vagrant VMs

The command `vagrant global-status` does not need to be run in a directory "below" or "at" the level of a `Vagrantfile`. It will list all of the VMs that Vagrant knows is still running. You can change into the directory listed in the command's output and execute `vagrant destroy` if you want to remove the VM. It is also possible to destroy named VMs---look up the details in the [Vagrant documentation](https://www.vagrantup.com/docs/).

### Cleaning up box files

As noted above, Vagrant will cache box files. If you need to reclaim storage space, you can run subcommands of `vagrant box` to list and remove cached box files, as [described in the Vagrant documentation](https://www.vagrantup.com/docs/cli/box.html)

# Vagrant beyond lab 3...

## Multiple VMs within one Vagrantfile

An useful capability of Vagrant is to be able to manage multiple VMs within a single `Vagrantfile`. This type of use of Vagrant is likely to be useful for, say, COSC349 assignment work, but I intend to document it on a different page from these lab 3 exercises.

In the meantime, though, you can interact with the repository that I have set up https://altitude.otago.ac.nz/cosc349/vagrant-multivm that shows a webserver and a database server interacting.

:::warning
:warning: 
When using this repository in the CS Labs, the `synced_folder` line in the `Vagrantfile` needs to set default permissions for files and directories (`fmode` and `dmode`). The repository has been updated to contain this option, since it seems not to break non-CS Lab environments (even if it is not necessary for them).
:::

## Advice on provisioning scripts

It may be difficult to convert your provisioning steps into a scripted version in one transition. Instead you can try to move your manual shell commands that set up your VM as desired into the provisioning script progressively.

It is very important to check that you have not broken the functionality of automatic provisioning if you modify your scripts at all. However leaving your computer to re-run a clean checkout of the containing repository and building of your Vagrant VM should not require your attention before provisioning is completed. 

After a development session I typically `git clone` and `vagrant up` in a terminal window that I hide while my attention is elsewhere. I periodically check back to see whether the Vagrant provisioning is complete, and then do any application testing required to be sure that I have a clean build process that creates a working application (e.g., that I haven't just broken something).