---
title: COSC349 Lab 3—Cloud Architecture—2026
tags: [cosc349, lab]

---

## Preliminaries for Lab 3

:::warning
:warning: 
Since I (Dave) already have an AWS Academy account, I was not able to see precisely what you see through all steps entering your AWS Academy Learner Lab. I believe that you end up seeing the same AWS Console that I do, but with some key differences:
- I can potentially log in as "you" to your AWS Console, but only in the context of your AWS Academy Learner Lab. In practice I only expect to do this for three main potential reasons (1) you contact members of the teaching team asking for support, (2) the teaching team needs access to material for marking, or (3) some other problem arises and I need to perform action;
- You don't need a registered credit card;
- You do not need to pay Amazon for the initial credit allocated to you;
- Within the context of this AWS Academy Learner Lab, your AWS credit and all AWS resources you use will vanish when the Learner Lab closes (which will happen in the exam period after semester).

Of course please do let me know if you see material significantly different from the screen-shots and descriptions that I provide below!
:::


### Activating your AWS Academy account

Using AWS costs money, although Amazon are also very keen that potential future clients of their cloud services (i.e., you!) are not turned away by the cost. Of the many ways Amazon tries to help:
1. Amazon provide a generous "free tier", that allows you to try out many of their services without cost. Sometimes the free tier is refreshed annually, so that small-scale use of Amazon services actually end up being free, in effect. However using the free tier requires having an Amazon account, which in turn requires registering a credit card, even if it doesn't end up getting charged.
2. Amazon provide generous educational access, such as the AWS Academy, and the Learner Lab within the AWS Academy that we will be trying to use.

:::info
:bulb: 
Although we have discussed Amazon Web Services here, most of the major public cloud providers provide some sort of equivalent free tier, or academic / educational / student / hobbyist access. Feel free to collect them all—I would be interested to hear about your experiences comparing their offerings.
:::

You should have received, to your student email address, an email that instructs you how to join AWS Academy and the Learner Lab we will use in COSC349.

## Lab 3—AWS IaaS with EC2

You should be able to complete this lab using any operating system, e.g., macOS, Linux, or Windows, including the lab computers within the Owheo Building. (The instructions have been developed on macOS and updated on Linux systems, though.)

:::info
:bulb: 
On the Microsoft Windows lab computers within the Owheo Building, I would recommend trying Git Bash as your shell. There are certainly ways to use PowerShell and `cmd.exe`, but where Git Bash works, all three major operating system types can follow the same instructions.
:::

In this lab we will introduce use of Amazon's Elastic Compute Cloud service (EC2), which provides (among other features) Infrastructure as a Service—i.e., the provision of (headless) virtual machines of the type you have experimented with using Vagrant in Lab 2.

Note that, consistent with the overall goals of COSC349, a primary aim is for you to see how the pieces of software infrastructure piece together. There are many excellent ways to deploy applications to AWS with a single mouse-click. You are very welcome to experiment with these, and provided that you keep an eye on your Amazon expenditure, there should be plenty of credit within your Amazon Academy allowance to facilitate you exploring the high-convenience Amazon offerings as well as the more technically detailed ones.

### Lab objectives

1. Ensure you can access Amazon Web Services using your AWS Academy account or otherwise.
1. Learn how to configure and launch virtual machines in the Amazon Elastic Compute Cloud (EC2).
1. Interact with your EC2 VMs as a user—e.g., view web sites produced by your VM.
1. Interact with your EC2 VMs as a developer, using the secure shell (SSH).
1. Understand and practice good "cloud hygiene", such as always checking that you have shut down unused but resource (and credit) consuming VMs.

:::info
:exclamation: 
To get the most out of the lab exercises, it is likely that you should take notes about the concepts you are least clear about. That way you can easily do further reading to acquire the information that you need, or can ask the COSC349 teaching team members for help or explanations.
:::

## Let's create a VM on AWS

Infrastructure as a Service VMs on Amazon are managed under the Amazon's Elastic Compute Cloud service—known as EC2.

Interactive control of AWS resources can usually be achieved through use of the AWS Management Console, which is accessed through the web using a web browser.

Normally you would log into the AWS Management Console directly, but for the AWS Academy Learner Labs, we need to use a less direct route. First, you log into AWS Academy, to access the learning management system they use (Canvas, I think). A module within the learning management system contains the controls that allow you to activate and deactivate your AWS Academy Learner Lab, and to access to many useful functions including: (1) opening a new browser tab to access AWS Management Console, and (2) to allow you to retrieve AWS credentials that you can use for command line tools (we'll get to that later).

:::info
:confused: 
AWS are currently still the world's leading public cloud provider (at least for IaaS). You may then wonder why some of the AWS Academy functionality (like the AWS Educate system before it) feels technically just a bit like it is probably not world leading. The reason for this apparent inconsistency is that AWS outsource their educational programmes to be largely run and implemented by a third party: Vocareum. In past COSC349 instances, all technical wrinkles have been able to be worked around or ironed out.
:::

### Log into AWS Academy

As noted above, to gain access to the AWS Management Console you first need to log into [AWS Academy](http://awsacademy.instructure.com) or more specifically the [COSC349 course within AWS Academy](https://awsacademy.instructure.com//courses/177746) ... but actually you may be able to [jump straight to the Learner Lab controls](https://awsacademy.instructure.com/courses/177746/modules/items/17458680) with the COSC349 AWS Academy course. The course is helpfully named "AWS Academy Learner Lab".

### Reach the Learner Lab

:::warning
:bomb: 
On macOS it seems that Safari does not work to reach the Learner Lab page. This year I'm updating the notes from an old iMac running Linux, so updated screenshots are from Firefox.
:::

After I had successfully logged into AWS Academy and assumed the persona of "student view", I was able to reach a page such as shown in the following screen capture:

![image](https://hackmd.io/_uploads/ByJSSWGHMg.png)

From which I clicked "Start Lab" in the controls above the shell window. The AWS control should change from red to yellow... and then eventually to green (although this can at worst take minutes, or at least, it did for me):

![image](https://hackmd.io/_uploads/SyNEIWGSzl.png)

### AWS credentials for CLI tools

At first we will access cloud resources using a web browser, but the Learner Lab page can provide you with the details that you need to give to command line tools (CLI) in order for those tools to be able to access AWS using your AWS Academy Learner Lab identity.

:::warning
:warning: Note that the AWS credentials roll over every few hours. So if your API access suddenly breaks, this may be what has happened.
:::

You can click on the "AWS Details" button near "Start Lab", and the panel to the rght of the shell will show details including "AWS CLI", which has a large "Show" button to its right.
![image](https://hackmd.io/_uploads/H1m2LZGrGe.png)

Click "show" to find out what should go into, for example, AWS credentials files used by the `aws` command line tool. This tool should be installed on the Owheo lab computers. The credentials are typically pasted into `~/.aws/credentials` on macOS and Linux machines, or `.aws\credentials` on Microsoft Windows, under your home directory. The fields you need to paste are `aws_access_key_id`, `aws_secret_access_key`, and `aws_session_token`).

### AWS Management Console

When your AWS indicator to the left of "Start Lab" is green, click on it to open another browser tab that will show you the AWS Management Console, logged in as your AWS Academy Learner Lab identity. I was greeted with a page that looked like: (note that you can scroll down a long way)

![image](https://hackmd.io/_uploads/SyECP-frfe.png)

This is the AWS Management Console for the North Virginia region. AWS Academy Learner Labs run in a particular AWS region, so you will not be able to change this. For unrestricted AWS accounts, you can change regions using the drop-down at the top-right of the window. However note that most resources have different prices in the different regions: the US East Coast regions are usually the cheapest.

One of the first points to note is that AWS provides a search-engine-style search box just to navigate within the services on offer. That should give you some idea of how many different types of services are available! (i.e., many hundreds!)

### Launch a virtual machine with EC2

At first, let's use the EC2 wizard to launch a VM. Although I have no services within the "Recently visited" area of the page, it already had a link to EC2, so I clicked on that, and reached the EC2 page.

![image](https://hackmd.io/_uploads/B1giuWGHze.png)

From there I clicked on "Launch instance".

![image](https://hackmd.io/_uploads/rJi1tWzSzl.png)

First fill in a name, I used "My test VM" (very creative, I know). Scroll down to the section entitled "Application and OS Images (Amazon Machine Image)".

Amazon Machine Images (AMIs) are akin to Vagrant "box" files or VirtualBox virtual hard disks. They are the starting point of the persistent storage of the machine.

The default choice of Amazon Linux is just fine, for our test. (More specifically this default for me reached the AMI with ID `ami-004f790b835b26145`, which is "Amazon Linux 2023 2023.12.20260724.0 x86_64 HVM kernel-6.18".)

![image](https://hackmd.io/_uploads/BJfWcWfBGl.png)

Scroll down and note that the instance type is `t3.micro` which is one of the cheapest types of VMs: e.g., about 1.04 US cents per hour. (Most of your work in COSC349 should work fine on `t3.micro` instances.)

![image](https://hackmd.io/_uploads/SkzH9bGrMl.png)

Next scroll to the "Key pair (login)" section of the page (visible in the previous screen capture). AWS typically does not offer a way to create password-based access, e.g., to SSH to your EC2 VMs. Instead public key authentication is used, where you receive a private key in a file, and Amazon (and anyone else) can view the public key.

If you are first accessing AWS, you will need to use the option to the right to "Create new key pair". (If you have already registered key pairs, you can select them from the drop-down box.)

![image](https://hackmd.io/_uploads/SJKdjZfHGg.png)

I recommend using a name without spaces, such as `cosc349-2024`. ED25519 is more efficient and probably more secure than RSA, but either should work. Most variants of SSH clients should work fine with the `.pem` format---the default (however if you are using the PuTTY tool on Microsoft Windows specifically, you might prefer the `.ppk` format).

Finally, select "Create key pair", and note that your selected type of file (I chose `.pem` will be downloaded by your web browser). This is your private key (file) for the named key pair.

Click "Download Key Pair", and move the private key file to somewhere safe, and make the file private. On a macOS / Linux machines / Git Bash on Owheo Building Lab Windows computers, a good place to move it to is within the `.ssh` subdirectory of your home directory. This is a conventional place to store encryption keys used for SSH (and the default place that must SSH software looks for keys).

:::warning
:warning:
The `~` in Bash shell commands should expand to your home directory. On the Owheo Building Lab computers in Git Bash, in 2025 `~` expanded to the wrong path for a while, but the system is different from 2026, and is hopefully fixed.
:::

:::warning
:warning: 
Note that it's possible that you do not yet have an `.ssh` directory in your home-directory. You can check—
```
$ ls -ld ~/.ssh
drwx------  34 dme  11306  1088  3 Aug 16:08 /Users/dme/.ssh
```
—and if the `ls` command fails, create the directory and set the permissions appropriately (SSH will refuse to use files from directories that are not sufficiently protected) using the command `mkdir -m 700 ~/.ssh` (the `-m` option sets the access control bits on that directory, just as is done when using the `chmod` command).
:::

You may need to rename the key file that was downloaded by your web-browser to remove the `.txt` extension if there is one. I changed the permissions to make the file only readable by my user using the following shell invocations:

```
$ mv ~/Downloads/cosc349-2026.pem ~/.ssh/
$ chmod 400 ~/.ssh/cosc349-2026.pem 
$ ls -l ~/.ssh/cosc349-2026.pem 
.r-------- 387 dme 25 Jul 22:12  /home/dme/.ssh/cosc349-2026.pem
```

Next, scroll down to the "Network settings" controls.

![image](https://hackmd.io/_uploads/HJxm0WzBzx.png)

The default to create a security group (as noted on the page, a security group is essentially a set of firewall rules), which is fine.

The default to "Allow SSH from ... Anywhere", is fine for our purposes. For production EC2 VMs, best practice would involve narrowing down the set of IP addresses that should be permitted to SSH to the EC2 VM.

Change the "Allow HTTPS ..." and "Allow HTTP ..." traffic options to be ticked (two checks).

Then scroll down to the "Configure storage" section of the page.

![image](https://hackmd.io/_uploads/rJthA-GSMg.png)

The default, to create a single 8 GiB `gp3` disk is fine. As you can determine from the pull-down option, `gp3` is a type of SSD storage that AWS offers. There are other choices, most of which will be more expensive per hour, but will have higher performance.

:::info
:eyes:
Note that the free tier message mentions "EBS", i.e. AWS Elastic Block Storage. EBS volumes are actually accessed over the AWS datacentre network: they are not hard disks directly attached to the physical computer running your EC2 VM. The default settings here will delete your EBS volume when your VM is terminated, but this is not the only way to configure EBS to work. Also, EBS always uses redundant hardware, so no EBS volume will be lost if a single hardware device, such as a hard disk, fails.
:::

You can skip the "Advanced details" part of the page, which is hidden by default, anyway.

Scrolling to the "Summary" section, I see something like:

![image](https://hackmd.io/_uploads/r1kzyfGSMe.png)

If everything looks good, you should select the "Launch Instance" button, to create and start your EC2 VM.

### Success launching your VM

Hopefully you successfully launch your VM, and see output similar to:

![image](https://hackmd.io/_uploads/ry3QJGfHzg.png)

Now return to the EC2 Dashboard (the topmost link on the left menu).

## EC2 Dashboard

You should now be viewing the EC2 dashboard. 

![image](https://hackmd.io/_uploads/Bk2V1GMBzl.png)

I clicked on "Instances (running)" to see a list of EC2 VMs that have been launched (i.e., our test VM).

You can click the checkbox next to your instance to view its details in the information panel in the lower half of the web page. This area includes details such as your public and private IPv4 addresses, and your "Public IPv4 DNS".

![image](https://hackmd.io/_uploads/S1lPJzzSMe.png)

## Connect to your VM using SSH

If you click the "Connect" button (top of page) after choosing your instance (it will be selected by default if there is only one instance), you get a choice of four different ways to open an SSH connection to your instance.

The "EC2 Instance Connect" panel tab allows you to open a JavaScript-driven SSH connection within your web browser. Click "Connect" to open the connection (the button is at the bottom right of the web page).

![image](https://hackmd.io/_uploads/SkF5yGfHfx.png)

In the past this type of AWS EC2 web shell did not work for me, but it worked fine this year, as shown in the following screen capture. (Possibly Chrome has always worked better than Safari for this sort of AWS functionality? Anyway, this year it's Firefox.). Feel free to test that this works, although we will not be using this route of access in the rest of the lab.

![image](https://hackmd.io/_uploads/HkjeeffHGl.png)

The "A standalone SSH client" panel tab does not actually allow you to connect directly from the web browser (you need to use a standalone SSH client, such as the `ssh` command in your shell (which is usually OpenSSH or similar), or a GUI program such as PuTTY on Microsoft Windows), but it does provide some useful instructions. You can click "Close" when you have read through the information presented, as specific instructions will be presented below.

:::info
:eyes: 
You may see slightly different information from what's shown in the following screen capture: for example, the DNS records for EC2 VMs may not be immediatly ready, and in those cases IP addresses will be provided instead of DNS names.

You can read up on the Domain Name System (DNS) but essentially DNS provides a mapping from names to IP addresses. So `www.google.co.nz` is a DNS name, and on pretty much any Linux / macOS / Windows terminal or command prompt, if you run the command `nslookup www.google.co.nz` then you will be told the IP address for that DNS name, `172.217.167.67`. Web browsers will happily reach the chosen site with either form, but `www.google.co.nz` is easier for humans to work with, since the name encodes semantics. (Actually, this is not entirely true---it may be that multiple DNS names point to the same IP address, which allows one webserver to serve up pages for multiple different websites, and thus if you visit the site by its IP address alone, the webserver will need to pick a default site for you to visit, of the possible choices.)
:::

![image](https://hackmd.io/_uploads/HkABeffHMl.png)

Note the public DNS address—in my case `ec2-54-198-58-67.compute-1.amazonaws.com`. It will almost certainly be different for you, and you will need to replace the DNS address of my EC2 instance with that of yours throughout the instructions below.

### Your VM's SSH host key

For security best practice, you should find out what the host (i.e., VM's) key fingerprint is, before you trust connecting to it using SSH (which is what you do in the next section, below).

(In practice, it is highly unlikely that a person-in-the-middle attack will have been set up for a VM that you just created, and it is also unlikely that you will place material that you treat as sensitive on a VM for doing COSC349 lab exercises anyway. However it's ideal to apply good security practices.)

You can determine the host key by looking at the server logs, in this case the console output captured from the VM as it booted up (that is when it created the host key).

![image](https://hackmd.io/_uploads/Hym3lzzrzx.png)

Keep the page like that in the following screen capture open so that when you SSH to your VM, you can check the VM's host key against the keys shown in the system log. (The screen capture's log output is scrolled to the right, to see the keys.)

![image](https://hackmd.io/_uploads/Hk-fbzzHGg.png)

### Failing to connect to your VM

First, let's try to connect to the EC2 VM in a way that will fail. Note that the first connection has prompted us to check the host key fingerprint. If you perform the steps described above, you can check that this really is your server that you're connecting to, as the long string starting `SHA256:` will match. (Although to be honest, the majority of users, globally, probably just type "yes" without checking, even though this is fundamentally insecure...)


```
$ ssh ec2-54-198-58-67.compute-1.amazonaws.com
The authenticity of host 'ec2-54-198-58-67.compute-1.amazonaws.com (54.198.58.67)' can't be established.
ED25519 key fingerprint is: SHA256:IE73CkyJF/k1pUNjq1ZTm98ZVz911kp2encnsmJ09bc
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? SHA256:IE73CkyJF/k1pUNjq1ZTm98ZVz911kp2encnsmJ09bc
Warning: Permanently added 'ec2-54-198-58-67.compute-1.amazonaws.com' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
dme@ec2-54-198-58-67.compute-1.amazonaws.com: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
$
```

This failed because the SSH server on your EC2 VM running Amazon Linux does not accept password logins, and yet we did not provide our private key to SSH so that it could use key-pair authentication (also, the connection is using the wrong username).

### Successfully connecting to your VM

This time, let's specify the key-pair file that we downloaded when we set up the EC2 instance. Note also that the SSH command instance below prepends `ec2-user@` to the address to indicate that SSH should connect as the user named `ec2-user`. 

You should be able to log in. You will see a command prompt similar to that shown below. As I have done, you can try running the `uname -a` command to request information about the running operating system. I then log out. (Typing <kbd>control</kbd><kbd>d</kbd> works to log out, too.)

```
❯ ssh -i ~/.ssh/cosc349-2026.pem ec2-user@ec2-54-198-58-67.compute-1.amazonaws.com
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sat Jul 25 10:28:54 2026 from 18.206.107.29
[ec2-user@ip-172-31-43-65 ~]$ uname -a
Linux ip-172-31-43-65.ec2.internal 6.18.38-73.137.amzn2023.x86_64 #2 SMP PREEMPT_DYNAMIC Mon Jul 13 22:27:08 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
[ec2-user@ip-172-31-43-65 ~]$ logout
Connection to ec2-54-198-58-67.compute-1.amazonaws.com closed.
```

## Installing a web server onto your VM

SSH to your VM again, but this time, use the commands below to update the software package list, and to install Apache and PHP. Note that the default Linux EC2 VMI is a RedHat-flavour of Linux rather than a Ubuntu flavour, so the package management commands are different, and the names of the packages are different. (In an ideal world, we'd only have to use one package manager...)

:::info
:eyes:
It is likely that you will need to look up and read at least the introductory documentation of the software package management commands used to install software on whatever variety of Linux you use, when you are not following specific lab exercises. 

(You are likely either to be using `apt` on Debian-derived distributions, such as Ubuntu, or `yum` on RedHat-derived distributions, such as Fedora, or Amazon's Linux AMI.)
:::

:::warning
:warning:
There are multiple commands **within** the transcript below: you need to read through what's shown to find them. (Or alternatively you could search for a constant prefix of my shell prompt string `[ec2-user@ip-172-31-43-65 ~]` to find each of them.)
:::

```
$ ssh -i ~/.ssh/cosc349-2026.pem ec2-user@ec2-54-198-58-67.compute-1.amazonaws.com
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sat Jul 25 10:37:12 2026 from 161.65.248.210
[ec2-user@ip-172-31-43-65 ~]$ sudo yum update -y
Amazon Linux 2023 Kernel Livepatch repository                                            514 kB/s |  61 kB     00:00    
Dependencies resolved.
Nothing to do.
Complete!
[ec2-user@ip-172-31-43-65 ~]$ sudo yum install -y httpd php
Last metadata expiration check: 0:00:14 ago on Sat Jul 25 10:40:08 2026.
Dependencies resolved.
=========================================================================================================================
 Package                         Architecture       Version                                Repository               Size
=========================================================================================================================
Installing:
 httpd                           x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              46 k
 php8.5                          x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              14 k
Installing dependencies:
 apr                             x86_64             1.7.5-1.amzn2023.0.4                   amazonlinux             129 k
 apr-util                        x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              97 k
 apr-util-lmdb                   x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              13 k
 generic-logos-httpd             noarch             18.0.0-12.amzn2023.0.3                 amazonlinux              19 k
 httpd-core                      x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux             1.4 M
 httpd-filesystem                noarch             2.4.68-1.amzn2023.0.1                  amazonlinux              12 k
 httpd-tools                     x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              80 k
 libbrotli                       x86_64             1.0.9-4.amzn2023.0.2                   amazonlinux             315 k
 libsodium                       x86_64             1.0.19-5.amzn2023                      amazonlinux             174 k
 mailcap                         noarch             2.1.49-3.amzn2023.0.3                  amazonlinux              33 k
 nginx-filesystem                noarch             1:1.30.3-1.amzn2023.0.1                amazonlinux              10 k
 php8.5-cli                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             6.1 M
 php8.5-common                   x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             860 k
 php8.5-process                  x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              49 k
 php8.5-xml                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             248 k
Installing weak dependencies:
 apr-util-openssl                x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              15 k
 mod_http2                       x86_64             2.0.42-1.amzn2023.0.1                  amazonlinux             167 k
 mod_lua                         x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              59 k
 php8.5-fpm                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             3.1 M
 php8.5-mbstring                 x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             539 k
 php8.5-pdo                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             100 k
 php8.5-sodium                   x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              45 k

Transaction Summary
=========================================================================================================================
Install  24 Packages

Total download size: 14 M
Installed size: 66 M
Downloading Packages:
(1/24): apr-1.7.5-1.amzn2023.0.4.x86_64.rpm                                              3.1 MB/s | 129 kB     00:00    
(2/24): apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64.rpm                                    309 kB/s |  13 kB     00:00    
(3/24): apr-util-1.6.3-1.amzn2023.0.2.x86_64.rpm                                         2.0 MB/s |  97 kB     00:00    
(4/24): generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch.rpm                            730 kB/s |  19 kB     00:00    
(5/24): apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64.rpm                                 479 kB/s |  15 kB     00:00    
(6/24): httpd-2.4.68-1.amzn2023.0.1.x86_64.rpm                                           1.6 MB/s |  46 kB     00:00    
(7/24): httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch.rpm                                537 kB/s |  12 kB     00:00    
(8/24): httpd-tools-2.4.68-1.amzn2023.0.1.x86_64.rpm                                     2.6 MB/s |  80 kB     00:00    
(9/24): httpd-core-2.4.68-1.amzn2023.0.1.x86_64.rpm                                       26 MB/s | 1.4 MB     00:00    
(10/24): libsodium-1.0.19-5.amzn2023.x86_64.rpm                                          6.9 MB/s | 174 kB     00:00    
(11/24): libbrotli-1.0.9-4.amzn2023.0.2.x86_64.rpm                                       6.3 MB/s | 315 kB     00:00    
(12/24): mod_http2-2.0.42-1.amzn2023.0.1.x86_64.rpm                                      7.7 MB/s | 167 kB     00:00    
(13/24): mailcap-2.1.49-3.amzn2023.0.3.noarch.rpm                                        1.0 MB/s |  33 kB     00:00    
(14/24): mod_lua-2.4.68-1.amzn2023.0.1.x86_64.rpm                                        2.0 MB/s |  59 kB     00:00    
(15/24): php8.5-8.5.8-1.amzn2023.0.1.x86_64.rpm                                          613 kB/s |  14 kB     00:00    
(16/24): nginx-filesystem-1.30.3-1.amzn2023.0.1.noarch.rpm                               340 kB/s |  10 kB     00:00    
(17/24): php8.5-common-8.5.8-1.amzn2023.0.1.x86_64.rpm                                    25 MB/s | 860 kB     00:00    
(18/24): php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64.rpm                                  16 MB/s | 539 kB     00:00    
(19/24): php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64.rpm                                       39 MB/s | 3.1 MB     00:00    
(20/24): php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64.rpm                                      3.9 MB/s | 100 kB     00:00    
(21/24): php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64.rpm                                       44 MB/s | 6.1 MB     00:00    
(22/24): php8.5-process-8.5.8-1.amzn2023.0.1.x86_64.rpm                                  963 kB/s |  49 kB     00:00    
(23/24): php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64.rpm                                   1.0 MB/s |  45 kB     00:00    
(24/24): php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64.rpm                                      7.1 MB/s | 248 kB     00:00    
-------------------------------------------------------------------------------------------------------------------------
Total                                                                                     34 MB/s |  14 MB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                 1/1 
  Installing       : php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                                                      1/24 
  Installing       : apr-1.7.5-1.amzn2023.0.4.x86_64                                                                2/24 
  Installing       : apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                                                      3/24 
  Installing       : apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64                                                   4/24 
  Installing       : apr-util-1.6.3-1.amzn2023.0.2.x86_64                                                           5/24 
  Installing       : mailcap-2.1.49-3.amzn2023.0.3.noarch                                                           6/24 
  Running scriptlet: httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  7/24 
  Installing       : httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  7/24 
  Installing       : httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                                                       8/24 
  Installing       : httpd-core-2.4.68-1.amzn2023.0.1.x86_64                                                        9/24 
  Installing       : mod_http2-2.0.42-1.amzn2023.0.1.x86_64                                                        10/24 
  Installing       : mod_lua-2.4.68-1.amzn2023.0.1.x86_64                                                          11/24 
  Installing       : php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                                                        12/24 
  Installing       : php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64                                                   13/24 
  Installing       : php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                                                        14/24 
  Installing       : php8.5-process-8.5.8-1.amzn2023.0.1.x86_64                                                    15/24 
  Installing       : php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                                                        16/24 
  Running scriptlet: nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               17/24 
  Installing       : nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               17/24 
  Installing       : php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        18/24 
  Running scriptlet: php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        18/24 
  Installing       : libsodium-1.0.19-5.amzn2023.x86_64                                                            19/24 
  Installing       : php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                                                     20/24 
  Installing       : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                                                         21/24 
  Installing       : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                             22/24 
  Installing       : httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            23/24 
  Running scriptlet: httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            23/24 
  Installing       : php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            24/24 
  Running scriptlet: httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            24/24 
  Running scriptlet: php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            24/24 
  Verifying        : apr-1.7.5-1.amzn2023.0.4.x86_64                                                                1/24 
  Verifying        : apr-util-1.6.3-1.amzn2023.0.2.x86_64                                                           2/24 
  Verifying        : apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                                                      3/24 
  Verifying        : apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64                                                   4/24 
  Verifying        : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                              5/24 
  Verifying        : httpd-2.4.68-1.amzn2023.0.1.x86_64                                                             6/24 
  Verifying        : httpd-core-2.4.68-1.amzn2023.0.1.x86_64                                                        7/24 
  Verifying        : httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  8/24 
  Verifying        : httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                                                       9/24 
  Verifying        : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                                                         10/24 
  Verifying        : libsodium-1.0.19-5.amzn2023.x86_64                                                            11/24 
  Verifying        : mailcap-2.1.49-3.amzn2023.0.3.noarch                                                          12/24 
  Verifying        : mod_http2-2.0.42-1.amzn2023.0.1.x86_64                                                        13/24 
  Verifying        : mod_lua-2.4.68-1.amzn2023.0.1.x86_64                                                          14/24 
  Verifying        : nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               15/24 
  Verifying        : php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            16/24 
  Verifying        : php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                                                        17/24 
  Verifying        : php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                                                     18/24 
  Verifying        : php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        19/24 
  Verifying        : php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64                                                   20/24 
  Verifying        : php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                                                        21/24 
  Verifying        : php8.5-process-8.5.8-1.amzn2023.0.1.x86_64                                                    22/24 
  Verifying        : php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                                                     23/24 
  Verifying        : php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                                                        24/24 

Installed:
  apr-1.7.5-1.amzn2023.0.4.x86_64                               apr-util-1.6.3-1.amzn2023.0.2.x86_64                     
  apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                     apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64             
  generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch             httpd-2.4.68-1.amzn2023.0.1.x86_64                       
  httpd-core-2.4.68-1.amzn2023.0.1.x86_64                       httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch            
  httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                      libbrotli-1.0.9-4.amzn2023.0.2.x86_64                    
  libsodium-1.0.19-5.amzn2023.x86_64                            mailcap-2.1.49-3.amzn2023.0.3.noarch                     
  mod_http2-2.0.42-1.amzn2023.0.1.x86_64                        mod_lua-2.4.68-1.amzn2023.0.1.x86_64                     
  nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch               php8.5-8.5.8-1.amzn2023.0.1.x86_64                       
  php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                
  php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64              
  php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-process-8.5.8-1.amzn2023.0.1.x86_64               
  php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                     php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                   

Complete!
[ec2-user@ip-172-31-43-65 ~]$ sudo systemctl start httpd.service
[ec2-user@ip-172-31-43-65 ~]$ 
```

:::info
:eyes: 
Note that the very last command, the invocation of `systemctl` to start the web server on your VM, has many historic variants, a number of which will still work. For example, you can also instead type `sudo service httpd start` (this will be redirected to `systemctl`). I mention this just in case it helps correlate documentation you find on the web with your experience on EC2.
:::


At this point, the Apache web server and PHP language should be installed and enabled. Visit your VM's public IP address in a web browser, for example, I visited http://ec2-54-198-58-67.compute-1.amazonaws.com to see mine. You should see an Apache test page, but note that most browsers and links will default to offering up and/or connecting to `https:` rather than `http:`. You should double check use of `http:` if your web server test does not work. If your browser goes further to complain about the `http` website being insecure (fair enough!) and it is difficult to convince the browser to proceed anyway, or the page otherwise fails to open, it may be worth trying to use a different browser.

![image](https://hackmd.io/_uploads/BkIHXGGSfx.png)

Now let's create a PHP script to test that PHP is installed and successfully executing code. The shell invocation (on your EC2 VM) creates a PHP script at `/var/www/html/test.php`. We can use the `sudo` command to provide super-user privileges to the command following the `sudo`. However we can't usually use a form such as `sudo echo > /protected/place`, since the redirection to the file (the `> /protected/place` part) is actually handled by the shell, and is not seen by `sudo`, and thus won't be able to access protected places. The `tee` command writes to the filename it is given, as well as standard output, so we can apply `sudo` to `tee` to write to protected file locations.

```
[ec2-user@ip-172-31-43-65 ~]$ echo '<?php phpinfo(); ?>' | sudo tee /var/www/html/test.php
<?php phpinfo(); ?>
[ec2-user@ip-172-31-43-65 ~]$ 
```

Now—substituting your VM's public IP address—try to visit http://ec2-54-198-58-67.compute-1.amazonaws.com/test.php (Note that I have added `test.php` to the end of the URL we used previously---by default, the Apache web server takes the part of the URL after the slash after the DNS name, and appends that onto the web root---`/var/www` in our case---to find the filename to retrieve on the server. If a directory is selected, Apache looks for a set of default filenames, including `index.html`.)

![image](https://hackmd.io/_uploads/r1rWNMfrzl.png)

## Cloud Hygiene

We have experimented with a VM on EC2. In this case the cost of resources that we have used will be extremely small. Nonetheless the VM is still running on EC2!

It is very important that you remember to shut down VMs that you start, unless they will be doing useful work.

A good idea is to look at the dashboard any time that you expect to stop working with Amazon services, and ensure that only VMs that you expect to be running are running.

In this case, when I had finished all my experimentation with this lab exercise. I terminated the VM. You can do this from the EC2 dashboard. I happened to already be on the instance summary page for the VM I had created, and from there could use the "Instance state" button near the centre-top of the page, and from that menu choose "Terminate instance".

![image](https://hackmd.io/_uploads/B188EzMrfg.png)

You will be shown a confirmation dialogue, since in this case, terminating the instance will also destroy the hard-disk image and anything stored on our VM. This is what we expect in this case, so we can proceed by clicking "Terminate".

![image](https://hackmd.io/_uploads/SJt_4Mfrze.png)

I was then returned to the instance summary with a confirmation banner that the instance had been terminated.

![image](https://hackmd.io/_uploads/Hy6KEfMHMg.png)

Refreshing the main EC2 Dashboard should show that there are no running instances.

:::info
:eyes: 
The EC2 Dashboard may not update immediately---there is a refresh button in the top-right of the EC2 Dashboard page aligned with the "Resources" heading.
:::

![image](https://hackmd.io/_uploads/SJUs4GzBMl.png)

Amazon has clear [documentation of the instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html).

## Build your own LAMP stack

The instructions above are intended to provide enough context specific to the Owheo Building Labs to allow you to work through tutorial material on the AWS website.

Now that you've created, visited, modified and destroyed a single EC2 VM, you can move on to a more complex example that involves creating multiple VMs.

:::info
:eyes:
While you should definitely ensure that you are comfortable working with EC2, and following the documentation and tutorials that are provided, you are not *required* to complete any given proportion of their material, or the specific tutorial below. Just experiment with their services within the time that you have available in the lab. We'd be keen to hear what your experiences are. Remember that you can refer back to this lab later if you need to, of course, if it is useful for completing assignment work, for example.
:::

:::success
:pencil: 
Work through the [Amazon tutorial at that describes how to build a LAMP stack](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html) with a separate database and web server. 

Note that if you use your AWS Academy Learner Lab COSC349 credit, you may face limitations compared to if you---say---used your own personal AWS account. For example, the use of elastic IP addresses, Identity and Access Management, and virtual private cloud configuration may not work as expected. You can usually work around this sort of limitation, e.g., using the specific IP addresses allocated to your EC2 instances.
:::

:::success
:pencil: 
Using the materials in this lab, and by reading Amazon's documentation (e.g., their LAMP stack tutorial mentioned above), try to create two fresh VMs and setup EC2 to allow you to `ssh` from one to the other. Then have one VM download a web page from the other VM (e.g., using a command such as `wget` or `curl`---you may well need to look up how to use these commands). 
:::

Please seek help if you get stuck or run into knowledge gaps, and we can augment these lab exercises to support what you need to know.

## Closing down your AWS Academy Learner Lab

When you want to pause or have finished using AWS Academy Learner Lab resources, you should use the "End Lab" button on the learning management system page from which you opened the AWS Management Console:

![image](https://hackmd.io/_uploads/B11QBMGHfe.png)

You will be asked for confirmation:

![image](https://hackmd.io/_uploads/By57SMMrzg.png)

... and the AWS indicator goes from green to yellow, and eventually to red.## Preliminaries for Lab 3

:::warning
:warning: 
Since I (Dave) already have an AWS Academy account, I was not able to see precisely what you see through all steps entering your AWS Academy Learner Lab. I believe that you end up seeing the same AWS Console that I do, but with some key differences:
- I can potentially log in as "you" to your AWS Console, but only in the context of your AWS Academy Learner Lab. In practice I only expect to do this for three main potential reasons (1) you contact members of the teaching team asking for support, (2) the teaching team needs access to material for marking, or (3) some other problem arises and I need to perform action;
- You don't need a registered credit card;
- You do not need to pay Amazon for the initial credit allocated to you;
- Within the context of this AWS Academy Learner Lab, your AWS credit and all AWS resources you use will vanish when the Learner Lab closes (which will happen in the exam period after semester).

Of course please do let me know if you see material significantly different from the screen-shots and descriptions that I provide below!
:::


### Activating your AWS Academy account

Using AWS costs money, although Amazon are also very keen that potential future clients of their cloud services (i.e., you!) are not turned away by the cost. Of the many ways Amazon tries to help:
1. Amazon provide a generous "free tier", that allows you to try out many of their services without cost. Sometimes the free tier is refreshed annually, so that small-scale use of Amazon services actually end up being free, in effect. However using the free tier requires having an Amazon account, which in turn requires registering a credit card, even if it doesn't end up getting charged.
2. Amazon provide generous educational access, such as the AWS Academy, and the Learner Lab within the AWS Academy that we will be trying to use.

:::info
:bulb: 
Although we have discussed Amazon Web Services here, most of the major public cloud providers provide some sort of equivalent free tier, or academic / educational / student / hobbyist access. Feel free to collect them all—I would be interested to hear about your experiences comparing their offerings.
:::

You should have received, to your student email address, an email that instructs you how to join AWS Academy and the Learner Lab we will use in COSC349.

## Lab 3—AWS IaaS with EC2

You should be able to complete this lab using any operating system, e.g., macOS, Linux, or Windows, including the lab computers within the Owheo Building. (The instructions have been developed on macOS and updated on Linux systems, though.)

:::info
:bulb: 
On the Microsoft Windows lab computers within the Owheo Building, I would recommend trying Git Bash as your shell. There are certainly ways to use PowerShell and `cmd.exe`, but where Git Bash works, all three major operating system types can follow the same instructions.
:::

In this lab we will introduce use of Amazon's Elastic Compute Cloud service (EC2), which provides (among other features) Infrastructure as a Service—i.e., the provision of (headless) virtual machines of the type you have experimented with using Vagrant in Lab 2.

Note that, consistent with the overall goals of COSC349, a primary aim is for you to see how the pieces of software infrastructure piece together. There are many excellent ways to deploy applications to AWS with a single mouse-click. You are very welcome to experiment with these, and provided that you keep an eye on your Amazon expenditure, there should be plenty of credit within your Amazon Academy allowance to facilitate you exploring the high-convenience Amazon offerings as well as the more technically detailed ones.

### Lab objectives

1. Ensure you can access Amazon Web Services using your AWS Academy account or otherwise.
1. Learn how to configure and launch virtual machines in the Amazon Elastic Compute Cloud (EC2).
1. Interact with your EC2 VMs as a user—e.g., view web sites produced by your VM.
1. Interact with your EC2 VMs as a developer, using the secure shell (SSH).
1. Understand and practice good "cloud hygiene", such as always checking that you have shut down unused but resource (and credit) consuming VMs.

:::info
:exclamation: 
To get the most out of the lab exercises, it is likely that you should take notes about the concepts you are least clear about. That way you can easily do further reading to acquire the information that you need, or can ask the COSC349 teaching team members for help or explanations.
:::

## Let's create a VM on AWS

Infrastructure as a Service VMs on Amazon are managed under the Amazon's Elastic Compute Cloud service—known as EC2.

Interactive control of AWS resources can usually be achieved through use of the AWS Management Console, which is accessed through the web using a web browser.

Normally you would log into the AWS Management Console directly, but for the AWS Academy Learner Labs, we need to use a less direct route. First, you log into AWS Academy, to access the learning management system they use (Canvas, I think). A module within the learning management system contains the controls that allow you to activate and deactivate your AWS Academy Learner Lab, and to access to many useful functions including: (1) opening a new browser tab to access AWS Management Console, and (2) to allow you to retrieve AWS credentials that you can use for command line tools (we'll get to that later).

:::info
:confused: 
AWS are currently still the world's leading public cloud provider (at least for IaaS). You may then wonder why some of the AWS Academy functionality (like the AWS Educate system before it) feels technically just a bit like it is probably not world leading. The reason for this apparent inconsistency is that AWS outsource their educational programmes to be largely run and implemented by a third party: Vocareum. In past COSC349 instances, all technical wrinkles have been able to be worked around or ironed out.
:::

### Log into AWS Academy

As noted above, to gain access to the AWS Management Console you first need to log into [AWS Academy](http://awsacademy.instructure.com) or more specifically the [COSC349 course within AWS Academy](https://awsacademy.instructure.com//courses/177746) ... but actually you may be able to [jump straight to the Learner Lab controls](https://awsacademy.instructure.com/courses/177746/modules/items/17458680) with the COSC349 AWS Academy course. The course is helpfully named "AWS Academy Learner Lab".

### Reach the Learner Lab

:::warning
:bomb: 
On macOS it seems that Safari does not work to reach the Learner Lab page. This year I'm updating the notes from an old iMac running Linux, so updated screenshots are from Firefox.
:::

After I had successfully logged into AWS Academy and assumed the persona of "student view", I was able to reach a page such as shown in the following screen capture:

![image](https://hackmd.io/_uploads/ByJSSWGHMg.png)

From which I clicked "Start Lab" in the controls above the shell window. The AWS control should change from red to yellow... and then eventually to green (although this can at worst take minutes, or at least, it did for me):

![image](https://hackmd.io/_uploads/SyNEIWGSzl.png)

### AWS credentials for CLI tools

At first we will access cloud resources using a web browser, but the Learner Lab page can provide you with the details that you need to give to command line tools (CLI) in order for those tools to be able to access AWS using your AWS Academy Learner Lab identity.

:::warning
:warning: Note that the AWS credentials roll over every few hours. So if your API access suddenly breaks, this may be what has happened.
:::

You can click on the "AWS Details" button near "Start Lab", and the panel to the rght of the shell will show details including "AWS CLI", which has a large "Show" button to its right.
![image](https://hackmd.io/_uploads/H1m2LZGrGe.png)

Click "show" to find out what should go into, for example, AWS credentials files used by the `aws` command line tool. This tool should be installed on the Owheo lab computers. The credentials are typically pasted into `~/.aws/credentials` on macOS and Linux machines, or `.aws\credentials` on Microsoft Windows, under your home directory. The fields you need to paste are `aws_access_key_id`, `aws_secret_access_key`, and `aws_session_token`).

### AWS Management Console

When your AWS indicator to the left of "Start Lab" is green, click on it to open another browser tab that will show you the AWS Management Console, logged in as your AWS Academy Learner Lab identity. I was greeted with a page that looked like: (note that you can scroll down a long way)

![image](https://hackmd.io/_uploads/SyECP-frfe.png)

This is the AWS Management Console for the North Virginia region. AWS Academy Learner Labs run in a particular AWS region, so you will not be able to change this. For unrestricted AWS accounts, you can change regions using the drop-down at the top-right of the window. However note that most resources have different prices in the different regions: the US East Coast regions are usually the cheapest.

One of the first points to note is that AWS provides a search-engine-style search box just to navigate within the services on offer. That should give you some idea of how many different types of services are available! (i.e., many hundreds!)

### Launch a virtual machine with EC2

At first, let's use the EC2 wizard to launch a VM. Although I have no services within the "Recently visited" area of the page, it already had a link to EC2, so I clicked on that, and reached the EC2 page.

![image](https://hackmd.io/_uploads/B1giuWGHze.png)

From there I clicked on "Launch instance".

![image](https://hackmd.io/_uploads/rJi1tWzSzl.png)

First fill in a name, I used "My test VM" (very creative, I know). Scroll down to the section entitled "Application and OS Images (Amazon Machine Image)".

Amazon Machine Images (AMIs) are akin to Vagrant "box" files or VirtualBox virtual hard disks. They are the starting point of the persistent storage of the machine.

The default choice of Amazon Linux is just fine, for our test. (More specifically this default for me reached the AMI with ID `ami-004f790b835b26145`, which is "Amazon Linux 2023 2023.12.20260724.0 x86_64 HVM kernel-6.18".)

![image](https://hackmd.io/_uploads/BJfWcWfBGl.png)

Scroll down and note that the instance type is `t3.micro` which is one of the cheapest types of VMs: e.g., about 1.04 US cents per hour. (Most of your work in COSC349 should work fine on `t3.micro` instances.)

![image](https://hackmd.io/_uploads/SkzH9bGrMl.png)

Next scroll to the "Key pair (login)" section of the page (visible in the previous screen capture). AWS typically does not offer a way to create password-based access, e.g., to SSH to your EC2 VMs. Instead public key authentication is used, where you receive a private key in a file, and Amazon (and anyone else) can view the public key.

If you are first accessing AWS, you will need to use the option to the right to "Create new key pair". (If you have already registered key pairs, you can select them from the drop-down box.)

![image](https://hackmd.io/_uploads/SJKdjZfHGg.png)

I recommend using a name without spaces, such as `cosc349-2024`. ED25519 is more efficient and probably more secure than RSA, but either should work. Most variants of SSH clients should work fine with the `.pem` format---the default (however if you are using the PuTTY tool on Microsoft Windows specifically, you might prefer the `.ppk` format).

Finally, select "Create key pair", and note that your selected type of file (I chose `.pem` will be downloaded by your web browser). This is your private key (file) for the named key pair.

Click "Download Key Pair", and move the private key file to somewhere safe, and make the file private. On a macOS / Linux machines / Git Bash on Owheo Building Lab Windows computers, a good place to move it to is within the `.ssh` subdirectory of your home directory. This is a conventional place to store encryption keys used for SSH (and the default place that must SSH software looks for keys).

:::warning
:warning:
The `~` in Bash shell commands should expand to your home directory. On the Owheo Building Lab computers in Git Bash, in 2025 `~` expanded to the wrong path for a while, but the system is different from 2026, and is hopefully fixed.
:::

:::warning
:warning: 
Note that it's possible that you do not yet have an `.ssh` directory in your home-directory. You can check—
```
$ ls -ld ~/.ssh
drwx------  34 dme  11306  1088  3 Aug 16:08 /Users/dme/.ssh
```
—and if the `ls` command fails, create the directory and set the permissions appropriately (SSH will refuse to use files from directories that are not sufficiently protected) using the command `mkdir -m 700 ~/.ssh` (the `-m` option sets the access control bits on that directory, just as is done when using the `chmod` command).
:::

You may need to rename the key file that was downloaded by your web-browser to remove the `.txt` extension if there is one. I changed the permissions to make the file only readable by my user using the following shell invocations:

```
$ mv ~/Downloads/cosc349-2026.pem ~/.ssh/
$ chmod 400 ~/.ssh/cosc349-2026.pem 
$ ls -l ~/.ssh/cosc349-2026.pem 
.r-------- 387 dme 25 Jul 22:12  /home/dme/.ssh/cosc349-2026.pem
```

Next, scroll down to the "Network settings" controls.

![image](https://hackmd.io/_uploads/HJxm0WzBzx.png)

The default to create a security group (as noted on the page, a security group is essentially a set of firewall rules), which is fine.

The default to "Allow SSH from ... Anywhere", is fine for our purposes. For production EC2 VMs, best practice would involve narrowing down the set of IP addresses that should be permitted to SSH to the EC2 VM.

Change the "Allow HTTPS ..." and "Allow HTTP ..." traffic options to be ticked (two checks).

Then scroll down to the "Configure storage" section of the page.

![image](https://hackmd.io/_uploads/rJthA-GSMg.png)

The default, to create a single 8 GiB `gp3` disk is fine. As you can determine from the pull-down option, `gp3` is a type of SSD storage that AWS offers. There are other choices, most of which will be more expensive per hour, but will have higher performance.

:::info
:eyes:
Note that the free tier message mentions "EBS", i.e. AWS Elastic Block Storage. EBS volumes are actually accessed over the AWS datacentre network: they are not hard disks directly attached to the physical computer running your EC2 VM. The default settings here will delete your EBS volume when your VM is terminated, but this is not the only way to configure EBS to work. Also, EBS always uses redundant hardware, so no EBS volume will be lost if a single hardware device, such as a hard disk, fails.
:::

You can skip the "Advanced details" part of the page, which is hidden by default, anyway.

Scrolling to the "Summary" section, I see something like:

![image](https://hackmd.io/_uploads/r1kzyfGSMe.png)

If everything looks good, you should select the "Launch Instance" button, to create and start your EC2 VM.

### Success launching your VM

Hopefully you successfully launch your VM, and see output similar to:

![image](https://hackmd.io/_uploads/ry3QJGfHzg.png)

Now return to the EC2 Dashboard (the topmost link on the left menu).

## EC2 Dashboard

You should now be viewing the EC2 dashboard. 

![image](https://hackmd.io/_uploads/Bk2V1GMBzl.png)

I clicked on "Instances (running)" to see a list of EC2 VMs that have been launched (i.e., our test VM).

You can click the checkbox next to your instance to view its details in the information panel in the lower half of the web page. This area includes details such as your public and private IPv4 addresses, and your "Public IPv4 DNS".

![image](https://hackmd.io/_uploads/S1lPJzzSMe.png)

## Connect to your VM using SSH

If you click the "Connect" button (top of page) after choosing your instance (it will be selected by default if there is only one instance), you get a choice of four different ways to open an SSH connection to your instance.

The "EC2 Instance Connect" panel tab allows you to open a JavaScript-driven SSH connection within your web browser. Click "Connect" to open the connection (the button is at the bottom right of the web page).

![image](https://hackmd.io/_uploads/SkF5yGfHfx.png)

In the past this type of AWS EC2 web shell did not work for me, but it worked fine this year, as shown in the following screen capture. (Possibly Chrome has always worked better than Safari for this sort of AWS functionality? Anyway, this year it's Firefox.). Feel free to test that this works, although we will not be using this route of access in the rest of the lab.

![image](https://hackmd.io/_uploads/HkjeeffHGl.png)

The "A standalone SSH client" panel tab does not actually allow you to connect directly from the web browser (you need to use a standalone SSH client, such as the `ssh` command in your shell (which is usually OpenSSH or similar), or a GUI program such as PuTTY on Microsoft Windows), but it does provide some useful instructions. You can click "Close" when you have read through the information presented, as specific instructions will be presented below.

:::info
:eyes: 
You may see slightly different information from what's shown in the following screen capture: for example, the DNS records for EC2 VMs may not be immediatly ready, and in those cases IP addresses will be provided instead of DNS names.

You can read up on the Domain Name System (DNS) but essentially DNS provides a mapping from names to IP addresses. So `www.google.co.nz` is a DNS name, and on pretty much any Linux / macOS / Windows terminal or command prompt, if you run the command `nslookup www.google.co.nz` then you will be told the IP address for that DNS name, `172.217.167.67`. Web browsers will happily reach the chosen site with either form, but `www.google.co.nz` is easier for humans to work with, since the name encodes semantics. (Actually, this is not entirely true---it may be that multiple DNS names point to the same IP address, which allows one webserver to serve up pages for multiple different websites, and thus if you visit the site by its IP address alone, the webserver will need to pick a default site for you to visit, of the possible choices.)
:::

![image](https://hackmd.io/_uploads/HkABeffHMl.png)

Note the public DNS address—in my case `ec2-54-198-58-67.compute-1.amazonaws.com`. It will almost certainly be different for you, and you will need to replace the DNS address of my EC2 instance with that of yours throughout the instructions below.

### Your VM's SSH host key

For security best practice, you should find out what the host (i.e., VM's) key fingerprint is, before you trust connecting to it using SSH (which is what you do in the next section, below).

(In practice, it is highly unlikely that a person-in-the-middle attack will have been set up for a VM that you just created, and it is also unlikely that you will place material that you treat as sensitive on a VM for doing COSC349 lab exercises anyway. However it's ideal to apply good security practices.)

You can determine the host key by looking at the server logs, in this case the console output captured from the VM as it booted up (that is when it created the host key).

![image](https://hackmd.io/_uploads/Hym3lzzrzx.png)

Keep the page like that in the following screen capture open so that when you SSH to your VM, you can check the VM's host key against the keys shown in the system log. (The screen capture's log output is scrolled to the right, to see the keys.)

![image](https://hackmd.io/_uploads/Hk-fbzzHGg.png)

### Failing to connect to your VM

First, let's try to connect to the EC2 VM in a way that will fail. Note that the first connection has prompted us to check the host key fingerprint. If you perform the steps described above, you can check that this really is your server that you're connecting to, as the long string starting `SHA256:` will match. (Although to be honest, the majority of users, globally, probably just type "yes" without checking, even though this is fundamentally insecure...)


```
$ ssh ec2-54-198-58-67.compute-1.amazonaws.com
The authenticity of host 'ec2-54-198-58-67.compute-1.amazonaws.com (54.198.58.67)' can't be established.
ED25519 key fingerprint is: SHA256:IE73CkyJF/k1pUNjq1ZTm98ZVz911kp2encnsmJ09bc
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? SHA256:IE73CkyJF/k1pUNjq1ZTm98ZVz911kp2encnsmJ09bc
Warning: Permanently added 'ec2-54-198-58-67.compute-1.amazonaws.com' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
dme@ec2-54-198-58-67.compute-1.amazonaws.com: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
$
```

This failed because the SSH server on your EC2 VM running Amazon Linux does not accept password logins, and yet we did not provide our private key to SSH so that it could use key-pair authentication (also, the connection is using the wrong username).

### Successfully connecting to your VM

This time, let's specify the key-pair file that we downloaded when we set up the EC2 instance. Note also that the SSH command instance below prepends `ec2-user@` to the address to indicate that SSH should connect as the user named `ec2-user`. 

You should be able to log in. You will see a command prompt similar to that shown below. As I have done, you can try running the `uname -a` command to request information about the running operating system. I then log out. (Typing <kbd>control</kbd><kbd>d</kbd> works to log out, too.)

```
❯ ssh -i ~/.ssh/cosc349-2026.pem ec2-user@ec2-54-198-58-67.compute-1.amazonaws.com
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sat Jul 25 10:28:54 2026 from 18.206.107.29
[ec2-user@ip-172-31-43-65 ~]$ uname -a
Linux ip-172-31-43-65.ec2.internal 6.18.38-73.137.amzn2023.x86_64 #2 SMP PREEMPT_DYNAMIC Mon Jul 13 22:27:08 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
[ec2-user@ip-172-31-43-65 ~]$ logout
Connection to ec2-54-198-58-67.compute-1.amazonaws.com closed.
```

## Installing a web server onto your VM

SSH to your VM again, but this time, use the commands below to update the software package list, and to install Apache and PHP. Note that the default Linux EC2 VMI is a RedHat-flavour of Linux rather than a Ubuntu flavour, so the package management commands are different, and the names of the packages are different. (In an ideal world, we'd only have to use one package manager...)

:::info
:eyes:
It is likely that you will need to look up and read at least the introductory documentation of the software package management commands used to install software on whatever variety of Linux you use, when you are not following specific lab exercises. 

(You are likely either to be using `apt` on Debian-derived distributions, such as Ubuntu, or `yum` on RedHat-derived distributions, such as Fedora, or Amazon's Linux AMI.)
:::

:::warning
:warning:
There are multiple commands **within** the transcript below: you need to read through what's shown to find them. (Or alternatively you could search for a constant prefix of my shell prompt string `[ec2-user@ip-172-31-43-65 ~]` to find each of them.)
:::

```
$ ssh -i ~/.ssh/cosc349-2026.pem ec2-user@ec2-54-198-58-67.compute-1.amazonaws.com
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sat Jul 25 10:37:12 2026 from 161.65.248.210
[ec2-user@ip-172-31-43-65 ~]$ sudo yum update -y
Amazon Linux 2023 Kernel Livepatch repository                                            514 kB/s |  61 kB     00:00    
Dependencies resolved.
Nothing to do.
Complete!
[ec2-user@ip-172-31-43-65 ~]$ sudo yum install -y httpd php
Last metadata expiration check: 0:00:14 ago on Sat Jul 25 10:40:08 2026.
Dependencies resolved.
=========================================================================================================================
 Package                         Architecture       Version                                Repository               Size
=========================================================================================================================
Installing:
 httpd                           x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              46 k
 php8.5                          x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              14 k
Installing dependencies:
 apr                             x86_64             1.7.5-1.amzn2023.0.4                   amazonlinux             129 k
 apr-util                        x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              97 k
 apr-util-lmdb                   x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              13 k
 generic-logos-httpd             noarch             18.0.0-12.amzn2023.0.3                 amazonlinux              19 k
 httpd-core                      x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux             1.4 M
 httpd-filesystem                noarch             2.4.68-1.amzn2023.0.1                  amazonlinux              12 k
 httpd-tools                     x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              80 k
 libbrotli                       x86_64             1.0.9-4.amzn2023.0.2                   amazonlinux             315 k
 libsodium                       x86_64             1.0.19-5.amzn2023                      amazonlinux             174 k
 mailcap                         noarch             2.1.49-3.amzn2023.0.3                  amazonlinux              33 k
 nginx-filesystem                noarch             1:1.30.3-1.amzn2023.0.1                amazonlinux              10 k
 php8.5-cli                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             6.1 M
 php8.5-common                   x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             860 k
 php8.5-process                  x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              49 k
 php8.5-xml                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             248 k
Installing weak dependencies:
 apr-util-openssl                x86_64             1.6.3-1.amzn2023.0.2                   amazonlinux              15 k
 mod_http2                       x86_64             2.0.42-1.amzn2023.0.1                  amazonlinux             167 k
 mod_lua                         x86_64             2.4.68-1.amzn2023.0.1                  amazonlinux              59 k
 php8.5-fpm                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             3.1 M
 php8.5-mbstring                 x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             539 k
 php8.5-pdo                      x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux             100 k
 php8.5-sodium                   x86_64             8.5.8-1.amzn2023.0.1                   amazonlinux              45 k

Transaction Summary
=========================================================================================================================
Install  24 Packages

Total download size: 14 M
Installed size: 66 M
Downloading Packages:
(1/24): apr-1.7.5-1.amzn2023.0.4.x86_64.rpm                                              3.1 MB/s | 129 kB     00:00    
(2/24): apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64.rpm                                    309 kB/s |  13 kB     00:00    
(3/24): apr-util-1.6.3-1.amzn2023.0.2.x86_64.rpm                                         2.0 MB/s |  97 kB     00:00    
(4/24): generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch.rpm                            730 kB/s |  19 kB     00:00    
(5/24): apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64.rpm                                 479 kB/s |  15 kB     00:00    
(6/24): httpd-2.4.68-1.amzn2023.0.1.x86_64.rpm                                           1.6 MB/s |  46 kB     00:00    
(7/24): httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch.rpm                                537 kB/s |  12 kB     00:00    
(8/24): httpd-tools-2.4.68-1.amzn2023.0.1.x86_64.rpm                                     2.6 MB/s |  80 kB     00:00    
(9/24): httpd-core-2.4.68-1.amzn2023.0.1.x86_64.rpm                                       26 MB/s | 1.4 MB     00:00    
(10/24): libsodium-1.0.19-5.amzn2023.x86_64.rpm                                          6.9 MB/s | 174 kB     00:00    
(11/24): libbrotli-1.0.9-4.amzn2023.0.2.x86_64.rpm                                       6.3 MB/s | 315 kB     00:00    
(12/24): mod_http2-2.0.42-1.amzn2023.0.1.x86_64.rpm                                      7.7 MB/s | 167 kB     00:00    
(13/24): mailcap-2.1.49-3.amzn2023.0.3.noarch.rpm                                        1.0 MB/s |  33 kB     00:00    
(14/24): mod_lua-2.4.68-1.amzn2023.0.1.x86_64.rpm                                        2.0 MB/s |  59 kB     00:00    
(15/24): php8.5-8.5.8-1.amzn2023.0.1.x86_64.rpm                                          613 kB/s |  14 kB     00:00    
(16/24): nginx-filesystem-1.30.3-1.amzn2023.0.1.noarch.rpm                               340 kB/s |  10 kB     00:00    
(17/24): php8.5-common-8.5.8-1.amzn2023.0.1.x86_64.rpm                                    25 MB/s | 860 kB     00:00    
(18/24): php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64.rpm                                  16 MB/s | 539 kB     00:00    
(19/24): php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64.rpm                                       39 MB/s | 3.1 MB     00:00    
(20/24): php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64.rpm                                      3.9 MB/s | 100 kB     00:00    
(21/24): php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64.rpm                                       44 MB/s | 6.1 MB     00:00    
(22/24): php8.5-process-8.5.8-1.amzn2023.0.1.x86_64.rpm                                  963 kB/s |  49 kB     00:00    
(23/24): php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64.rpm                                   1.0 MB/s |  45 kB     00:00    
(24/24): php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64.rpm                                      7.1 MB/s | 248 kB     00:00    
-------------------------------------------------------------------------------------------------------------------------
Total                                                                                     34 MB/s |  14 MB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                 1/1 
  Installing       : php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                                                      1/24 
  Installing       : apr-1.7.5-1.amzn2023.0.4.x86_64                                                                2/24 
  Installing       : apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                                                      3/24 
  Installing       : apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64                                                   4/24 
  Installing       : apr-util-1.6.3-1.amzn2023.0.2.x86_64                                                           5/24 
  Installing       : mailcap-2.1.49-3.amzn2023.0.3.noarch                                                           6/24 
  Running scriptlet: httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  7/24 
  Installing       : httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  7/24 
  Installing       : httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                                                       8/24 
  Installing       : httpd-core-2.4.68-1.amzn2023.0.1.x86_64                                                        9/24 
  Installing       : mod_http2-2.0.42-1.amzn2023.0.1.x86_64                                                        10/24 
  Installing       : mod_lua-2.4.68-1.amzn2023.0.1.x86_64                                                          11/24 
  Installing       : php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                                                        12/24 
  Installing       : php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64                                                   13/24 
  Installing       : php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                                                        14/24 
  Installing       : php8.5-process-8.5.8-1.amzn2023.0.1.x86_64                                                    15/24 
  Installing       : php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                                                        16/24 
  Running scriptlet: nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               17/24 
  Installing       : nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               17/24 
  Installing       : php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        18/24 
  Running scriptlet: php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        18/24 
  Installing       : libsodium-1.0.19-5.amzn2023.x86_64                                                            19/24 
  Installing       : php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                                                     20/24 
  Installing       : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                                                         21/24 
  Installing       : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                             22/24 
  Installing       : httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            23/24 
  Running scriptlet: httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            23/24 
  Installing       : php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            24/24 
  Running scriptlet: httpd-2.4.68-1.amzn2023.0.1.x86_64                                                            24/24 
  Running scriptlet: php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            24/24 
  Verifying        : apr-1.7.5-1.amzn2023.0.4.x86_64                                                                1/24 
  Verifying        : apr-util-1.6.3-1.amzn2023.0.2.x86_64                                                           2/24 
  Verifying        : apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                                                      3/24 
  Verifying        : apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64                                                   4/24 
  Verifying        : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch                                              5/24 
  Verifying        : httpd-2.4.68-1.amzn2023.0.1.x86_64                                                             6/24 
  Verifying        : httpd-core-2.4.68-1.amzn2023.0.1.x86_64                                                        7/24 
  Verifying        : httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch                                                  8/24 
  Verifying        : httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                                                       9/24 
  Verifying        : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                                                         10/24 
  Verifying        : libsodium-1.0.19-5.amzn2023.x86_64                                                            11/24 
  Verifying        : mailcap-2.1.49-3.amzn2023.0.3.noarch                                                          12/24 
  Verifying        : mod_http2-2.0.42-1.amzn2023.0.1.x86_64                                                        13/24 
  Verifying        : mod_lua-2.4.68-1.amzn2023.0.1.x86_64                                                          14/24 
  Verifying        : nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch                                               15/24 
  Verifying        : php8.5-8.5.8-1.amzn2023.0.1.x86_64                                                            16/24 
  Verifying        : php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                                                        17/24 
  Verifying        : php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                                                     18/24 
  Verifying        : php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                                                        19/24 
  Verifying        : php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64                                                   20/24 
  Verifying        : php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                                                        21/24 
  Verifying        : php8.5-process-8.5.8-1.amzn2023.0.1.x86_64                                                    22/24 
  Verifying        : php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                                                     23/24 
  Verifying        : php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                                                        24/24 

Installed:
  apr-1.7.5-1.amzn2023.0.4.x86_64                               apr-util-1.6.3-1.amzn2023.0.2.x86_64                     
  apr-util-lmdb-1.6.3-1.amzn2023.0.2.x86_64                     apr-util-openssl-1.6.3-1.amzn2023.0.2.x86_64             
  generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch             httpd-2.4.68-1.amzn2023.0.1.x86_64                       
  httpd-core-2.4.68-1.amzn2023.0.1.x86_64                       httpd-filesystem-2.4.68-1.amzn2023.0.1.noarch            
  httpd-tools-2.4.68-1.amzn2023.0.1.x86_64                      libbrotli-1.0.9-4.amzn2023.0.2.x86_64                    
  libsodium-1.0.19-5.amzn2023.x86_64                            mailcap-2.1.49-3.amzn2023.0.3.noarch                     
  mod_http2-2.0.42-1.amzn2023.0.1.x86_64                        mod_lua-2.4.68-1.amzn2023.0.1.x86_64                     
  nginx-filesystem-1:1.30.3-1.amzn2023.0.1.noarch               php8.5-8.5.8-1.amzn2023.0.1.x86_64                       
  php8.5-cli-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-common-8.5.8-1.amzn2023.0.1.x86_64                
  php8.5-fpm-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-mbstring-8.5.8-1.amzn2023.0.1.x86_64              
  php8.5-pdo-8.5.8-1.amzn2023.0.1.x86_64                        php8.5-process-8.5.8-1.amzn2023.0.1.x86_64               
  php8.5-sodium-8.5.8-1.amzn2023.0.1.x86_64                     php8.5-xml-8.5.8-1.amzn2023.0.1.x86_64                   

Complete!
[ec2-user@ip-172-31-43-65 ~]$ sudo systemctl start httpd.service
[ec2-user@ip-172-31-43-65 ~]$ 
```

:::info
:eyes: 
Note that the very last command, the invocation of `systemctl` to start the web server on your VM, has many historic variants, a number of which will still work. For example, you can also instead type `sudo service httpd start` (this will be redirected to `systemctl`). I mention this just in case it helps correlate documentation you find on the web with your experience on EC2.
:::


At this point, the Apache web server and PHP language should be installed and enabled. Visit your VM's public IP address in a web browser, for example, I visited http://ec2-54-198-58-67.compute-1.amazonaws.com to see mine. You should see an Apache test page, but note that most browsers and links will default to offering up and/or connecting to `https:` rather than `http:`. You should double check use of `http:` if your web server test does not work. If your browser goes further to complain about the `http` website being insecure (fair enough!) and it is difficult to convince the browser to proceed anyway, or the page otherwise fails to open, it may be worth trying to use a different browser.

![image](https://hackmd.io/_uploads/BkIHXGGSfx.png)

Now let's create a PHP script to test that PHP is installed and successfully executing code. The shell invocation (on your EC2 VM) creates a PHP script at `/var/www/html/test.php`. We can use the `sudo` command to provide super-user privileges to the command following the `sudo`. However we can't usually use a form such as `sudo echo > /protected/place`, since the redirection to the file (the `> /protected/place` part) is actually handled by the shell, and is not seen by `sudo`, and thus won't be able to access protected places. The `tee` command writes to the filename it is given, as well as standard output, so we can apply `sudo` to `tee` to write to protected file locations.

```
[ec2-user@ip-172-31-43-65 ~]$ echo '<?php phpinfo(); ?>' | sudo tee /var/www/html/test.php
<?php phpinfo(); ?>
[ec2-user@ip-172-31-43-65 ~]$ 
```

Now—substituting your VM's public IP address—try to visit http://ec2-54-198-58-67.compute-1.amazonaws.com/test.php (Note that I have added `test.php` to the end of the URL we used previously---by default, the Apache web server takes the part of the URL after the slash after the DNS name, and appends that onto the web root---`/var/www` in our case---to find the filename to retrieve on the server. If a directory is selected, Apache looks for a set of default filenames, including `index.html`.)

![image](https://hackmd.io/_uploads/r1rWNMfrzl.png)

## Cloud Hygiene

We have experimented with a VM on EC2. In this case the cost of resources that we have used will be extremely small. Nonetheless the VM is still running on EC2!

It is very important that you remember to shut down VMs that you start, unless they will be doing useful work.

A good idea is to look at the dashboard any time that you expect to stop working with Amazon services, and ensure that only VMs that you expect to be running are running.

In this case, when I had finished all my experimentation with this lab exercise. I terminated the VM. You can do this from the EC2 dashboard. I happened to already be on the instance summary page for the VM I had created, and from there could use the "Instance state" button near the centre-top of the page, and from that menu choose "Terminate instance".

![image](https://hackmd.io/_uploads/B188EzMrfg.png)

You will be shown a confirmation dialogue, since in this case, terminating the instance will also destroy the hard-disk image and anything stored on our VM. This is what we expect in this case, so we can proceed by clicking "Terminate".

![image](https://hackmd.io/_uploads/SJt_4Mfrze.png)

I was then returned to the instance summary with a confirmation banner that the instance had been terminated.

![image](https://hackmd.io/_uploads/Hy6KEfMHMg.png)

Refreshing the main EC2 Dashboard should show that there are no running instances.

:::info
:eyes: 
The EC2 Dashboard may not update immediately---there is a refresh button in the top-right of the EC2 Dashboard page aligned with the "Resources" heading.
:::

![image](https://hackmd.io/_uploads/SJUs4GzBMl.png)

Amazon has clear [documentation of the instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html).

## Build your own LAMP stack

The instructions above are intended to provide enough context specific to the Owheo Building Labs to allow you to work through tutorial material on the AWS website.

Now that you've created, visited, modified and destroyed a single EC2 VM, you can move on to a more complex example that involves creating multiple VMs.

:::info
:eyes:
While you should definitely ensure that you are comfortable working with EC2, and following the documentation and tutorials that are provided, you are not *required* to complete any given proportion of their material, or the specific tutorial below. Just experiment with their services within the time that you have available in the lab. We'd be keen to hear what your experiences are. Remember that you can refer back to this lab later if you need to, of course, if it is useful for completing assignment work, for example.
:::

:::success
:pencil: 
Work through the [Amazon tutorial at that describes how to build a LAMP stack](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html) with a separate database and web server. 

Note that if you use your AWS Academy Learner Lab COSC349 credit, you may face limitations compared to if you---say---used your own personal AWS account. For example, the use of elastic IP addresses, Identity and Access Management, and virtual private cloud configuration may not work as expected. You can usually work around this sort of limitation, e.g., using the specific IP addresses allocated to your EC2 instances.
:::

:::success
:pencil: 
Using the materials in this lab, and by reading Amazon's documentation (e.g., their LAMP stack tutorial mentioned above), try to create two fresh VMs and setup EC2 to allow you to `ssh` from one to the other. Then have one VM download a web page from the other VM (e.g., using a command such as `wget` or `curl`---you may well need to look up how to use these commands). 
:::

Please seek help if you get stuck or run into knowledge gaps, and we can augment these lab exercises to support what you need to know.

## Closing down your AWS Academy Learner Lab

When you want to pause or have finished using AWS Academy Learner Lab resources, you should use the "End Lab" button on the learning management system page from which you opened the AWS Management Console:

![image](https://hackmd.io/_uploads/B11QBMGHfe.png)

You will be asked for confirmation:

![image](https://hackmd.io/_uploads/By57SMMrzg.png)

... and the AWS indicator goes from green to yellow, and eventually to red.