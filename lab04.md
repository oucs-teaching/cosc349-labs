---
tags: cosc349
---
# COSC349 Lab 4—Cloud Architecture—2020
## Preliminaries for Lab 4
These preliminary steps should ideally be performed within week 2, to ensure that you are prepared for the lab in week 4... although you are indeed most welcome to start using the AWS credit on COSC349 labs and other exploration as soon as you receive it (just don't use it all up!).

:::warning
Since I already have an AWS account, I was not able to see precisely what you see through all steps entering your AWS classroom. I believe that you end up seeing the same AWS Console that I do, but with some key differences:
- I can potentially log into your AWS Console (only) in the context of your AWS Educate Classroom. In practice I don't expect to do this without you inviting me, or unless some other problem arises and I need to perform action;
- You don't need a registered credit card;
- You do not need to pay Amazon for the initial credit allocated to you;
- Within the context of this AWS Educate Classroom, your AWS credit and all AWS resources you use will vanish when the Classroom closes (in the exam period after semester).

Of course please do let me know if you see material significantly different from the screen-shots and descriptions that I provide below!
:::


### Activating your AWS Educate account

Using AWS costs money, although Amazon are also very keen that potential clients of their cloud services are not turned away by the cost. Of the many ways Amazon tries to help:
1. Amazon provide a generous "free tier", that allows you to try out many of their services without cost. Sometimes the free tier is refreshed annually, so that small-scale use of Amazon services actually end up being free, in effect. However using the free tier requires having an Amazon account, which in turn requires registering a credit card, even if it doesn't end up getting charged.
2. Amazon provide generous educational access, such as the "AWS Educate Classrooms" that we will be trying to use.

:::info
Although we have discussed Amazon Web Services here, most of the major public cloud providers provide some sort of equivalent free tier, or academic access. Feel free to collect them all—I would be interested to hear about your experiences comparing their offerings.
:::

You should have received, to your student email address, an email that should look something like the following:

> Hi - 
> 
> Your educator has invited you to join AWS Educate and access a "Classroom" for your course work. A "Classroom" is a hands-on learning environment for you to access AWS services and practice AWS. There are no costs or fees to access a Classroom. 

Further down in the email is a that you need to click. This will take you to a registration page. It may seem surprising that Amazon are collecting information about your institution, etc., but keen in mind that there is no tight coupling between the University of Otago and Amazon regarding this service. You are establishing a connection with Amazon. For example, Amazon need to ask your birthdate because there are legal restrictions on your age for using their cloud services, and this is how they determine your eligibility (automatically). If you are not comfortable providing any of the information on this form, please do let me know, so that we can discuss your concerns. Note that the terms and conditions and privacy notice are linked from that page.

![](https://i.imgur.com/d889nv2.png)

On the next page you will need to agree AWS Educate's Terms and Conditions. My impression is that these have been carefully designed to have a "light touch" given that education is a sensitive sector with which to interact, and Amazon need to comply with a wide range of regulations internationally. Again, do let me know if you have any concerns, though.

On completing the sign-up process, you will receive an email that includes a verification link that you need to visit.

At this time your AWS Educate application will go in for review, which appears to be a non-automatic process: it may take a few days to be processed.

:::info
Here's some more info about AWS Educate that was posted to by a student from 2019. It goes into more detail about the sign-up process and the differences between the Classroom account you are allocated (and should probably use for this paper) and the extra credit avaliable for a personal account, in case anyone's interested.

>For those that haven’t signed up for AWS yet (and those that have but haven’t linked a AWS account or made an AWS starter account), this is how it all works.
>
>When you follow the email link, you make an AWS Educate account under your student email. The email field is pre-populated with your student email and you seem to be automatically approved immediately. This gives you access to a ‘Classroom’ with $50 USD in credit for use in one of the US datacenters only (East Coast at that), which David Eyers can also access and is only available for the duration of the paper. 
>
>You can also get $30/$40 USD in credit for a personal account if you want one. You can link an existing AWS account (or make one then link it) for $40 USD of credit, but a credit card is required. It is a full AWS account however. The other option is an AWS Starter account. No credit card is required, but you only get $30 USD of credit, you can’t add a payment method (the $30 USD is all you get, but I think you get another $30 each year), if you use it up your account disappears and you can’t convert it into a normal AWS account. You also don’t get access to some services and you don’t get Free Tier. If you’ve got a credit or debit card and you don’t mind giving it to Amazon (and having it charged if you use up your $40 USD credit), then I strongly recommend using a normal AWS account instead of a Starter account if you are wanting a personal AWS account. Do note however that you don’t need either for this course, you can just use the classroom with its $50 USD of credit. 
>
>Unfortunately, you do have to logout of one account before switching to the other, so using both the classroom and either a normal or Starter AWS account at the same time is a pain. You’re probably best to just use the classroom for the course and worry about a personal account later. 
>
>One other thing is that GitHub offers extra AWS credit as part of their GitHub Student Developer Pack (as well as a few other things like credit on other cloud providers). I’d recommend having a look at that.
:::

## Lab 4—AWS IaaS with EC2

You should be able to complete this lab using any operating system, e.g., macOS or Linux in the CS labs. The instructions have been developed on a macOS system, though.

In this lab we will introduce use of Amazon's Elastic Compute Cloud service, which provides (among other features) Infrastructure as a Service—i.e., the provision of (headless) virtual machines of the type you have experimented with using Vagrant in [Lab 3](/bi1pAIlXT3O4WezjVtqPrA).

Note that, consistent with the overall goals of COSC349, a primary aim is for you to see how the pieces of software infrastructure piece together. There are many excellent ways to deploy applications to AWS with a single mouse-click. You are very welcome to experiment with these, and provided that you keep an eye on your Amazon expenditure, there should be plenty of credit within your Amazon Educate allowance to facilitate you exploring the high-convenience Amazon offerings as well as the more technically detailed ones.

### Lab objectives

1. Ensure you can access Amazon Web Services using your AWS Educate account or otherwise.
1. Learn how to configure and launch virtual machines in the Amazon Elastic Compute Cloud (EC2).
1. Interact with your EC2 VMs as a user—e.g., view web sites produced by your VM.
1. Interact with your EC2 VMs as a developer, using the secure shell (SSH).
1. Understand and practice good "cloud hygiene", such as always checking that you have shut down unused but resource (and credit) consuming VMs.

:::info
To get the most out of the lab exercises, it is likely that you should take notes about the concepts you are least clear about. That way you can easily do further reading to acquire the information that you need, or can ask the COSC349 teaching team members for help or explanations.
:::

## Let's create a VM on AWS

Infrastructure as a Service VMs on Amazon are managed under the Amazon's Elastic Compute Cloud service—known as EC2.

:::warning
Accessing the AWS front page from the link that follows may not work the first time you use it. You can instead log in to Amazon Educate, click on the "My Classrooms" menu, then choose "Go to classroom" from the right of the table of classrooms. From that page you should be able to clock the "AWS Console" link. 

(As noted above, I was unsuccessful in getting a student account as well as a teacher account, which means I have not been able to see exactly what you see, unfortunately. Of course, just let me know what's not working, and we can augment the lab instructions.)
:::

Navigate to the AWS front page: 
https://console.aws.amazon.com/console/home


![](https://i.imgur.com/dMYvfiU.png)

In my case, I end up seeing the console on the North Virginia  region. AWS Educate classrooms run in a particular region, so you will not be able to change this. For unrestricted accounts, you can change regions using the drop-down at the top-right of the window. However note that most resources have different prices in the different regions: the US East Coast regions are usually the cheapest.

One of the first points to note is that AWS provides a search-engine-style search box just to navigate within the services on offer. That should give you some idea of how many different types of services are available!

### Launch a virtual machine with EC2

At first, let's use the EC2 wizard to launch a VM. You should see this as "Launch a virtual machine" under the "Build a solution" area of the page.

### 1. Chose an Amazon Machine Image (AMI)

AMIs are akin to Vagrant "box" files or VirtualBox virtual hard disks. They are the starting point of the persistent storage of the machine.

Select the Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type (the second on the list in this screen capture, but you may need to use the search control to find it).

![](https://i.imgur.com/WrOXh9r.png)

### 2. Choose Instance Type

The default selection is for a t2.micro instance. This is reasonable. Note the columns in this table, and scroll down to see just how many different configurations are on offer...

![](https://i.imgur.com/LyF2s38.png)

Click "Next: Configure Instance Details".

### 3. Configure Instance Details

Most of the configuration parameters on this page relate to situations more complex than the test that we will perform.

![](https://i.imgur.com/bcNLMQ2.png)

Click "Next: Add Storage"

### 4. Add Storage

The default configuration creates an Amazon EBS volume—i.e., a virtual hard disk—that will be deleted when the VM stops.

![](https://i.imgur.com/Gi5zgLC.png)

Click "Next: Add Tags"

### 5. Add Tags

Tags allow you to organise your EC2 resources and volumes. This is unlikely to be important within the COSC349 context, but if you were a contractor working for multiple organisations, it could be very useful to separate your resources using tagging.

Click "Next: Configure Security Group"

### 6. Configure Security Group

Security groups are Amazon's way of managing firewalls that protect your VMs. Note that the launch wizard has defaulted to creating a new security group, and has provided a rule that allows SSH traffic to reach your VM from anywhere on the Internet.

![](https://i.imgur.com/Tr0D11V.png)

Let's use the "Add Rule" button to also open access to port 80, i.e., HTTP. Note that I have also inserted a description on both types of access.

![](https://i.imgur.com/rM5L1Pc.png)

Now click the "Review and Launch" button

### 7. Review

I have attached what I see. Note that the security warning is because any IP address can SSH to your VM. This is unlikely to be necessary in practice, and narrowing the accessibility of the SSH server on your VM will increase effective security easily.

![](https://i.imgur.com/JQ5TZph.png)

### Download Key Pair

EC2 VMs do not allow you to use SSH to log into them using passwords: you must use SSH public key encryption.

Thus, before creating your instance, you will be prompted as to which public key will be pre-loaded into your VM, so that you can use your private key to connect.

As shown in the following screenshot, I have suggested that you create a new key pair, and in this case I have labelled it cosc349-lab since that is what I expect I will use this key-pair for COSC349 labs.

![](https://i.imgur.com/KDqqLSy.png)

Click "Download Key Pair", and move it somewhere safe, and private. On a CS Lab machine, a good place to move it to is the `.ssh` subdirectory of your home directory (`~/.ssh`). This is a conventional place to store encryption keys used for SSH.

:::warning
Note that it's possible that you do not yet have an `.ssh` directory in your home-directory. You can check—
```
$ ls -ld ~/.ssh
drwx------  28 dme  11306  896  8 Jul 13:46 .ssh
```
—and if the `ls` command fails, create the directory and set the permissions appropriately (SSH will refuse to use files from directories that are not sufficiently protected) using the command `mkdir -m 700 ~/.ssh` (the `-m` option sets the access control bits on that directory, just as is done when using the `chmod` command).
:::

In my case, I needed to rename the key file that was downloaded by my web-browser to remove the `.txt` extension, and I changed the permissions to make the file only readable by my user.

```
$ mv ~/Downloads/cosc349-lab.pem.txt ~/.ssh/cosc349-lab.pem
$ chmod 400 ~/.ssh/cosc349-lab.pem 
$ ls -l ~/.ssh/cosc349-lab.pem 
-r--------@ 1 dme  11306  1692  8 Jul 13:45 /Users/dme/.ssh/cosc349-lab.pem
```

### Launch Instances

Finally, you should be able to click the "Launch Instances" button, shown below.

![](https://i.imgur.com/gZBOEn2.png)

You will see a webpage with a spinner indicating various actions being performed.

![](https://i.imgur.com/x5xPzVT.png)

Eventually you should see a page similar to the one shown below.

![](https://i.imgur.com/Wztdatc.png)

Click "View Instances"

## EC2 Dashboard

You should now be viewing the EC2 dashboard. In my case, the instance that was created immediately went to the "Instance State" of running. The "Status Checks" took a minute or so to finalise (you do not need to wait), but then reported 2/2 checks passed.

![](https://i.imgur.com/5fHHvR7.png)

Down the bottom of the dashboard, you should see a display that indicates details such as your Private IPs and Public IPv4 address.

## Connect to your VM using SSH

If you click the "Connect" button after choosing your instance (it will be selected by default if there is only one instance), you get a choice of three different ways to open an SSH connection to your instance.

The "EC2 Instance Connect" option should allow you to open a JavaScript-driven SSH connection within your web browser, although didn't work for me—I didn't try to fix it, as it was not my preferred option.

![](https://i.imgur.com/bevjgf9.png)

The "A standalone SSH client" option does not actually allow you to connect directly from the web browser (you need to use a standalone SSH client), but it does provide some useful instructions. You can click "Close" when you have read through the information presented, as specific instructions will be presented below.

:::info
You may see slightly different information from what's shown in the following screen capture: in my case the DNS records for my EC2 VM were not yet ready, so Amazon has provided information about connecting to my VM using an IP address. However when DNS records have been prepared for your VM, you may instead see a DNS name instead of an IP address. Both should work.

You can read up on the Domain Name System (DNS) but essentially DNS provides a mapping from names to IP addresses. So `www.google.co.nz` is a DNS name, and on pretty much any Linux / macOS / Windows terminal or command prompt, if you run the command `nslookup www.google.co.nz` then you will be told the IP address for that DNS name, `172.217.167.67`. Web browsers will happily reach the CS site with either form, but `www.google.co.nz` is easier for humans to work with, since the name encodes semantics. (Actually, this is not entirely true---it may be that multiple DNS names point to the same IP address, which allows one webserver to serve up pages for multiple different websites, and thus if you visit the site by its IP address alone, the webserver will need to pick a default site for you to visit, of the possible choices.)
:::

![](https://i.imgur.com/TvKGWPB.png)

Note the public IP address—in my case 54.205.214.108. It will almost certainly be different for you, and you will need to replace the IP address of my EC2 instance with that of yours throughout the instructions below.

### Your VM's SSH host key

For security best practice, you should find out what the host (i.e., VM's) key fingerprint is, before you trust connecting to it. More pragmatically, though, it is unlikely that a man-in-the-middle attack will have been set up for a VM that you just created, and it is also unlikely that you will place material that you treat as sensitive on a VM for doing COSC349 lab exercises anyway...

You can determine the host key by looking at the server logs, in this case the console output captured from the VM as it booted up (that is when it created the host key). In my case SSH connected using the ECDSA encryption method, so I needed to find the fingerprint displayed soon after the log message "Your public key has been saved in /etc/ssh/ssh_host_ecdsa_key.pub.". Alternatively, there is a summary of keys printed near the bottom of the System Log.

![](https://i.imgur.com/5qTrbZG.png)
![](https://i.imgur.com/Y4ONdDk.png)


### Failing to connect to your VM

First, let's try to connect to the EC2 VM in a way that will fail. Note that the first connection has prompted us to check the host key fingerprint. If you perform the steps described above, you can check that this really is your server that you're connecting to, as the long string starting `SHA256:` will match. (Although to be honest, the majority of users, globally, probably just type "yes" without checking, even though this is fundamentally insecure...)


```
$ ssh ec2-user@54.205.214.108
The authenticity of host '54.205.214.108 (54.205.214.108)' can't be established.
ECDSA key fingerprint is SHA256:hzC1Nvg48PyXj93P7wllK3Pd3iMDE3k6GdqNY5lsL5w.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '54.205.214.108' (ECDSA) to the list of known hosts.
ec2-user@54.205.214.108: Permission denied (publickey).
```

This failed because the SSH server on the Amazon Linux VM does not accept password logins, and yet we did not provide our private key to SSH so that it could use key-pair authentication.

### Successfully connecting to your VM

This time, let's specify the key-pair file that we downloaded when we set up the EC2 instance. You should be able to log in. You will see a command prompt similar to that shown below. As I have done, you can try running the `uname -a` command to request information about the running operating system. I then log out. (Typing <kbd>control</kbd><kbd>d</kbd> works to log out, too.)

```
$ ssh -i ~/.ssh/cosc349-lab.pem ec2-user@54.205.214.108

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2018.03-release-notes/
[ec2-user@ip-172-30-0-192 ~]$ uname -a
Linux ip-172-30-0-192 4.14.123-86.109.amzn1.x86_64 #1 SMP Mon Jun 10 19:44:53 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
[ec2-user@ip-172-30-0-192 ~]$ logout
Connection to 54.205.214.108 closed.
```

## Installing a web server onto your VM

SSH to your VM again, but this time, use the commands below to update the software package list, and to install Apache and PHP. Note that the default Linux EC2 VMI is a RedHat-flavour of Linux rather than a Ubuntu flavour, so the package management commands are different, and the names of the packages are different. (In an ideal world, we'd only have to use one package manager...)

:::info
It is likely that you will need to look up and read at least the introductory documentation of the software package management commands used to install software on whatever variety of Linux you use, when you are not following specific lab exercises. 

(You are likely either to be using `apt` on Debian-derived distributions, such as Ubuntu, or `yum` on RedHat-derived distributions, such as Fedora, or Amazon's Linux AMI.)
:::

:::warning
There are multiple commands in the transcript below: you need to read through what's shown to find them. (Or alternatively you could search for my prompt string `[ec2-user@ip-172-30-0-192` to find each of them.)
:::

```
$ ssh -i ~/.ssh/cosc349-lab.pem ec2-user@54.205.214.108
Last login: Mon Jul  8 02:18:03 2019 from oucs1564.otago.ac.nz

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2018.03-release-notes/
3 package(s) needed for security, out of 5 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-30-0-192 ~]$ sudo yum update -y
Loaded plugins: priorities, update-motd, upgrade-helper
amzn-main                                                | 2.1 kB     00:00     
amzn-updates                                             | 2.5 kB     00:00     
No packages marked for update
[ec2-user@ip-172-30-0-192 ~]$ sudo yum install -y httpd24 php56
Loaded plugins: priorities, update-motd, upgrade-helper
Resolving Dependencies
--> Running transaction check
---> Package httpd24.x86_64 0:2.4.43-1.89.amzn1 will be installed
--> Processing Dependency: httpd24-tools = 2.4.43-1.89.amzn1 for package: httpd24-2.4.43-1.89.amzn1.x86_64
--> Processing Dependency: apr-util > 1.5.1 for package: httpd24-2.4.43-1.89.amzn1.x86_64
--> Processing Dependency: apr > 1.5.1 for package: httpd24-2.4.43-1.89.amzn1.x86_64
--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd24-2.4.43-1.89.amzn1.x86_64
--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd24-2.4.43-1.89.amzn1.x86_64
---> Package php56.x86_64 0:5.6.40-1.143.amzn1 will be installed
--> Processing Dependency: php56-cli(x86-64) = 5.6.40-1.143.amzn1 for package: php56-5.6.40-1.143.amzn1.x86_64
--> Processing Dependency: php56-common(x86-64) = 5.6.40-1.143.amzn1 for package: php56-5.6.40-1.143.amzn1.x86_64
--> Processing Dependency: php56-common for package: php56-5.6.40-1.143.amzn1.x86_64
--> Running transaction check
---> Package apr.x86_64 0:1.5.2-5.13.amzn1 will be installed
---> Package apr-util.x86_64 0:1.5.4-6.18.amzn1 will be installed
---> Package httpd24-tools.x86_64 0:2.4.43-1.89.amzn1 will be installed
---> Package php56-cli.x86_64 0:5.6.40-1.143.amzn1 will be installed
---> Package php56-common.x86_64 0:5.6.40-1.143.amzn1 will be installed
--> Processing Dependency: php56-process(x86-64) = 5.6.40-1.143.amzn1 for package: php56-common-5.6.40-1.143.amzn1.x86_64
--> Processing Dependency: php56-xml(x86-64) = 5.6.40-1.143.amzn1 for package: php56-common-5.6.40-1.143.amzn1.x86_64
--> Processing Dependency: php56-jsonc(x86-64) for package: php56-common-5.6.40-1.143.amzn1.x86_64
--> Running transaction check
---> Package php56-jsonc.x86_64 0:1.3.10-1.20.amzn1 will be installed
---> Package php56-process.x86_64 0:5.6.40-1.143.amzn1 will be installed
---> Package php56-xml.x86_64 0:5.6.40-1.143.amzn1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package            Arch        Version                 Repository         Size
================================================================================
Installing:
 httpd24            x86_64      2.4.43-1.89.amzn1       amzn-updates      1.6 M
 php56              x86_64      5.6.40-1.143.amzn1      amzn-updates      3.0 M
Installing for dependencies:
 apr                x86_64      1.5.2-5.13.amzn1        amzn-main         118 k
 apr-util           x86_64      1.5.4-6.18.amzn1        amzn-main          99 k
 httpd24-tools      x86_64      2.4.43-1.89.amzn1       amzn-updates       92 k
 php56-cli          x86_64      5.6.40-1.143.amzn1      amzn-updates      4.2 M
 php56-common       x86_64      5.6.40-1.143.amzn1      amzn-updates      1.4 M
 php56-jsonc        x86_64      1.3.10-1.20.amzn1       amzn-main          82 k
 php56-process      x86_64      5.6.40-1.143.amzn1      amzn-updates      100 k
 php56-xml          x86_64      5.6.40-1.143.amzn1      amzn-updates      345 k

Transaction Summary
================================================================================
Install  2 Packages (+8 Dependent packages)

Total download size: 11 M
Installed size: 33 M
Downloading packages:
(1/10): httpd24-tools-2.4.43-1.89.amzn1.x86_64.rpm         |  92 kB   00:00     
(2/10): apr-util-1.5.4-6.18.amzn1.x86_64.rpm               |  99 kB   00:00     
(3/10): apr-1.5.2-5.13.amzn1.x86_64.rpm                    | 118 kB   00:00     
(4/10): php56-jsonc-1.3.10-1.20.amzn1.x86_64.rpm           |  82 kB   00:00     
(5/10): php56-common-5.6.40-1.143.amzn1.x86_64.rpm         | 1.4 MB   00:00     
(6/10): httpd24-2.4.43-1.89.amzn1.x86_64.rpm               | 1.6 MB   00:00     
(7/10): php56-5.6.40-1.143.amzn1.x86_64.rpm                | 3.0 MB   00:00     
(8/10): php56-process-5.6.40-1.143.amzn1.x86_64.rpm        | 100 kB   00:00     
(9/10): php56-xml-5.6.40-1.143.amzn1.x86_64.rpm            | 345 kB   00:00     
(10/10): php56-cli-5.6.40-1.143.amzn1.x86_64.rpm           | 4.2 MB   00:00     
--------------------------------------------------------------------------------
Total                                              9.1 MB/s |  11 MB  00:01     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : php56-jsonc-1.3.10-1.20.amzn1.x86_64                        1/10 
  Installing : php56-process-5.6.40-1.143.amzn1.x86_64                     2/10 
  Installing : php56-xml-5.6.40-1.143.amzn1.x86_64                         3/10 
  Installing : php56-cli-5.6.40-1.143.amzn1.x86_64                         4/10 
  Installing : php56-common-5.6.40-1.143.amzn1.x86_64                      5/10 
  Installing : apr-1.5.2-5.13.amzn1.x86_64                                 6/10 
  Installing : apr-util-1.5.4-6.18.amzn1.x86_64                            7/10 
  Installing : httpd24-tools-2.4.43-1.89.amzn1.x86_64                      8/10 
  Installing : httpd24-2.4.43-1.89.amzn1.x86_64                            9/10 
  Installing : php56-5.6.40-1.143.amzn1.x86_64                            10/10 
  Verifying  : php56-common-5.6.40-1.143.amzn1.x86_64                      1/10 
  Verifying  : httpd24-tools-2.4.43-1.89.amzn1.x86_64                      2/10 
  Verifying  : php56-5.6.40-1.143.amzn1.x86_64                             3/10 
  Verifying  : php56-jsonc-1.3.10-1.20.amzn1.x86_64                        4/10 
  Verifying  : php56-process-5.6.40-1.143.amzn1.x86_64                     5/10 
  Verifying  : httpd24-2.4.43-1.89.amzn1.x86_64                            6/10 
  Verifying  : php56-xml-5.6.40-1.143.amzn1.x86_64                         7/10 
  Verifying  : php56-cli-5.6.40-1.143.amzn1.x86_64                         8/10 
  Verifying  : apr-1.5.2-5.13.amzn1.x86_64                                 9/10 
  Verifying  : apr-util-1.5.4-6.18.amzn1.x86_64                           10/10 

Installed:
  httpd24.x86_64 0:2.4.43-1.89.amzn1      php56.x86_64 0:5.6.40-1.143.amzn1     

Dependency Installed:
  apr.x86_64 0:1.5.2-5.13.amzn1                                                 
  apr-util.x86_64 0:1.5.4-6.18.amzn1                                            
  httpd24-tools.x86_64 0:2.4.43-1.89.amzn1                                      
  php56-cli.x86_64 0:5.6.40-1.143.amzn1                                         
  php56-common.x86_64 0:5.6.40-1.143.amzn1                                      
  php56-jsonc.x86_64 0:1.3.10-1.20.amzn1                                        
  php56-process.x86_64 0:5.6.40-1.143.amzn1                                     
  php56-xml.x86_64 0:5.6.40-1.143.amzn1                                         

Complete!
[ec2-user@ip-172-30-0-192 ~]$ sudo service httpd start
Starting httpd:                                            [  OK  ]
[ec2-user@ip-172-30-0-192 ~]$ 
```

At this point, the Apache web server and PHP language should be installed and enabled. Visit your VM's public IP address in a web browser, for example, I visited http://54.205.214.108 to see mine. You should see an Apache test page.

![](https://i.imgur.com/cyEMAWy.png)

Now let's create the same PHP script that we used previously, to test that PHP is installed and successfully executing code. The shell invocation (on your EC2 VM) creates a PHP script at `/var/www/html/test.php`. In the Bitnami environment that we used in a previous lab, the shell user was able to write to files within the equivalent of `/var/www/html`. In this case, we can use the `sudo` command to provide super-user privileges to the command following the `sudo`. However we can't usually use a form such as `sudo echo > /protected/place`, since the redirection to the file (the `> /protected/place` part) is actually handled by the shell, and is not seen by `sudo`, and thus won't be able to access protected places. The `tee` command writes to the filename it is given, as well as standard output, so we can apply `sudo` to `tee` to write to protected file locations.

```
[ec2-user@ip-172-30-0-192 ~]$ echo '<?php phpinfo(); ?>' | sudo tee /var/www/html/test.php
<?php phpinfo(); ?>
[ec2-user@ip-172-30-0-192 ~]$ 
```

Now—substituting your VM's public IP address—try to visit http://54.205.214.108/test.php

![](https://i.imgur.com/fBedPkD.png)

## Cloud Hygiene

We have experimented with a VM on EC2. In this case the cost of resources that we have used will be extremely small. Nonetheless the VM is still running on EC2!

It is very important that you remember to shut down VMs that you start, unless they will be doing useful work.

A good idea is to look at the dashboard any time that you expect to stop working with Amazon services, and ensure that only VMs that you expect to be running are running.

In this case, when I had finished my experimentation with this lab exercise, from the EC2 dashboard, I selected the test VM, and selected the menu item "Actions", "Instance State", "Terminate".

![](https://i.imgur.com/vIVqnWT.png)

You will be shown a confirmation dialogue, since in this case, terminating the instance will also destroy the hard-disk image and anything stored on our VM. This is what we expect in this case, so we can proceed by clicking "Yes, Terminate".

![](https://i.imgur.com/8QMDHnN.png)

On the EC2 Dashboard you will see the instance state change to "shutting down".

![](https://i.imgur.com/0QnRtRA.png)

Eventually the dashboard will show the instance as "terminated".

![](https://i.imgur.com/29bCpII.png)

Amazon has clear [documentation of the instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html).

## Build your own LAMP stack

The instructions above are intended to provide enough context specific to the CS Labs to allow you to work through tutorial material on the AWS website.

Now that you've created, visited, modified and destroyed a single EC2 VM, you can move on to a more complex example that involves creating multiple VMs.

:::info
While you should definitely ensure that you are comfortable working with EC2, and following the documentation and tutorials that are provided, you are not *required* to complete any given proportion of their material, or the specific tutorial below. Just experiment with their services within the time that you have available in the lab. We'd be keen to hear what your experiences are. Remember that you can refer back to this lab later if you need to, of course, if it is useful for completing assignment work, for example.
:::

:::success
Work through the [Amazon tutorial at that describes how to build a LAMP stack](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html) with a separate database and web server.
:::

:::success
Using the materials in this lab, and by reading Amazon's documentation (e.g., their LAMP stack tutorial mentioned above), try to create two fresh VMs and setup EC2 to allow you to `ssh` from one to the other. Then have one VM download a web page from the other VM (e.g., using a command such as `wget` or `curl`---you may well need to look up how to use these commands). 
:::

Please seek help if you get stuck or run into knowledge gaps, and we can augment these lab exercises to support what you need to know.