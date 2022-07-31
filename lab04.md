---
tags: cosc349
---
# COSC349 Lab 4—Cloud Architecture—2022

## Preliminaries for Lab 4
These preliminary steps should ideally be performed within week 2, to ensure that you are prepared for the lab in week 4... although you are indeed most welcome to start using the AWS credit on COSC349 labs and other exploration as soon as you receive it (just don't use it all up!).

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
Although we have discussed Amazon Web Services here, most of the major public cloud providers provide some sort of equivalent free tier, or academic access. Feel free to collect them all—I would be interested to hear about your experiences comparing their offerings.
:::

You should have received, to your student email address, an email that instructs you how to join AWS Academy and the Learner Lab we will use in COSC349.

## Lab 4—AWS IaaS with EC2

You should be able to complete this lab using any operating system, e.g., macOS or Linux in the CS labs. The instructions have been developed on a macOS system, though.

In this lab we will introduce use of Amazon's Elastic Compute Cloud service (EC2), which provides (among other features) Infrastructure as a Service—i.e., the provision of (headless) virtual machines of the type you have experimented with using Vagrant in [Lab 3](/bi1pAIlXT3O4WezjVtqPrA).

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

Interactive control of AWS resources can usually be achieved through use of the AWS Management Console. which is accessed through the web using a web browser.

Normally you would log into the AWS Management Console directly, but for the AWS Academy Learner Labs, we use a less direct route. First, you log into AWS Academy, accessing a learning management system (e.g., Blackboard is a LMS). A module within the learning management system contains the controls that allow you to activate and deactivate your AWS Academy Learner Lab, and to access to many useful functions including: (1) opening a new browser tab to access AWS Management Console, and (2) to allow you to retrieve AWS credentials that you can use for command line tools (we'll get to that later).

:::info
:confused: 
AWS are currently still the world's leading public cloud provider. You may then wonder why some of the AWS Academy functionality (like the AWS Educate system before it) feels technically just a bit like it is probably not world leading. The reason for this apparent inconsistency is that AWS outsource their educational programmes to be largely run and implemented by a third party: Vocareum. In past COSC349 instances, all technical wrinkles have been able to be worked around or ironed out.
:::

:::danger
:bomb:
When I was trying to use the Learner Lab module from Safari (version 15.6, on a relatively old version of macOS 10.15.7), I did not see any of the lab controls. I switched to using Chrome (version 103.0.5060.134), and everything began to work as expected. Your mileage may vary, as the saying goes...
:::

### Log into AWS Academy

As noted above, to gain access to the AWS Management Console you first need to log into [AWS Academy](http://awsacademy.instructure.com) or more specifically the [COSC349 course within AWS Academy](http://awsacademy.instructure.com/courses/23058) ... but actually you may be able to [jump straight to the Learner Lab controls](https://awsacademy.instructure.com/courses/23058/modules/items/1888693) with the COSC349 AWS Academy course.

### Reach the Learner Lab

After I had successfully logged into AWS Academy and assumed the persona of "test student", I was able to rach a page such as shown in the following screen capture:

![](https://i.imgur.com/PxyBYXq.png)

If you so choose, you can fold away the AWS Academy left menu a little bit with the arrow at the bottom of that left panel. You can also click the hamburger just to the right of the AWS logo at the top of the page to fold awy the LMS module menu. I ended up as shown:

![](https://i.imgur.com/CJnktfS.png)

From which I clicked "Start Lab" in the controls above the shell window. The AWS control should change from red to yellow... and then eventually to green:

![](https://i.imgur.com/lQtCSuD.png)

### AWS credentials for CLI tools

Not relevant for this lab (unless you want to experiment), but the Learner Lab page can provide you with the details that you need to give to command line tools (CLI) in order for those tools to be able to access AWS using your AWS Academy Learner Lab identity.

You can click on the "AWS Details" button near "Start Lab", and the panel to the rght of the shell will show details including "AWS CLI", which has a large "Show" button to its right.
![](https://i.imgur.com/tthh8Ku.png)

Click show to find out what should go into, for example, the file `~/.aws/credentials` (i.e., the `aws_access_key_id`, `aws_secret_access_key`, and `aws_session_token`).

### AWS Management Console

When your AWS indicator to the left of "Start Lab" is green, click on it to open another browser tab that will show you the AWS Management Console, logged in as your AWS Academy Learner Lab identity. I was greeted with a page that looked like: (note that you can scroll down a long way)

![](https://i.imgur.com/J4ARckA.png)

This is the AWS Management Console for the North Virginia region. AWS Academy Learner Labs run in a particular region, so you will not be able to change this. For unrestricted AWS accounts, you can change regions using the drop-down at the top-right of the window. However note that most resources have different prices in the different regions: the US East Coast regions are usually the cheapest.

One of the first points to note is that AWS provides a search-engine-style search box just to navigate within the services on offer. That should give you some idea of how many different types of services are available! (i.e., many hundreds!)

### Launch a virtual machine with EC2

At first, let's use the EC2 wizard to launch a VM. You should see this as "Launch a virtual machine" under the "Build a solution" area of the AWS Management Console page (scrroll down to find it):

![](https://i.imgur.com/UYU72IT.png)

I'm using the "new launch experience", which you should opt into if you want your testing to line up with the screen captures I've made. You should see a screen such as:

![](https://i.imgur.com/DRo2MkQ.png)

First fill in a name, I used "My test VM" (very creative, I know). Scroll down to the section entitled "Application and OS Images (Amazon Machine Image)".

Amazon Machine Images (AMIs) are akin to Vagrant "box" files or VirtualBox virtual hard disks. They are the starting point of the persistent storage of the machine.

The default choice of Amazon Linux is just fine, for our test. (More specifically this default for me reached the AMI with ID `ami-0cff7528ff583bf9a`, which is "Amazon Linux 2 Kernel 5.10 AMI 2.0.20220606.1 x86_64 HVM gp2".)

![](https://i.imgur.com/fQnVVEx.png)

Scroll down and note that the instance type is `t2.micro` which is one of the cheapest types of VMs: e.g., about 1.2 US cents per hour. (Most of your work in COSC349 should work fine on `t2.micro` instances.)

![](https://i.imgur.com/TlLBDGa.png)

Next scroll to the "Key pair (login)" section of the page (visible in the previous screen capture). AWS typically does not offer a way to create password-based access, e.g., to SSH to your EC2 VMs. Instead public key authentication is used, where you receive a private key in a file, and Amazon (and anyone else) can view the public key.

If you are first accessing AWS, you will need to use the option to the right to "Create new key pair". (If you have already registered key pairs, you can select them from the drop-down box.)

![](https://i.imgur.com/RcslDk0.png)

I recomment using a name without spaces, such as `cosc349-2022`. RSA keys should be OK. Most variants of SSH should work with the `.pem` format---the default---however if you are ussing PuTTY on Windows, instead choose the `.ppk` format.

:::info
:face_palm: 
This year (2022) I actually left a space in `cosc349 2022` by accident. Some evidence of that mistake is contained in the screen captures, but I've tried to fix the command invocations...
:::

Finally, select "Create key pair", and note that your selected type of file (I chose `.pem` will be downloaded by your web browser). This is your private key (file) for the named key pair.

Click "Download Key Pair", and m
Move the private key file to somewhere safe, and make the file private. On a CS Lab machine, a good place to move it to is the `.ssh` subdirectory of your home directory (`~/.ssh`). This is a conventional place to store encryption keys used for SSH (and the default place that must SSH software looks for keys).

:::warning
:warning: 
Note that it's possible that you do not yet have an `.ssh` directory in your home-directory. You can check—
```
$ ls -ld ~/.ssh
drwx------  34 dme  11306  1088  3 Aug 16:08 /Users/dme/.ssh
```
—and if the `ls` command fails, create the directory and set the permissions appropriately (SSH will refuse to use files from directories that are not sufficiently protected) using the command `mkdir -m 700 ~/.ssh` (the `-m` option sets the access control bits on that directory, just as is done when using the `chmod` command).
:::

You may need to rename the key file that was downloaded by my web-browser to remove the `.txt` extension if there is one. I changed the permissions to make the file only readable by my user using the following shell invocations:

```
$ mv ~/Downloads/cosc349-2022.pem ~/.ssh/cosc349-2022.pem
$ chmod 400 ~/.ssh/cosc349-2022.pem 
$ ls -l ~/.ssh/cosc349-2022.pem 
-r--------@ 1 dme  11306  1704  3 Aug 16:07 /Users/dme/.ssh/cosc349-2022.pem
```

Next, scroll down to the "Network settings" controls.

![](https://i.imgur.com/DEqHUDj.png)

The default to create a security group (as noted, a security group is essentially a set of firewall rules), which is fine.

The default to "Allow SSH from ... Anywhere", is fine for our purposes. For production EC2 VMs, best practice would involve narrowomg down the set of IP addresses that are permitted to SSH to the EC2 VM.

Change the "Allow HTTPs ..." and "Allow HTTP ..." traffic options to be ticked (two checks).

Then scroll down to the "Configure storage" section of the page.

![](https://i.imgur.com/5mRvtIi.png)

The default, to create a single 8 GiB `gp2` disk is fine. As you can determine from the pull-down option, `gp2` is a type of SSD storage that AWS offers. There are other choices, most of which will be more expensive per hour, but will have higer performance.

:::info
:eyes:
Note that the free tier message mentions "EBS", i.e. AWS Elastic Block Storage. EBS volumes are actually accessed over the AWS datacentre network: they are not hard disks directly attached to the physical computer running your EC2 VM. The default settings here will delete your EBS volume when your VM is terminated, but this is not the only way to configure EBS to work. Also, EBS always uses redundant hardware, so no EBS volume will be lost if a single hardware device, such as a hard disk, fails.
:::

You can skip the "Advanced details" part of the page, which is hidden by default, anyway.

Scrolling to the "Summary" section, I see something like:

![](https://i.imgur.com/N5yqUNF.png)

If everything looks good, you should select the "Launch Instance" button, to create and start your EC2 VM.

### Success launching your VM

Hopefully you successfully launch your VM, and see output similar to:

![](https://i.imgur.com/zfdIAyQ.png)

Now return to the EC2 Dashboard (the topmost link on the left menu).

## EC2 Dashboard

You should now be viewing the EC2 dashboard. 

![](https://i.imgur.com/GMoGxIt.png)

I clicked on "Instances (running)" to see a list of EC2 VMs that have been launched (i.e., our test VM).

You can click the checkbox next to your instance to view its details in the information panel in the lower half of the web page. This area includes details such as your public and private IPv4 addresses, and your "Public IPv4 DNS".

![](https://i.imgur.com/q2zNw0r.png)

## Connect to your VM using SSH

If you click the "Connect" button (top of page) after choosing your instance (it will be selected by default if there is only one instance), you get a choice of four different ways to open an SSH connection to your instance.

The "EC2 Instance Connect" panel tab allows you to open a JavaScript-driven SSH connection within your web browser. Click "Connect" to open the connect.

![](https://i.imgur.com/81yEBW9.png)

In the past this type of AWS EC2 web shell did not work for me, but it worked fine this year, as shown in the following screen capture. (Possibly Chrome has always worked better than Safari for this sort of AWS functionality?). Feel free to test that this works, although we will not be using this route of access in the rest of the lab.

![](https://i.imgur.com/Jp6UCun.png)

The "A standalone SSH client" panel tab does not actually allow you to connect directly from the web browser (you need to use a standalone SSH client, such as the `ssh` command in your shell (which is usually OpenSSH or similar), or a GUI program such as PuTTY on Microsoft Windows), but it does provide some useful instructions. You can click "Close" when you have read through the information presented, as specific instructions will be presented below.

:::info
:eyes: 
You may see slightly different information from what's shown in the following screen capture: for example, the DNS records for EC2 VMs may not be immediatly ready, and in those cases IP addresses will be provided instead of DNS names.

You can read up on the Domain Name System (DNS) but essentially DNS provides a mapping from names to IP addresses. So `www.google.co.nz` is a DNS name, and on pretty much any Linux / macOS / Windows terminal or command prompt, if you run the command `nslookup www.google.co.nz` then you will be told the IP address for that DNS name, `172.217.167.67`. Web browsers will happily reach the CS site with either form, but `www.google.co.nz` is easier for humans to work with, since the name encodes semantics. (Actually, this is not entirely true---it may be that multiple DNS names point to the same IP address, which allows one webserver to serve up pages for multiple different websites, and thus if you visit the site by its IP address alone, the webserver will need to pick a default site for you to visit, of the possible choices.)
:::

![](https://i.imgur.com/geJ4THH.png)

Note the public DNS address—in my case `ec2-54-221-79-179.compute-1.amazonaws.com`. It will almost certainly be different for you, and you will need to replace the DNS address of my EC2 instance with that of yours throughout the instructions below.

### Your VM's SSH host key

For security best practice, you should find out what the host (i.e., VM's) key fingerprint is, before you trust connecting to it using SSH (which is what you do in the next section, below).

(In practice, it is highly unlikely that a person-in-the-middle attack will have been set up for a VM that you just created, and it is also unlikely that you will place material that you treat as sensitive on a VM for doing COSC349 lab exercises anyway. However it's ideal to apply good security practices.)

You can determine the host key by looking at the server logs, in this case the console output captured from the VM as it booted up (that is when it created the host key).

![](https://i.imgur.com/YnYxDE7.png)

Keep the page like that in the following screen capture open so that when you SSH to your VM, you can check the VM's host key against the keys shown in the system log. (The screen capture's log output is scrolled to the right, to see the keys.)

![](https://i.imgur.com/LqCexj7.png)

### Failing to connect to your VM

First, let's try to connect to the EC2 VM in a way that will fail. Note that the first connection has prompted us to check the host key fingerprint. If you perform the steps described above, you can check that this really is your server that you're connecting to, as the long string starting `SHA256:` will match. (Although to be honest, the majority of users, globally, probably just type "yes" without checking, even though this is fundamentally insecure...)


```
$ ssh ec2-user@ec2-54-221-79-179.compute-1.amazonaws.com
The authenticity of host 'ec2-54-221-79-179.compute-1.amazonaws.com (54.221.79.179)' can't be established.
ECDSA key fingerprint is SHA256:pZ1EQiABK+6u+R2x7CehS6OsS3RENawT+Yk5q3xYIQs.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-54-221-79-179.compute-1.amazonaws.com,54.221.79.179' (ECDSA) to the list of known hosts.
ec2-user@ec2-54-221-79-179.compute-1.amazonaws.com: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```

This failed because the SSH server on your EC2 VM running Amazon Linux does not accept password logins, and yet we did not provide our private key to SSH so that it could use key-pair authentication.

### Successfully connecting to your VM

This time, let's specify the key-pair file that we downloaded when we set up the EC2 instance. You should be able to log in. You will see a command prompt similar to that shown below. As I have done, you can try running the `uname -a` command to request information about the running operating system. I then log out. (Typing <kbd>control</kbd><kbd>d</kbd> works to log out, too.)

```
$ ssh -i ~/.ssh/cosc349-2022.pem ec2-user@ec2-54-221-79-179.compute-1.amazonaws.com
Last login: Sun Jul 31 03:32:21 2022 from ec2-18-206-107-28.compute-1.amazonaws.com

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
12 package(s) needed for security, out of 22 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-31-23-180 ~]$ uname -a
Linux ip-172-31-23-180.ec2.internal 5.10.118-111.515.amzn2.x86_64 #1 SMP Wed May 25 22:12:19 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
[ec2-user@ip-172-31-23-180 ~]$ logout
Connection to ec2-54-221-79-179.compute-1.amazonaws.com closed.
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
There are multiple commands **within** the transcript below: you need to read through what's shown to find them. (Or alternatively you could search for a constant prefix of my shell prompt string `[ec2-user@ip-172-31-23-180` to find each of them.)
:::

```
$ ssh -i ~/.ssh/cosc349-2022.pem ec2-user@ec2-54-221-79-179.compute-1.amazonaws.com
Last login: Sun Jul 31 03:41:22 2022 from 119.224.98.119

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
12 package(s) needed for security, out of 22 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-31-23-180 ~]$ sudo yum update -y
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                               | 3.7 kB     00:00
Resolving Dependencies
--> Running transaction check
---> Package amazon-ssm-agent.x86_64 0:3.1.1188.0-1.amzn2 will be updated
---> Package amazon-ssm-agent.x86_64 0:3.1.1575.0-1.amzn2 will be an update
---> Package curl.x86_64 0:7.79.1-2.amzn2.0.1 will be updated
---> Package curl.x86_64 0:7.79.1-4.amzn2.0.1 will be an update
---> Package expat.x86_64 0:2.1.0-12.amzn2.0.4 will be updated
---> Package expat.x86_64 0:2.1.0-14.amzn2.0.1 will be an update
---> Package glibc.x86_64 0:2.26-58.amzn2 will be updated
---> Package glibc.x86_64 0:2.26-59.amzn2 will be an update
---> Package glibc-all-langpacks.x86_64 0:2.26-58.amzn2 will be updated
---> Package glibc-all-langpacks.x86_64 0:2.26-59.amzn2 will be an update
---> Package glibc-common.x86_64 0:2.26-58.amzn2 will be updated
---> Package glibc-common.x86_64 0:2.26-59.amzn2 will be an update
---> Package glibc-locale-source.x86_64 0:2.26-58.amzn2 will be updated
---> Package glibc-locale-source.x86_64 0:2.26-59.amzn2 will be an update
---> Package glibc-minimal-langpack.x86_64 0:2.26-58.amzn2 will be updated
---> Package glibc-minimal-langpack.x86_64 0:2.26-59.amzn2 will be an update
---> Package initscripts.x86_64 0:9.49.47-1.amzn2.0.1 will be updated
---> Package initscripts.x86_64 0:9.49.47-1.amzn2.0.2 will be an update
---> Package iproute.x86_64 0:5.10.0-2.amzn2.0.2 will be updated
---> Package iproute.x86_64 0:5.10.0-2.amzn2.0.3 will be an update
---> Package kernel.x86_64 0:5.10.130-118.517.amzn2 will be installed
---> Package libcrypt.x86_64 0:2.26-58.amzn2 will be updated
---> Package libcrypt.x86_64 0:2.26-59.amzn2 will be an update
---> Package libcurl.x86_64 0:7.79.1-2.amzn2.0.1 will be updated
---> Package libcurl.x86_64 0:7.79.1-4.amzn2.0.1 will be an update
---> Package libxml2.x86_64 0:2.9.1-6.amzn2.5.4 will be updated
---> Package libxml2.x86_64 0:2.9.1-6.amzn2.5.5 will be an update
---> Package libxml2-python.x86_64 0:2.9.1-6.amzn2.5.4 will be updated
---> Package libxml2-python.x86_64 0:2.9.1-6.amzn2.5.5 will be an update
---> Package systemtap-runtime.x86_64 0:4.4-1.amzn2.0.2 will be updated
---> Package systemtap-runtime.x86_64 0:4.5-1.amzn2.0.1 will be an update
---> Package vim-common.x86_64 2:8.2.4857-1.amzn2.0.1 will be updated
---> Package vim-common.x86_64 2:8.2.5172-1.amzn2.0.1 will be an update
---> Package vim-data.noarch 2:8.2.4857-1.amzn2.0.1 will be updated
---> Package vim-data.noarch 2:8.2.5172-1.amzn2.0.1 will be an update
---> Package vim-enhanced.x86_64 2:8.2.4857-1.amzn2.0.1 will be updated
---> Package vim-enhanced.x86_64 2:8.2.5172-1.amzn2.0.1 will be an update
---> Package vim-filesystem.noarch 2:8.2.4857-1.amzn2.0.1 will be updated
---> Package vim-filesystem.noarch 2:8.2.5172-1.amzn2.0.1 will be an update
---> Package vim-minimal.x86_64 2:8.2.4857-1.amzn2.0.1 will be updated
---> Package vim-minimal.x86_64 2:8.2.5172-1.amzn2.0.1 will be an update
---> Package yum.noarch 0:3.4.3-158.amzn2.0.5 will be updated
---> Package yum.noarch 0:3.4.3-158.amzn2.0.6 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package             Arch   Version                Repository              Size
================================================================================
Installing:
 kernel              x86_64 5.10.130-118.517.amzn2 amzn2extra-kernel-5.10  31 M
Updating:
 amazon-ssm-agent    x86_64 3.1.1575.0-1.amzn2     amzn2-core              24 M
 curl                x86_64 7.79.1-4.amzn2.0.1     amzn2-core             360 k
 expat               x86_64 2.1.0-14.amzn2.0.1     amzn2-core              89 k
 glibc               x86_64 2.26-59.amzn2          amzn2-core             3.4 M
 glibc-all-langpacks x86_64 2.26-59.amzn2          amzn2-core             7.0 M
 glibc-common        x86_64 2.26-59.amzn2          amzn2-core             773 k
 glibc-locale-source x86_64 2.26-59.amzn2          amzn2-core             3.2 M
 glibc-minimal-langpack
                     x86_64 2.26-59.amzn2          amzn2-core              32 k
 initscripts         x86_64 9.49.47-1.amzn2.0.2    amzn2-core             440 k
 iproute             x86_64 5.10.0-2.amzn2.0.3     amzn2-core             649 k
 libcrypt            x86_64 2.26-59.amzn2          amzn2-core              52 k
 libcurl             x86_64 7.79.1-4.amzn2.0.1     amzn2-core             321 k
 libxml2             x86_64 2.9.1-6.amzn2.5.5      amzn2-core             660 k
 libxml2-python      x86_64 2.9.1-6.amzn2.5.5      amzn2-core             246 k
 systemtap-runtime   x86_64 4.5-1.amzn2.0.1        amzn2-core             475 k
 vim-common          x86_64 2:8.2.5172-1.amzn2.0.1 amzn2-core             7.7 M
 vim-data            noarch 2:8.2.5172-1.amzn2.0.1 amzn2-core              76 k
 vim-enhanced        x86_64 2:8.2.5172-1.amzn2.0.1 amzn2-core             1.7 M
 vim-filesystem      noarch 2:8.2.5172-1.amzn2.0.1 amzn2-core              71 k
 vim-minimal         x86_64 2:8.2.5172-1.amzn2.0.1 amzn2-core             705 k
 yum                 noarch 3.4.3-158.amzn2.0.6    amzn2-core             1.2 M

Transaction Summary
================================================================================
Install   1 Package
Upgrade  21 Packages

Total download size: 84 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/22): curl-7.79.1-4.amzn2.0.1.x86_64.rpm                 | 360 kB   00:00
(2/22): expat-2.1.0-14.amzn2.0.1.x86_64.rpm                |  89 kB   00:00
(3/22): glibc-2.26-59.amzn2.x86_64.rpm                     | 3.4 MB   00:00
(4/22): glibc-all-langpacks-2.26-59.amzn2.x86_64.rpm       | 7.0 MB   00:00
(5/22): glibc-common-2.26-59.amzn2.x86_64.rpm              | 773 kB   00:00
(6/22): glibc-locale-source-2.26-59.amzn2.x86_64.rpm       | 3.2 MB   00:00
(7/22): glibc-minimal-langpack-2.26-59.amzn2.x86_64.rpm    |  32 kB   00:00
(8/22): initscripts-9.49.47-1.amzn2.0.2.x86_64.rpm         | 440 kB   00:00
(9/22): iproute-5.10.0-2.amzn2.0.3.x86_64.rpm              | 649 kB   00:00
(10/22): libcrypt-2.26-59.amzn2.x86_64.rpm                 |  52 kB   00:00
(11/22): libcurl-7.79.1-4.amzn2.0.1.x86_64.rpm             | 321 kB   00:00
(12/22): libxml2-2.9.1-6.amzn2.5.5.x86_64.rpm              | 660 kB   00:00
(13/22): libxml2-python-2.9.1-6.amzn2.5.5.x86_64.rpm       | 246 kB   00:00
(14/22): systemtap-runtime-4.5-1.amzn2.0.1.x86_64.rpm      | 475 kB   00:00
(15/22): amazon-ssm-agent-3.1.1575.0-1.amzn2.x86_64.rpm    |  24 MB   00:01
(16/22): vim-data-8.2.5172-1.amzn2.0.1.noarch.rpm          |  76 kB   00:00
(17/22): vim-common-8.2.5172-1.amzn2.0.1.x86_64.rpm        | 7.7 MB   00:00
(18/22): vim-enhanced-8.2.5172-1.amzn2.0.1.x86_64.rpm      | 1.7 MB   00:00
(19/22): vim-filesystem-8.2.5172-1.amzn2.0.1.noarch.rpm    |  71 kB   00:00
(20/22): vim-minimal-8.2.5172-1.amzn2.0.1.x86_64.rpm       | 705 kB   00:00
(21/22): yum-3.4.3-158.amzn2.0.6.noarch.rpm                | 1.2 MB   00:00
(22/22): kernel-5.10.130-118.517.amzn2.x86_64.rpm          |  31 MB   00:01
--------------------------------------------------------------------------------
Total                                               35 MB/s |  84 MB  00:02
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 2:vim-data-8.2.5172-1.amzn2.0.1.noarch                      1/43
  Updating   : glibc-common-2.26-59.amzn2.x86_64                           2/43
  Updating   : glibc-2.26-59.amzn2.x86_64                                  3/43
  Updating   : glibc-minimal-langpack-2.26-59.amzn2.x86_64                 4/43
  Updating   : libcurl-7.79.1-4.amzn2.0.1.x86_64                           5/43
  Updating   : libxml2-2.9.1-6.amzn2.5.5.x86_64                            6/43
  Updating   : iproute-5.10.0-2.amzn2.0.3.x86_64                           7/43
  Updating   : initscripts-9.49.47-1.amzn2.0.2.x86_64                      8/43
  Updating   : 2:vim-filesystem-8.2.5172-1.amzn2.0.1.noarch                9/43
  Updating   : 2:vim-common-8.2.5172-1.amzn2.0.1.x86_64                   10/43
  Updating   : 2:vim-enhanced-8.2.5172-1.amzn2.0.1.x86_64                 11/43
  Installing : kernel-5.10.130-118.517.amzn2.x86_64                       12/43
  Updating   : libxml2-python-2.9.1-6.amzn2.5.5.x86_64                    13/43
  Updating   : curl-7.79.1-4.amzn2.0.1.x86_64                             14/43
  Updating   : glibc-all-langpacks-2.26-59.amzn2.x86_64                   15/43
  Updating   : expat-2.1.0-14.amzn2.0.1.x86_64                            16/43
  Updating   : 2:vim-minimal-8.2.5172-1.amzn2.0.1.x86_64                  17/43
  Updating   : systemtap-runtime-4.5-1.amzn2.0.1.x86_64                   18/43
  Updating   : glibc-locale-source-2.26-59.amzn2.x86_64                   19/43
  Updating   : libcrypt-2.26-59.amzn2.x86_64                              20/43
  Updating   : amazon-ssm-agent-3.1.1575.0-1.amzn2.x86_64                 21/43
  Updating   : yum-3.4.3-158.amzn2.0.6.noarch                             22/43
  Cleanup    : glibc-locale-source-2.26-58.amzn2.x86_64                   23/43
  Cleanup    : glibc-all-langpacks-2.26-58.amzn2.x86_64                   24/43
  Cleanup    : amazon-ssm-agent-3.1.1188.0-1.amzn2.x86_64                 25/43
  Cleanup    : yum-3.4.3-158.amzn2.0.5.noarch                             26/43
  Cleanup    : libxml2-python-2.9.1-6.amzn2.5.4.x86_64                    27/43
  Cleanup    : curl-7.79.1-2.amzn2.0.1.x86_64                             28/43
  Cleanup    : 2:vim-enhanced-8.2.4857-1.amzn2.0.1.x86_64                 29/43
  Cleanup    : 2:vim-common-8.2.4857-1.amzn2.0.1.x86_64                   30/43
  Cleanup    : 2:vim-minimal-8.2.4857-1.amzn2.0.1.x86_64                  31/43
  Cleanup    : initscripts-9.49.47-1.amzn2.0.1.x86_64                     32/43
  Cleanup    : iproute-5.10.0-2.amzn2.0.2.x86_64                          33/43
  Cleanup    : libcurl-7.79.1-2.amzn2.0.1.x86_64                          34/43
  Cleanup    : libxml2-2.9.1-6.amzn2.5.4.x86_64                           35/43
  Cleanup    : libcrypt-2.26-58.amzn2.x86_64                              36/43
  Cleanup    : systemtap-runtime-4.4-1.amzn2.0.2.x86_64                   37/43
  Cleanup    : expat-2.1.0-12.amzn2.0.4.x86_64                            38/43
  Cleanup    : 2:vim-data-8.2.4857-1.amzn2.0.1.noarch                     39/43
  Cleanup    : 2:vim-filesystem-8.2.4857-1.amzn2.0.1.noarch               40/43
  Cleanup    : glibc-common-2.26-58.amzn2.x86_64                          41/43
  Cleanup    : glibc-minimal-langpack-2.26-58.amzn2.x86_64                42/43
  Cleanup    : glibc-2.26-58.amzn2.x86_64                                 43/43
/sbin/dracut: line 655: warning: setlocale: LC_MESSAGES: cannot change locale (en_US.UTF-8): No such file or directory
/sbin/dracut: line 656: warning: setlocale: LC_CTYPE: cannot change locale (en_US.UTF-8): No such file or directory
/sbin/dracut: line 656: warning: setlocale: LC_CTYPE: cannot change locale (en_US.UTF-8)
  Verifying  : libcurl-7.79.1-4.amzn2.0.1.x86_64                           1/43
  Verifying  : yum-3.4.3-158.amzn2.0.6.noarch                              2/43
  Verifying  : initscripts-9.49.47-1.amzn2.0.2.x86_64                      3/43
  Verifying  : kernel-5.10.130-118.517.amzn2.x86_64                        4/43
  Verifying  : glibc-all-langpacks-2.26-59.amzn2.x86_64                    5/43
  Verifying  : expat-2.1.0-14.amzn2.0.1.x86_64                             6/43
  Verifying  : curl-7.79.1-4.amzn2.0.1.x86_64                              7/43
  Verifying  : libxml2-2.9.1-6.amzn2.5.5.x86_64                            8/43
  Verifying  : libxml2-python-2.9.1-6.amzn2.5.5.x86_64                     9/43
  Verifying  : glibc-minimal-langpack-2.26-59.amzn2.x86_64                10/43
  Verifying  : amazon-ssm-agent-3.1.1575.0-1.amzn2.x86_64                 11/43
  Verifying  : 2:vim-minimal-8.2.5172-1.amzn2.0.1.x86_64                  12/43
  Verifying  : glibc-common-2.26-59.amzn2.x86_64                          13/43
  Verifying  : 2:vim-data-8.2.5172-1.amzn2.0.1.noarch                     14/43
  Verifying  : 2:vim-enhanced-8.2.5172-1.amzn2.0.1.x86_64                 15/43
  Verifying  : 2:vim-filesystem-8.2.5172-1.amzn2.0.1.noarch               16/43
  Verifying  : iproute-5.10.0-2.amzn2.0.3.x86_64                          17/43
  Verifying  : glibc-2.26-59.amzn2.x86_64                                 18/43
  Verifying  : 2:vim-common-8.2.5172-1.amzn2.0.1.x86_64                   19/43
  Verifying  : systemtap-runtime-4.5-1.amzn2.0.1.x86_64                   20/43
  Verifying  : glibc-locale-source-2.26-59.amzn2.x86_64                   21/43
  Verifying  : libcrypt-2.26-59.amzn2.x86_64                              22/43
  Verifying  : yum-3.4.3-158.amzn2.0.5.noarch                             23/43
  Verifying  : systemtap-runtime-4.4-1.amzn2.0.2.x86_64                   24/43
  Verifying  : 2:vim-enhanced-8.2.4857-1.amzn2.0.1.x86_64                 25/43
  Verifying  : libxml2-python-2.9.1-6.amzn2.5.4.x86_64                    26/43
  Verifying  : libxml2-2.9.1-6.amzn2.5.4.x86_64                           27/43
  Verifying  : expat-2.1.0-12.amzn2.0.4.x86_64                            28/43
  Verifying  : iproute-5.10.0-2.amzn2.0.2.x86_64                          29/43
  Verifying  : 2:vim-data-8.2.4857-1.amzn2.0.1.noarch                     30/43
  Verifying  : glibc-2.26-58.amzn2.x86_64                                 31/43
  Verifying  : curl-7.79.1-2.amzn2.0.1.x86_64                             32/43
  Verifying  : libcurl-7.79.1-2.amzn2.0.1.x86_64                          33/43
  Verifying  : glibc-locale-source-2.26-58.amzn2.x86_64                   34/43
  Verifying  : glibc-minimal-langpack-2.26-58.amzn2.x86_64                35/43
  Verifying  : glibc-common-2.26-58.amzn2.x86_64                          36/43
  Verifying  : 2:vim-filesystem-8.2.4857-1.amzn2.0.1.noarch               37/43
  Verifying  : 2:vim-minimal-8.2.4857-1.amzn2.0.1.x86_64                  38/43
  Verifying  : 2:vim-common-8.2.4857-1.amzn2.0.1.x86_64                   39/43
  Verifying  : glibc-all-langpacks-2.26-58.amzn2.x86_64                   40/43
  Verifying  : amazon-ssm-agent-3.1.1188.0-1.amzn2.x86_64                 41/43
  Verifying  : libcrypt-2.26-58.amzn2.x86_64                              42/43
  Verifying  : initscripts-9.49.47-1.amzn2.0.1.x86_64                     43/43

Installed:
  kernel.x86_64 0:5.10.130-118.517.amzn2

Updated:
  amazon-ssm-agent.x86_64 0:3.1.1575.0-1.amzn2
  curl.x86_64 0:7.79.1-4.amzn2.0.1
  expat.x86_64 0:2.1.0-14.amzn2.0.1
  glibc.x86_64 0:2.26-59.amzn2
  glibc-all-langpacks.x86_64 0:2.26-59.amzn2
  glibc-common.x86_64 0:2.26-59.amzn2
  glibc-locale-source.x86_64 0:2.26-59.amzn2
  glibc-minimal-langpack.x86_64 0:2.26-59.amzn2
  initscripts.x86_64 0:9.49.47-1.amzn2.0.2
  iproute.x86_64 0:5.10.0-2.amzn2.0.3
  libcrypt.x86_64 0:2.26-59.amzn2
  libcurl.x86_64 0:7.79.1-4.amzn2.0.1
  libxml2.x86_64 0:2.9.1-6.amzn2.5.5
  libxml2-python.x86_64 0:2.9.1-6.amzn2.5.5
  systemtap-runtime.x86_64 0:4.5-1.amzn2.0.1
  vim-common.x86_64 2:8.2.5172-1.amzn2.0.1
  vim-data.noarch 2:8.2.5172-1.amzn2.0.1
  vim-enhanced.x86_64 2:8.2.5172-1.amzn2.0.1
  vim-filesystem.noarch 2:8.2.5172-1.amzn2.0.1
  vim-minimal.x86_64 2:8.2.5172-1.amzn2.0.1
  yum.noarch 0:3.4.3-158.amzn2.0.6

Complete!
[ec2-user@ip-172-31-23-180 ~]$ sudo yum install -y httpd php
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.54-1.amzn2 will be installed
--> Processing Dependency: httpd-tools = 2.4.54-1.amzn2 for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: httpd-filesystem = 2.4.54-1.amzn2 for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: system-logos-httpd for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: mod_http2 for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: httpd-filesystem for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: /etc/mime.types for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.54-1.amzn2.x86_64
--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.54-1.amzn2.x86_64
---> Package php.x86_64 0:5.4.16-46.amzn2.0.2 will be installed
--> Processing Dependency: php-cli(x86-64) = 5.4.16-46.amzn2.0.2 for package: php-5.4.16-46.amzn2.0.2.x86_64
--> Processing Dependency: php-common(x86-64) = 5.4.16-46.amzn2.0.2 for package: php-5.4.16-46.amzn2.0.2.x86_64
--> Running transaction check
---> Package apr.x86_64 0:1.7.0-9.amzn2 will be installed
---> Package apr-util.x86_64 0:1.6.1-5.amzn2.0.2 will be installed
--> Processing Dependency: apr-util-bdb(x86-64) = 1.6.1-5.amzn2.0.2 for package: apr-util-1.6.1-5.amzn2.0.2.x86_64
---> Package generic-logos-httpd.noarch 0:18.0.0-4.amzn2 will be installed
---> Package httpd-filesystem.noarch 0:2.4.54-1.amzn2 will be installed
---> Package httpd-tools.x86_64 0:2.4.54-1.amzn2 will be installed
---> Package mailcap.noarch 0:2.1.41-2.amzn2 will be installed
---> Package mod_http2.x86_64 0:1.15.19-1.amzn2.0.1 will be installed
---> Package php-cli.x86_64 0:5.4.16-46.amzn2.0.2 will be installed
---> Package php-common.x86_64 0:5.4.16-46.amzn2.0.2 will be installed
--> Processing Dependency: libzip.so.2()(64bit) for package: php-common-5.4.16-46.amzn2.0.2.x86_64
--> Running transaction check
---> Package apr-util-bdb.x86_64 0:1.6.1-5.amzn2.0.2 will be installed
---> Package libzip010-compat.x86_64 0:0.10.1-9.amzn2.0.5 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                Arch      Version                   Repository     Size
================================================================================
Installing:
 httpd                  x86_64    2.4.54-1.amzn2            amzn2-core    1.4 M
 php                    x86_64    5.4.16-46.amzn2.0.2       amzn2-core    1.4 M
Installing for dependencies:
 apr                    x86_64    1.7.0-9.amzn2             amzn2-core    122 k
 apr-util               x86_64    1.6.1-5.amzn2.0.2         amzn2-core     99 k
 apr-util-bdb           x86_64    1.6.1-5.amzn2.0.2         amzn2-core     19 k
 generic-logos-httpd    noarch    18.0.0-4.amzn2            amzn2-core     19 k
 httpd-filesystem       noarch    2.4.54-1.amzn2            amzn2-core     24 k
 httpd-tools            x86_64    2.4.54-1.amzn2            amzn2-core     88 k
 libzip010-compat       x86_64    0.10.1-9.amzn2.0.5        amzn2-core     30 k
 mailcap                noarch    2.1.41-2.amzn2            amzn2-core     31 k
 mod_http2              x86_64    1.15.19-1.amzn2.0.1       amzn2-core    149 k
 php-cli                x86_64    5.4.16-46.amzn2.0.2       amzn2-core    2.8 M
 php-common             x86_64    5.4.16-46.amzn2.0.2       amzn2-core    563 k

Transaction Summary
================================================================================
Install  2 Packages (+11 Dependent packages)

Total download size: 6.6 M
Installed size: 22 M
Downloading packages:
(1/13): apr-util-1.6.1-5.amzn2.0.2.x86_64.rpm              |  99 kB   00:00
(2/13): apr-1.7.0-9.amzn2.x86_64.rpm                       | 122 kB   00:00
(3/13): apr-util-bdb-1.6.1-5.amzn2.0.2.x86_64.rpm          |  19 kB   00:00
(4/13): generic-logos-httpd-18.0.0-4.amzn2.noarch.rpm      |  19 kB   00:00
(5/13): httpd-filesystem-2.4.54-1.amzn2.noarch.rpm         |  24 kB   00:00
(6/13): httpd-tools-2.4.54-1.amzn2.x86_64.rpm              |  88 kB   00:00
(7/13): libzip010-compat-0.10.1-9.amzn2.0.5.x86_64.rpm     |  30 kB   00:00
(8/13): httpd-2.4.54-1.amzn2.x86_64.rpm                    | 1.4 MB   00:00
(9/13): mod_http2-1.15.19-1.amzn2.0.1.x86_64.rpm           | 149 kB   00:00
(10/13): mailcap-2.1.41-2.amzn2.noarch.rpm                 |  31 kB   00:00
(11/13): php-5.4.16-46.amzn2.0.2.x86_64.rpm                | 1.4 MB   00:00
(12/13): php-common-5.4.16-46.amzn2.0.2.x86_64.rpm         | 563 kB   00:00
(13/13): php-cli-5.4.16-46.amzn2.0.2.x86_64.rpm            | 2.8 MB   00:00
--------------------------------------------------------------------------------
Total                                               20 MB/s | 6.6 MB  00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : apr-1.7.0-9.amzn2.x86_64                                    1/13
  Installing : apr-util-bdb-1.6.1-5.amzn2.0.2.x86_64                       2/13
  Installing : apr-util-1.6.1-5.amzn2.0.2.x86_64                           3/13
  Installing : httpd-tools-2.4.54-1.amzn2.x86_64                           4/13
  Installing : httpd-filesystem-2.4.54-1.amzn2.noarch                      5/13
  Installing : generic-logos-httpd-18.0.0-4.amzn2.noarch                   6/13
  Installing : mailcap-2.1.41-2.amzn2.noarch                               7/13
  Installing : mod_http2-1.15.19-1.amzn2.0.1.x86_64                        8/13
  Installing : httpd-2.4.54-1.amzn2.x86_64                                 9/13
  Installing : libzip010-compat-0.10.1-9.amzn2.0.5.x86_64                 10/13
  Installing : php-common-5.4.16-46.amzn2.0.2.x86_64                      11/13
  Installing : php-cli-5.4.16-46.amzn2.0.2.x86_64                         12/13
  Installing : php-5.4.16-46.amzn2.0.2.x86_64                             13/13
  Verifying  : apr-util-1.6.1-5.amzn2.0.2.x86_64                           1/13
  Verifying  : libzip010-compat-0.10.1-9.amzn2.0.5.x86_64                  2/13
  Verifying  : apr-util-bdb-1.6.1-5.amzn2.0.2.x86_64                       3/13
  Verifying  : php-cli-5.4.16-46.amzn2.0.2.x86_64                          4/13
  Verifying  : httpd-tools-2.4.54-1.amzn2.x86_64                           5/13
  Verifying  : php-5.4.16-46.amzn2.0.2.x86_64                              6/13
  Verifying  : mod_http2-1.15.19-1.amzn2.0.1.x86_64                        7/13
  Verifying  : httpd-2.4.54-1.amzn2.x86_64                                 8/13
  Verifying  : mailcap-2.1.41-2.amzn2.noarch                               9/13
  Verifying  : generic-logos-httpd-18.0.0-4.amzn2.noarch                  10/13
  Verifying  : httpd-filesystem-2.4.54-1.amzn2.noarch                     11/13
  Verifying  : apr-1.7.0-9.amzn2.x86_64                                   12/13
  Verifying  : php-common-5.4.16-46.amzn2.0.2.x86_64                      13/13

Installed:
  httpd.x86_64 0:2.4.54-1.amzn2         php.x86_64 0:5.4.16-46.amzn2.0.2

Dependency Installed:
  apr.x86_64 0:1.7.0-9.amzn2
  apr-util.x86_64 0:1.6.1-5.amzn2.0.2
  apr-util-bdb.x86_64 0:1.6.1-5.amzn2.0.2
  generic-logos-httpd.noarch 0:18.0.0-4.amzn2
  httpd-filesystem.noarch 0:2.4.54-1.amzn2
  httpd-tools.x86_64 0:2.4.54-1.amzn2
  libzip010-compat.x86_64 0:0.10.1-9.amzn2.0.5
  mailcap.noarch 0:2.1.41-2.amzn2
  mod_http2.x86_64 0:1.15.19-1.amzn2.0.1
  php-cli.x86_64 0:5.4.16-46.amzn2.0.2
  php-common.x86_64 0:5.4.16-46.amzn2.0.2

Complete!
[ec2-user@ip-172-31-23-180 ~]$ sudo systemctl start httpd.service
```

:::info
:eyes: 
Note that the very last command, the invocation of `systemctl` to start the web server on your VM, has many historic variants, a number of which will still work. For example, you can also instead type `sudo service httpd start` (this will be redirected to `systemctl`). I mention this just in case it helps correlate documentation you find on the web with your experience on EC2.
:::


At this point, the Apache web server and PHP language should be installed and enabled. Visit your VM's public IP address in a web browser, for example, I visited http://ec2-54-221-79-179.compute-1.amazonaws.com to see mine. You should see an Apache test page. If your browser complains about the `http` website being insecure and it is difficult to convince the browser to proceed anyway, or the page otherwise fails to open, it may be worth trying to use a different browser.

![](https://i.imgur.com/NIYU980.png)

Now let's create the same PHP script that we used previously, to test that PHP is installed and successfully executing code. The shell invocation (on your EC2 VM) creates a PHP script at `/var/www/html/test.php`. In the Bitnami environment that we used in a previous lab, the shell user was able to write to files within the equivalent of `/var/www/html`. In this case, we can use the `sudo` command to provide super-user privileges to the command following the `sudo`. However we can't usually use a form such as `sudo echo > /protected/place`, since the redirection to the file (the `> /protected/place` part) is actually handled by the shell, and is not seen by `sudo`, and thus won't be able to access protected places. The `tee` command writes to the filename it is given, as well as standard output, so we can apply `sudo` to `tee` to write to protected file locations.

```
[ec2-user@ip-172-31-23-180 ~]$ echo '<?php phpinfo(); ?>' | sudo tee /var/www/html/test.php
<?php phpinfo(); ?>
[ec2-user@ip-172-31-23-180 ~]$
```

Now—substituting your VM's public IP address—try to visit https://ec2-54-221-79-179.compute-1.amazonaws.com/test.php (Note that I have added `test.php` to the end of the URL we used previously---by default, the Apache web server takes the part of the URL after the slash after the DNS name, and appends that onto the web root---`/var/www` in our case---to find the filename to retrieve on the server. If a directory is selected, Apache looks for a set of default filenames, including `index.html`.)

![](https://i.imgur.com/sJOdnO5.png)

## Cloud Hygiene

We have experimented with a VM on EC2. In this case the cost of resources that we have used will be extremely small. Nonetheless the VM is still running on EC2!

It is very important that you remember to shut down VMs that you start, unless they will be doing useful work.

A good idea is to look at the dashboard any time that you expect to stop working with Amazon services, and ensure that only VMs that you expect to be running are running.

In this case, when I had finished all my experimentation with this lab exercise. I terminated the VM. You can do this from the EC2 dashboard. I happened to already be on the instance summary page for the VM I had created, and from there could use the "Instance state" button near the centre-top of the page, and from that menu choose "Terminate instance".

![](https://i.imgur.com/HpbZmL3.png)

You will be shown a confirmation dialogue, since in this case, terminating the instance will also destroy the hard-disk image and anything stored on our VM. This is what we expect in this case, so we can proceed by clicking "Terminate".

![](https://i.imgur.com/nsmWbuF.png)

I was then returned to the instance summary with a confirmation banner that the instance had been terminated.

![](https://i.imgur.com/H8v3RHd.png)

Refreshing the main EC2 Dashboard should show that there are no running instances.

:::info
:eyes: 
The EC2 Dashboard may not update immediately---there is a refresh button in the top-right of the EC2 Dashboard page aligned with the "Resources" heading.
:::

![](https://i.imgur.com/NMGqehz.png)

Amazon has clear [documentation of the instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html).

## Build your own LAMP stack

The instructions above are intended to provide enough context specific to the CS Labs to allow you to work through tutorial material on the AWS website.

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

![](https://i.imgur.com/Nmr6cM7.png)

You will be asked for confirmation:

![](https://i.imgur.com/BbUjlRZ.png)

... and the AWS indicator goes from green to yellow. I'd guess it's supposed to go back to red eventually, but it did not do so in the time between when I'd clicked "End Lab" and the time of writing this text.