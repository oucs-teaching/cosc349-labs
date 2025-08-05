## Preliminaries for Lab 4

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

You should be able to complete this lab using any operating system, e.g., macOS, Linux, or Windows, such as within the Owheo Building Labs. The instructions have been developed on a macOS system, though.

:::info
:bulb: 
On the Owheo Building Labs computers running Windows, I would recommend trying Git Bash as your shell. There are certainly ways to use PowerShell and `cmd.exe`, but where Git Bash works, all three major operating system types can follow the same instructions.
:::

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

Interactive control of AWS resources can usually be achieved through use of the AWS Management Console, which is accessed through the web using a web browser.

Normally you would log into the AWS Management Console directly, but for the AWS Academy Learner Labs, we use a less direct route. First, you log into AWS Academy, accessing a learning management system (e.g., Blackboard is a LMS). A module within the learning management system contains the controls that allow you to activate and deactivate your AWS Academy Learner Lab, and to access to many useful functions including: (1) opening a new browser tab to access AWS Management Console, and (2) to allow you to retrieve AWS credentials that you can use for command line tools (we'll get to that later).

:::info
:confused: 
AWS are currently still the world's leading public cloud provider. You may then wonder why some of the AWS Academy functionality (like the AWS Educate system before it) feels technically just a bit like it is probably not world leading. The reason for this apparent inconsistency is that AWS outsource their educational programmes to be largely run and implemented by a third party: Vocareum. In past COSC349 instances, all technical wrinkles have been able to be worked around or ironed out.
:::

### Log into AWS Academy

As noted above, to gain access to the AWS Management Console you first need to log into [AWS Academy](http://awsacademy.instructure.com) or more specifically the [COSC349 course within AWS Academy](http://awsacademy.instructure.com/courses/87101) ... but actually you may be able to [jump straight to the Learner Lab controls](https://awsacademy.instructure.com/courses/87101/modules/items/7924680) with the COSC349 AWS Academy course. The course is helpfully named "AWS Academy Learner Lab".

### Reach the Learner Lab

:::warning
:bomb: 
On macOS it seems that Safari does not work to reach the Learner Lab page. I thus switched to using Chrome to do this lab (and the screen captures contained within it).
:::

After I had successfully logged into AWS Academy and assumed the persona of "test student", I was able to reach a page such as shown in the following screen capture:

![](https://hackmd.io/_uploads/HkrwJDUo2.png)

From which I clicked "Start Lab" in the controls above the shell window. The AWS control should change from red to yellow... and then eventually to green:

![](https://i.imgur.com/lQtCSuD.png)

### AWS credentials for CLI tools

Not relevant for this lab (unless you want to experiment), but the Learner Lab page can provide you with the details that you need to give to command line tools (CLI) in order for those tools to be able to access AWS using your AWS Academy Learner Lab identity.

You can click on the "AWS Details" button near "Start Lab", and the panel to the rght of the shell will show details including "AWS CLI", which has a large "Show" button to its right.
![](https://hackmd.io/_uploads/SklclxwUsn.png)

Click "show" to find out what should go into, for example, the file `~/.aws/credentials` on macOS and Linux machines in which you may have installed the AWS command line tools (i.e., the `aws_access_key_id`, `aws_secret_access_key`, and `aws_session_token`).

### AWS Management Console

When your AWS indicator to the left of "Start Lab" is green, click on it to open another browser tab that will show you the AWS Management Console, logged in as your AWS Academy Learner Lab identity. I was greeted with a page that looked like: (note that you can scroll down a long way)

![](https://hackmd.io/_uploads/S1i_xPLsh.png)

This is the AWS Management Console for the North Virginia region. AWS Academy Learner Labs run in a particular AWS region, so you will not be able to change this. For unrestricted AWS accounts, you can change regions using the drop-down at the top-right of the window. However note that most resources have different prices in the different regions: the US East Coast regions are usually the cheapest.

One of the first points to note is that AWS provides a search-engine-style search box just to navigate within the services on offer. That should give you some idea of how many different types of services are available! (i.e., many hundreds!)

### Launch a virtual machine with EC2

At first, let's use the EC2 wizard to launch a VM. You should see this as "Launch a virtual machine" under the "Build a solution" area of the AWS Management Console page (scroll down to find it):

![](https://hackmd.io/_uploads/Hkh6ewUs3.png)

I'm using the "new launch experience", which you should opt into if you want your testing to line up with the screen captures I've made. You should see a screen such as:

![](https://hackmd.io/_uploads/SkXeWwLih.png)

First fill in a name, I used "My test VM" (very creative, I know). Scroll down to the section entitled "Application and OS Images (Amazon Machine Image)".

Amazon Machine Images (AMIs) are akin to Vagrant "box" files or VirtualBox virtual hard disks. They are the starting point of the persistent storage of the machine.

The default choice of Amazon Linux is just fine, for our test. (More specifically this default for me reached the AMI with ID `ami-0f34c5ae932e6f0e4`, which is "Amazon Linux 2023 AMI 2023.1.20230725.0 x86_64 HVM kernel-6.1".)

![](https://hackmd.io/_uploads/HJp8ZDIsn.png)

Scroll down and note that the instance type is `t2.micro` which is one of the cheapest types of VMs: e.g., about 1.2 US cents per hour. (Most of your work in COSC349 should work fine on `t2.micro` instances.)

![](https://hackmd.io/_uploads/SybOWw8j3.png)

Next scroll to the "Key pair (login)" section of the page (visible in the previous screen capture). AWS typically does not offer a way to create password-based access, e.g., to SSH to your EC2 VMs. Instead public key authentication is used, where you receive a private key in a file, and Amazon (and anyone else) can view the public key.

If you are first accessing AWS, you will need to use the option to the right to "Create new key pair". (If you have already registered key pairs, you can select them from the drop-down box.)

![](https://hackmd.io/_uploads/ry8gGvIs3.png)

I recommend using a name without spaces, such as `cosc349-2024`. RSA keys should be OK. Most variants of SSH should work with the `.pem` format---the default---however if you are ussing PuTTY on Windows, instead choose the `.ppk` format.

Finally, select "Create key pair", and note that your selected type of file (I chose `.pem` will be downloaded by your web browser). This is your private key (file) for the named key pair.

Click "Download Key Pair", and move the private key file to somewhere safe, and make the file private. On a macOS / Linux machines / Git Bash on Owheo Building Lab Windows computers, a good place to move it to is the `.ssh` subdirectory of your home directory. This is a conventional place to store encryption keys used for SSH (and the default place that must SSH software looks for keys).

:::warning
:warning:
The `~` in shell commands should be expanded to your home directory. On the Owheo Building Lab computers in Git Bash, it seems that `~` expands to `/h/` (i.e., the H drive), which is odd, since it shouldn't have the trailing `/`. Indeed, I don't think it had the trailing slash last week, so I'll follow up with the IT powers-that-be as to what might be going on. You can manually rewrite a command like `ls -ld ~/.ssh` that is referring to the `.ssh` directory within your home directory, into `ls -ld /h/.ssh` on the Owheo Building Lab computers.
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

You may need to rename the key file that was downloaded by my web-browser to remove the `.txt` extension if there is one. I changed the permissions to make the file only readable by my user using the following shell invocations:

```
$ mv ~/Downloads/cosc349-2024.pem ~/.ssh/
$ chmod 400 ~/.ssh/cosc349-2024.pem
$ ls -l ~/.ssh/cosc349-2024.pem
-r--------@ 1 dme26  staff  1678  1 Aug 22:44 /Users/dme26/.ssh/cosc349-2024.pem
```

:::warning
:warning: 
Note that on the lab machines, your Desktop, Downloads, and Documents folders are in `/h/RedirectedFolders`. So to move the key file, you would use:

```
$ mv /h/RedirectedFolders/Downloads/cosc349-2024.pem ~/.ssh/
```
:::

Next, scroll down to the "Network settings" controls.

![](https://hackmd.io/_uploads/rJfBmw8i2.png)

The default to create a security group (as noted, a security group is essentially a set of firewall rules), which is fine.

The default to "Allow SSH from ... Anywhere", is fine for our purposes. For production EC2 VMs, best practice would involve narrowing down the set of IP addresses that are permitted to SSH to the EC2 VM.

Change the "Allow HTTPs ..." and "Allow HTTP ..." traffic options to be ticked (two checks).

Then scroll down to the "Configure storage" section of the page.

![](https://hackmd.io/_uploads/HJADmDLj2.png)

The default, to create a single 8 GiB `gp3` disk is fine. As you can determine from the pull-down option, `gp3` is a type of SSD storage that AWS offers. There are other choices, most of which will be more expensive per hour, but will have higer performance.

:::info
:eyes:
Note that the free tier message mentions "EBS", i.e. AWS Elastic Block Storage. EBS volumes are actually accessed over the AWS datacentre network: they are not hard disks directly attached to the physical computer running your EC2 VM. The default settings here will delete your EBS volume when your VM is terminated, but this is not the only way to configure EBS to work. Also, EBS always uses redundant hardware, so no EBS volume will be lost if a single hardware device, such as a hard disk, fails.
:::

You can skip the "Advanced details" part of the page, which is hidden by default, anyway.

Scrolling to the "Summary" section, I see something like:

![](https://hackmd.io/_uploads/B1b0XvLi3.png)

If everything looks good, you should select the "Launch Instance" button, to create and start your EC2 VM.

### Success launching your VM

Hopefully you successfully launch your VM, and see output similar to:

![](https://hackmd.io/_uploads/H1K14wLs3.png)

Now return to the EC2 Dashboard (the topmost link on the left menu).

## EC2 Dashboard

You should now be viewing the EC2 dashboard. 

![](https://hackmd.io/_uploads/HkBZNv8jn.png)

I clicked on "Instances (running)" to see a list of EC2 VMs that have been launched (i.e., our test VM).

You can click the checkbox next to your instance to view its details in the information panel in the lower half of the web page. This area includes details such as your public and private IPv4 addresses, and your "Public IPv4 DNS".

![](https://hackmd.io/_uploads/HkVmEvLon.png)

## Connect to your VM using SSH

If you click the "Connect" button (top of page) after choosing your instance (it will be selected by default if there is only one instance), you get a choice of four different ways to open an SSH connection to your instance.

The "EC2 Instance Connect" panel tab allows you to open a JavaScript-driven SSH connection within your web browser. Click "Connect" to open the connection (scroll down to the bottom of the web page).

![](https://hackmd.io/_uploads/Bk-u4PUj2.png)

In the past this type of AWS EC2 web shell did not work for me, but it worked fine this year, as shown in the following screen capture. (Possibly Chrome has always worked better than Safari for this sort of AWS functionality?). Feel free to test that this works, although we will not be using this route of access in the rest of the lab.

![](https://hackmd.io/_uploads/ryKh4PIi3.png)

The "A standalone SSH client" panel tab does not actually allow you to connect directly from the web browser (you need to use a standalone SSH client, such as the `ssh` command in your shell (which is usually OpenSSH or similar), or a GUI program such as PuTTY on Microsoft Windows), but it does provide some useful instructions. You can click "Close" when you have read through the information presented, as specific instructions will be presented below.

:::info
:eyes: 
You may see slightly different information from what's shown in the following screen capture: for example, the DNS records for EC2 VMs may not be immediatly ready, and in those cases IP addresses will be provided instead of DNS names.

You can read up on the Domain Name System (DNS) but essentially DNS provides a mapping from names to IP addresses. So `www.google.co.nz` is a DNS name, and on pretty much any Linux / macOS / Windows terminal or command prompt, if you run the command `nslookup www.google.co.nz` then you will be told the IP address for that DNS name, `172.217.167.67`. Web browsers will happily reach the chosen site with either form, but `www.google.co.nz` is easier for humans to work with, since the name encodes semantics. (Actually, this is not entirely true---it may be that multiple DNS names point to the same IP address, which allows one webserver to serve up pages for multiple different websites, and thus if you visit the site by its IP address alone, the webserver will need to pick a default site for you to visit, of the possible choices.)
:::

![](https://hackmd.io/_uploads/r1PlSDLj2.png)

Note the public DNS address—in my case `ec2-34-238-121-97.compute-1.amazonaws.com`. It will almost certainly be different for you, and you will need to replace the DNS address of my EC2 instance with that of yours throughout the instructions below.

### Your VM's SSH host key

For security best practice, you should find out what the host (i.e., VM's) key fingerprint is, before you trust connecting to it using SSH (which is what you do in the next section, below).

(In practice, it is highly unlikely that a person-in-the-middle attack will have been set up for a VM that you just created, and it is also unlikely that you will place material that you treat as sensitive on a VM for doing COSC349 lab exercises anyway. However it's ideal to apply good security practices.)

You can determine the host key by looking at the server logs, in this case the console output captured from the VM as it booted up (that is when it created the host key).

![](https://hackmd.io/_uploads/S1twrPLs3.png)

Keep the page like that in the following screen capture open so that when you SSH to your VM, you can check the VM's host key against the keys shown in the system log. (The screen capture's log output is scrolled to the right, to see the keys.)

![](https://hackmd.io/_uploads/BJewoHwUs2.png)

### Failing to connect to your VM

First, let's try to connect to the EC2 VM in a way that will fail. Note that the first connection has prompted us to check the host key fingerprint. If you perform the steps described above, you can check that this really is your server that you're connecting to, as the long string starting `SHA256:` will match. (Although to be honest, the majority of users, globally, probably just type "yes" without checking, even though this is fundamentally insecure...)


```
$ ssh ec2-34-238-121-97.compute-1.amazonaws.com
The authenticity of host 'ec2-34-238-121-97.compute-1.amazonaws.com (34.238.121.97)' can't be established.
ED25519 key fingerprint is SHA256:rog6dh+kENeEynRphGzNeERuiYzfS6HB67Mr+jzBqsQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-34-238-121-97.compute-1.amazonaws.com' (ED25519) to the list of known hosts.
dme26@ec2-34-238-121-97.compute-1.amazonaws.com: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
$
```

This failed because the SSH server on your EC2 VM running Amazon Linux does not accept password logins, and yet we did not provide our private key to SSH so that it could use key-pair authentication.

### Successfully connecting to your VM

This time, let's specify the key-pair file that we downloaded when we set up the EC2 instance. Note also that the SSH command instance below prepends `ec2-user@` to the address to indicate that SSH should connect as the user named `ec2-user`. 

You should be able to log in. You will see a command prompt similar to that shown below. As I have done, you can try running the `uname -a` command to request information about the running operating system. I then log out. (Typing <kbd>control</kbd><kbd>d</kbd> works to log out, too.)

```
$ ssh -i ~/.ssh/cosc349-2024.pem ec2-user@ec2-34-238-121-97.compute-1.amazonaws.com
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
Last login: Tue Aug  1 10:55:33 2023 from 18.206.107.29
[ec2-user@ip-172-31-87-149 ~]$ uname -a
Linux ip-172-31-87-149.ec2.internal 6.1.38-59.109.amzn2023.x86_64 #1 SMP PREEMPT_DYNAMIC Tue Jul 11 23:51:29 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
[ec2-user@ip-172-31-87-149 ~]$ logout
Connection to ec2-34-238-121-97.compute-1.amazonaws.com closed.
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
There are multiple commands **within** the transcript below: you need to read through what's shown to find them. (Or alternatively you could search for a constant prefix of my shell prompt string `[ec2-user@ip-172-31-87-149` to find each of them.)
:::

```
$ ssh -i ~/.ssh/cosc349-2024.pem ec2-user@ec2-34-238-121-97.compute-1.amazonaws.com
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
Last login: Tue Aug  1 11:03:11 2023 from 119.224.98.119
[ec2-user@ip-172-31-87-149 ~]$ sudo yum update -y
Last metadata expiration check: 0:17:50 ago on Tue Aug  1 10:52:52 2023.
Dependencies resolved.
Nothing to do.
Complete!
[ec2-user@ip-172-31-87-149 ~]$ sudo yum install -y httpd php
Last metadata expiration check: 0:19:05 ago on Tue Aug  1 10:52:52 2023.
Dependencies resolved.
================================================================================
 Package               Arch     Version                     Repository     Size
================================================================================
Installing:
 httpd                 x86_64   2.4.56-1.amzn2023           amazonlinux    48 k
 php8.2                x86_64   8.2.7-1.amzn2023.0.1        amazonlinux    14 k
Installing dependencies:
 apr                   x86_64   1.7.2-2.amzn2023.0.2        amazonlinux   129 k
 apr-util              x86_64   1.6.3-1.amzn2023.0.1        amazonlinux    98 k
 generic-logos-httpd   noarch   18.0.0-12.amzn2023.0.3      amazonlinux    19 k
 httpd-core            x86_64   2.4.56-1.amzn2023           amazonlinux   1.4 M
 httpd-filesystem      noarch   2.4.56-1.amzn2023           amazonlinux    15 k
 httpd-tools           x86_64   2.4.56-1.amzn2023           amazonlinux    82 k
 libbrotli             x86_64   1.0.9-4.amzn2023.0.2        amazonlinux   315 k
 libxslt               x86_64   1.1.34-5.amzn2023.0.2       amazonlinux   241 k
 mailcap               noarch   2.1.49-3.amzn2023.0.3       amazonlinux    33 k
 nginx-filesystem      noarch   1:1.24.0-1.amzn2023.0.1     amazonlinux   9.0 k
 php8.2-cli            x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   3.6 M
 php8.2-common         x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   684 k
 php8.2-process        x86_64   8.2.7-1.amzn2023.0.1        amazonlinux    46 k
 php8.2-xml            x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   146 k
Installing weak dependencies:
 apr-util-openssl      x86_64   1.6.3-1.amzn2023.0.1        amazonlinux    17 k
 mod_http2             x86_64   2.0.11-2.amzn2023           amazonlinux   150 k
 mod_lua               x86_64   2.4.56-1.amzn2023           amazonlinux    62 k
 php8.2-fpm            x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   1.9 M
 php8.2-mbstring       x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   527 k
 php8.2-opcache        x86_64   8.2.7-1.amzn2023.0.1        amazonlinux   381 k
 php8.2-pdo            x86_64   8.2.7-1.amzn2023.0.1        amazonlinux    91 k

Transaction Summary
================================================================================
Install  23 Packages

Total download size: 9.8 M
Installed size: 43 M
Downloading Packages:
(1/23): php8.2-mbstring-8.2.7-1.amzn2023.0.1.x8 5.8 MB/s | 527 kB     00:00
(2/23): apr-1.7.2-2.amzn2023.0.2.x86_64.rpm     7.8 MB/s | 129 kB     00:00
(3/23): php8.2-pdo-8.2.7-1.amzn2023.0.1.x86_64. 817 kB/s |  91 kB     00:00
(4/23): httpd-2.4.56-1.amzn2023.x86_64.rpm      2.8 MB/s |  48 kB     00:00
(5/23): php8.2-8.2.7-1.amzn2023.0.1.x86_64.rpm  798 kB/s |  14 kB     00:00
(6/23): php8.2-common-8.2.7-1.amzn2023.0.1.x86_ 4.6 MB/s | 684 kB     00:00
(7/23): php8.2-fpm-8.2.7-1.amzn2023.0.1.x86_64.  22 MB/s | 1.9 MB     00:00
(8/23): php8.2-xml-8.2.7-1.amzn2023.0.1.x86_64. 1.5 MB/s | 146 kB     00:00
(9/23): php8.2-process-8.2.7-1.amzn2023.0.1.x86 3.2 MB/s |  46 kB     00:00
(10/23): apr-util-1.6.3-1.amzn2023.0.1.x86_64.r 4.3 MB/s |  98 kB     00:00
(11/23): php8.2-opcache-8.2.7-1.amzn2023.0.1.x8 7.2 MB/s | 381 kB     00:00
(12/23): apr-util-openssl-1.6.3-1.amzn2023.0.1. 1.0 MB/s |  17 kB     00:00
(13/23): libxslt-1.1.34-5.amzn2023.0.2.x86_64.r 1.1 MB/s | 241 kB     00:00
(14/23): httpd-core-2.4.56-1.amzn2023.x86_64.rp  40 MB/s | 1.4 MB     00:00
(15/23): mod_lua-2.4.56-1.amzn2023.x86_64.rpm   4.3 MB/s |  62 kB     00:00
(16/23): libbrotli-1.0.9-4.amzn2023.0.2.x86_64.  14 MB/s | 315 kB     00:00
(17/23): mod_http2-2.0.11-2.amzn2023.x86_64.rpm 5.5 MB/s | 150 kB     00:00
(18/23): httpd-tools-2.4.56-1.amzn2023.x86_64.r 2.2 MB/s |  82 kB     00:00
(19/23): mailcap-2.1.49-3.amzn2023.0.3.noarch.r 2.7 MB/s |  33 kB     00:00
(20/23): nginx-filesystem-1.24.0-1.amzn2023.0.1 730 kB/s | 9.0 kB     00:00
(21/23): generic-logos-httpd-18.0.0-12.amzn2023 1.4 MB/s |  19 kB     00:00
(22/23): httpd-filesystem-2.4.56-1.amzn2023.noa 834 kB/s |  15 kB     00:00
(23/23): php8.2-cli-8.2.7-1.amzn2023.0.1.x86_64  14 MB/s | 3.6 MB     00:00
--------------------------------------------------------------------------------
Total                                            18 MB/s | 9.8 MB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Installing       : php8.2-common-8.2.7-1.amzn2023.0.1.x86_64             1/23
  Installing       : apr-1.7.2-2.amzn2023.0.2.x86_64                       2/23
  Installing       : apr-util-openssl-1.6.3-1.amzn2023.0.1.x86_64          3/23
  Installing       : apr-util-1.6.3-1.amzn2023.0.1.x86_64                  4/23
  Running scriptlet: httpd-filesystem-2.4.56-1.amzn2023.noarch             5/23
  Installing       : httpd-filesystem-2.4.56-1.amzn2023.noarch             5/23
  Installing       : mailcap-2.1.49-3.amzn2023.0.3.noarch                  6/23
  Installing       : httpd-tools-2.4.56-1.amzn2023.x86_64                  7/23
  Installing       : httpd-core-2.4.56-1.amzn2023.x86_64                   8/23
  Installing       : mod_lua-2.4.56-1.amzn2023.x86_64                      9/23
  Installing       : mod_http2-2.0.11-2.amzn2023.x86_64                   10/23
  Installing       : php8.2-pdo-8.2.7-1.amzn2023.0.1.x86_64               11/23
  Installing       : php8.2-mbstring-8.2.7-1.amzn2023.0.1.x86_64          12/23
  Installing       : php8.2-cli-8.2.7-1.amzn2023.0.1.x86_64               13/23
  Installing       : php8.2-process-8.2.7-1.amzn2023.0.1.x86_64           14/23
  Installing       : php8.2-opcache-8.2.7-1.amzn2023.0.1.x86_64           15/23
  Installing       : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch    16/23
  Running scriptlet: nginx-filesystem-1:1.24.0-1.amzn2023.0.1.noarch      17/23
  Installing       : nginx-filesystem-1:1.24.0-1.amzn2023.0.1.noarch      17/23
  Installing       : php8.2-fpm-8.2.7-1.amzn2023.0.1.x86_64               18/23
  Running scriptlet: php8.2-fpm-8.2.7-1.amzn2023.0.1.x86_64               18/23
  Installing       : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                19/23
  Installing       : httpd-2.4.56-1.amzn2023.x86_64                       20/23
  Running scriptlet: httpd-2.4.56-1.amzn2023.x86_64                       20/23
  Installing       : libxslt-1.1.34-5.amzn2023.0.2.x86_64                 21/23
  Installing       : php8.2-xml-8.2.7-1.amzn2023.0.1.x86_64               22/23
  Installing       : php8.2-8.2.7-1.amzn2023.0.1.x86_64                   23/23
  Running scriptlet: httpd-2.4.56-1.amzn2023.x86_64                       23/23
  Running scriptlet: php8.2-8.2.7-1.amzn2023.0.1.x86_64                   23/23
  Verifying        : php8.2-pdo-8.2.7-1.amzn2023.0.1.x86_64                1/23
  Verifying        : php8.2-mbstring-8.2.7-1.amzn2023.0.1.x86_64           2/23
  Verifying        : php8.2-common-8.2.7-1.amzn2023.0.1.x86_64             3/23
  Verifying        : apr-1.7.2-2.amzn2023.0.2.x86_64                       4/23
  Verifying        : httpd-2.4.56-1.amzn2023.x86_64                        5/23
  Verifying        : php8.2-8.2.7-1.amzn2023.0.1.x86_64                    6/23
  Verifying        : php8.2-fpm-8.2.7-1.amzn2023.0.1.x86_64                7/23
  Verifying        : php8.2-xml-8.2.7-1.amzn2023.0.1.x86_64                8/23
  Verifying        : libxslt-1.1.34-5.amzn2023.0.2.x86_64                  9/23
  Verifying        : php8.2-cli-8.2.7-1.amzn2023.0.1.x86_64               10/23
  Verifying        : php8.2-process-8.2.7-1.amzn2023.0.1.x86_64           11/23
  Verifying        : apr-util-1.6.3-1.amzn2023.0.1.x86_64                 12/23
  Verifying        : php8.2-opcache-8.2.7-1.amzn2023.0.1.x86_64           13/23
  Verifying        : apr-util-openssl-1.6.3-1.amzn2023.0.1.x86_64         14/23
  Verifying        : httpd-core-2.4.56-1.amzn2023.x86_64                  15/23
  Verifying        : libbrotli-1.0.9-4.amzn2023.0.2.x86_64                16/23
  Verifying        : mod_lua-2.4.56-1.amzn2023.x86_64                     17/23
  Verifying        : httpd-tools-2.4.56-1.amzn2023.x86_64                 18/23
  Verifying        : mod_http2-2.0.11-2.amzn2023.x86_64                   19/23
  Verifying        : mailcap-2.1.49-3.amzn2023.0.3.noarch                 20/23
  Verifying        : nginx-filesystem-1:1.24.0-1.amzn2023.0.1.noarch      21/23
  Verifying        : generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch    22/23
  Verifying        : httpd-filesystem-2.4.56-1.amzn2023.noarch            23/23

Installed:
  apr-1.7.2-2.amzn2023.0.2.x86_64
  apr-util-1.6.3-1.amzn2023.0.1.x86_64
  apr-util-openssl-1.6.3-1.amzn2023.0.1.x86_64
  generic-logos-httpd-18.0.0-12.amzn2023.0.3.noarch
  httpd-2.4.56-1.amzn2023.x86_64
  httpd-core-2.4.56-1.amzn2023.x86_64
  httpd-filesystem-2.4.56-1.amzn2023.noarch
  httpd-tools-2.4.56-1.amzn2023.x86_64
  libbrotli-1.0.9-4.amzn2023.0.2.x86_64
  libxslt-1.1.34-5.amzn2023.0.2.x86_64
  mailcap-2.1.49-3.amzn2023.0.3.noarch
  mod_http2-2.0.11-2.amzn2023.x86_64
  mod_lua-2.4.56-1.amzn2023.x86_64
  nginx-filesystem-1:1.24.0-1.amzn2023.0.1.noarch
  php8.2-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-cli-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-common-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-fpm-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-mbstring-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-opcache-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-pdo-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-process-8.2.7-1.amzn2023.0.1.x86_64
  php8.2-xml-8.2.7-1.amzn2023.0.1.x86_64

Complete!
[ec2-user@ip-172-31-87-149 ~]$ sudo systemctl start httpd.service
```

:::info
:eyes: 
Note that the very last command, the invocation of `systemctl` to start the web server on your VM, has many historic variants, a number of which will still work. For example, you can also instead type `sudo service httpd start` (this will be redirected to `systemctl`). I mention this just in case it helps correlate documentation you find on the web with your experience on EC2.
:::


At this point, the Apache web server and PHP language should be installed and enabled. Visit your VM's public IP address in a web browser, for example, I visited http://ec2-34-238-121-97.compute-1.amazonaws.com to see mine. You should see an Apache test page, but note that most browsers and links will default to offering up and/or connecting to `https:` rather than `http:`. You should double check use of `http:` if your web server test does not work. If your browser goes further to complain about the `http` website being insecure (fair enough!) and it is difficult to convince the browser to proceed anyway, or the page otherwise fails to open, it may be worth trying to use a different browser.

![](https://hackmd.io/_uploads/B1_SKvLj3.png)

Now let's create the same PHP script that we used previously, to test that PHP is installed and successfully executing code. The shell invocation (on your EC2 VM) creates a PHP script at `/var/www/html/test.php`. In the Bitnami environment that we used in a previous lab, the shell user was able to write to files within the equivalent of `/var/www/html`. In this case, we can use the `sudo` command to provide super-user privileges to the command following the `sudo`. However we can't usually use a form such as `sudo echo > /protected/place`, since the redirection to the file (the `> /protected/place` part) is actually handled by the shell, and is not seen by `sudo`, and thus won't be able to access protected places. The `tee` command writes to the filename it is given, as well as standard output, so we can apply `sudo` to `tee` to write to protected file locations.

```
[ec2-user@ip-172-31-87-149 ~]$ echo '<?php phpinfo(); ?>' | sudo tee /var/www/html/test.php
<?php phpinfo(); ?>
[ec2-user@ip-172-31-87-149 ~]$
```

Now—substituting your VM's public IP address—try to visit https://ec2-54-221-79-179.compute-1.amazonaws.com/test.php (Note that I have added `test.php` to the end of the URL we used previously---by default, the Apache web server takes the part of the URL after the slash after the DNS name, and appends that onto the web root---`/var/www` in our case---to find the filename to retrieve on the server. If a directory is selected, Apache looks for a set of default filenames, including `index.html`.)

![](https://hackmd.io/_uploads/ryV3FvUo2.png)

## Cloud Hygiene

We have experimented with a VM on EC2. In this case the cost of resources that we have used will be extremely small. Nonetheless the VM is still running on EC2!

It is very important that you remember to shut down VMs that you start, unless they will be doing useful work.

A good idea is to look at the dashboard any time that you expect to stop working with Amazon services, and ensure that only VMs that you expect to be running are running.

In this case, when I had finished all my experimentation with this lab exercise. I terminated the VM. You can do this from the EC2 dashboard. I happened to already be on the instance summary page for the VM I had created, and from there could use the "Instance state" button near the centre-top of the page, and from that menu choose "Terminate instance".

![](https://hackmd.io/_uploads/rks0FPIjn.png)

You will be shown a confirmation dialogue, since in this case, terminating the instance will also destroy the hard-disk image and anything stored on our VM. This is what we expect in this case, so we can proceed by clicking "Terminate".

![](https://hackmd.io/_uploads/SkpZcDIi2.png)

I was then returned to the instance summary with a confirmation banner that the instance had been terminated.

![](https://hackmd.io/_uploads/Sk0McwLjh.png)

Refreshing the main EC2 Dashboard should show that there are no running instances.

:::info
:eyes: 
The EC2 Dashboard may not update immediately---there is a refresh button in the top-right of the EC2 Dashboard page aligned with the "Resources" heading.
:::

![](https://hackmd.io/_uploads/rkPNqwLj3.png)

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

![](https://hackmd.io/_uploads/SJ5K5v8jh.png)

You will be asked for confirmation:

![](https://hackmd.io/_uploads/ry25cPIoh.png)

... and the AWS indicator goes from green to yellow. I'd guess it's supposed to go back to red eventually, but it did not do so in the time between when I'd clicked "End Lab" and the time of writing this text.