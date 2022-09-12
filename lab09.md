---
tags: cosc349
---
# COSC349 Lab 9—Cloud Architecture—2022
## Lab 9—Amazon EC2 via Vagrant

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

:::info
:warning: 
This lab has been tested in 2022 by the teaching team, including on the CS labs to the extent to which student user accounts will behave like staff accounts does. If you run into problems when working on the CS lab computers, please let the teaching team know.
:::

This lab involves deploying virtual machines into Amazon EC2 using Vagrant. More detailed use of SSH is also explored.

As before, when using Vagrant, in the CS Labs you will need to boot into macOS.

:::warning
:warning: 
Note that if Vagrant presents error messages such as "Request has expired", then you should check your AWS Academy credentials, since the AWS_SESSION_TOKEN etc. roll-over periodically. VMs that you created before your AWS session token expires will be accessible after you refresh your AWS session token, although it's possible that the VMs may need to be brought back from a paused or shutdown state.
:::

## Prepare your Vagrant environment

:::danger
:bomb: Using Vagrant to control EC2 is complicated by an increasing number of factors. You may well find that it is simplest just to manually deploy your assignment two work to EC2. You are welcome to do so.
:::

:::warning
:warning: In the CS Labs an appropriately old version of Vagrant should be running. For your own computers please download Vagrant version 2.2.15 or below (e.g., I am running version 2.2.14 on my laptop).
:::

:::info
These steps should only need to be performed once for a given installation of Vagrant, or for a given user account within the CS Lab environment. This should be true even if you subsequently use Vagrant to deploy further VMs to EC2.
:::

:::warning
:warning: 
The CS Lab environment requires additional steps due to the sysadmins holding back software updates so as to maximise stability. Unfortunately for many cloud tools, latest is greatest, and dependencies change rapidly. The instructions below divide in a few places between what should work on your own computer running the most recent Vagrant download, and what is necessary on the CS Labs.
:::

- Vagrant does not come with a built-in capability to deploy VMs to Amazon EC2, but Vagrant does have a plug-in system that allows for such extensions in functionality.
:::info
Vagrant is not likely to be widely used for AWS resource management. However given that you are familiar with Vagrant from earlier COSC349 lab exercises, use of the AWS provider for Vagrant is being presented for your convenience.

It would be much more common to use a cloud-focused orchestration tool such as Terraform (by the same developers that wrote Vagrant), or possibly just custom scripting built over the `aws` command line tool. (Note that as from 2021 the `aws` command line tool should be installed within the CS lab macOS environment.)
:::
- Install the AWS plug-in into your Vagrant environment:
    - Download this fork of the AWS plugin https://github.com/bdwyertech/vagrant-aws/releases/download/v0.8.0-bdwyertech/vagrant-aws-0.8.0.gem
    - Install the local `vagrant-aws-0.8.0.gem` that you just downloaded (macOS/Linux/PowerShell syntax for a file in the current directory---Windows cmd shell syntax might need `.\` instead of `./`).
     ```shell=-
    vagrant plugin install ./vagrant-aws-0.8.0.gem
     ```
    
It will take a while to download and configure the plug-in. Installation should not require super-user privileges.
- The other configuration step required is to download a dummy Vagrant box. Since AWS EC2 provides its own infrastructure for disk images (i.e., AMIs), the Vagrant box facilities are unneeded. (Vagrant boxes can contain AWS configuration parameters, but in this lab we will be also to set these sorts of parameters just as easily, within the `Vagrantfile`.)

```
vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
```



## Repository with an initial `Vagrantfile`

- A git repository that provides a starting point for your `Vagrantfile` is available at https://altitude.otago.ac.nz/cosc349/lab09-vagrant-aws
- `git clone` this repository so that you acquire a local copy.
- As for [Lab 8], you will need to have your AWS API credentials set up so that Vagrant can work on your behalf. The `aws` command and Vagrant AWS plugin can read details from the `credentials` configuration files (often stored at `~/.aws/credentials`), however the simplest approach may be to use shell environment variables to your AWS API credentials.

## Set shell environment variables for AWS use

- Amazon has defined a number of shell environment variables that can pass useful configuration information to the `aws` command (see https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html). These environment variables are also used by the Vagrant AWS plug-in.
- Rather than putting your AWS API access credentials into your `Vagrantfile`, though, in this lab exercise you will define shell environment variables for the three authentication-related parameters required. These shell environment variables will be available to commands you run subsequently, until you exit the shell in which they are defined.
- My apprach was to copy the API credentials from the AWS Academy Learner Lab information page (as for [Lab 8]) and paste the data into a temporary text file. Those credentials will have the following sort of form (although I manually destroyed the specific values here, as you should never share your API details):
```
[default]
aws_access_key_id=ASJD83JMFNMFKJJJFFDA
aws_secret_access_key=oXdfjerj35jg+aMsdfjerDGJjfeERFjrferFFf6K
aws_session_token=FwoGadfgaADFSDAFsdffsdFSDFSDFfdsdfSDFSF4sdgDFDSFzhh+83AXXR+RASdffasdfEWRDFasdfasDFSADFdfdsDEFW/IQDFGdfgegsdh45wgsdgfsrgfRsgrwrgrwtytr5t4gregetrZGERTGERT6zeLpnMXQ89IQ/2SLNasASDFJSfj45hsfSdghjDFGDFGDFhrheSDFSDFWehetrwFDJSDFJDS4534SDGFJSTRfjsdfjsdFSDFJESJFFF25TkBl/2GasdfJEWJFJEWJFVN3234nfSDFJASDFJASDFnfdwenfhsdf12312FSDJFDEKFSJVD234fvdsdjgjv3jdsfjsjfEdsjjfjew4x
```
- Copy the necessary secret values from the right-hand side (i.e., to the right of the `=`) of each of the above three variables into the right-hand-side of the three shell environment variables below. I saved the three `exprt` lines into a temporary file in my text editor so that I could then copy-paste the commands into shell windows whenever I needed that shell to have my AWS API credentials set (either for use of `aws` or use of the Vagrant AWS plug-in). Note that the left-hand-side naming conventions are different (!).

For macOS or Linux users, use the following form:
```
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_SESSION_TOKEN=
```

For Windows PowerShell users, do the following
```
$env:AWS_ACCESS_KEY_ID=""
$env:WS_SECRET_ACCESS_KEY=""
$env:AWS_SESSION_TOKEN=""
```

## Ensure you have access to the `aws` command

- The CS Lab computers should now—under macOS—include the `aws` command line tool. If you can run `aws` in a terminal, you do not need to set up a complete VM to access the `aws` command, and can skip the following subsection.
### Prepare a separate VM that can use the `aws` command

- The Vagrant configuration steps later in Lab , involve fetching some information that can be gathered by the `aws` command line interface.
- Thus, you will need to have access to the `aws` command, and in the instructions here for Lab 9 we suggest how to use the VM from [Lab 8] to achieve this.
- [Lab 8] describes how to use Vagrant to create a machine into which you set up AWS credentials, and that has the `aws` command installed.
- Open a terminal window focused on your [Lab 8] VM and a separate terminal window working through this lab.
- Ensure that you have done the usual `vagrant up`, `vagrant ssh` invocations successfully in your [Lab 8] window, and have tested that the `aws` command works as expected.
- Remember that Vagrant isolates state within each directory, so you are able to use multiple independent Vagrant VMs from different directories at the same time.
- In the [Lab 8] VM, test that your credentials are working as expected. For me, this involved pasting the three `export` lines from my temporary text editor window into my VM's shell, and then running the `aws` command. (The command below I've used for testing the `aws` command shows my lack of removing resources following completion of [Lab 8]...)

```
vagrant@ubuntu-focal:~$ aws s3 ls
2022-09-05 23:32:22 dme26-2022-test
2022-09-05 23:36:57 logs.dme26-2022-test
```



## Create a keypair on EC2, if you need to

:::info
:confused: 
For some reason, I have described the process here in lab 9 for creating a new key pair as if you have never done so... but you probably created a key pair back in lab 4! You can use the key pair you created back in lab 4, and skip creating another one, and thus this section. In any case, having multiple key pairs will not break anything---provided that you match up the name of the key pair (used by AWS to look it up) with the private key file that you specify (used by your local software to prove to AWS that it's your key).
:::

:::info
:information_source: 
The screenshots in this section are from a past year (in 2022 I chose to re-use my lab 4 keypair!), but should provide enough context to be useful even so. Let the teaching team know if this is not the case!
:::

- We will define an AWS key pair with a given name that can be loaded into EC2 instances that you create.
- Navigate your web browser to your AWS console:

![](https://i.imgur.com/GiCRDrv.png)

- ... more specifically with the goal of reaching your EC2 console:

![](https://i.imgur.com/Nv9FjUU.png)

- Note that in case there are "Key pairs 0" defined in the "Resources" displayed at the top of the console page. Let's change that.
- On the left-menu under "Network & Security", select "Key Pairs":

![](https://i.imgur.com/Q71sM1v.png)
![](https://i.imgur.com/MNiD3JK.png)

- From there, click the red "Create key pair" button, and provide a key pair name (e.g., `cosc349-2022`):

![](https://i.imgur.com/Zw0EDrT.png)

- Your key should now be listed:

![](https://i.imgur.com/N4iYXeK.png)

- ... but **in addition**, the private portion of your key-pair will have been downloaded by your web browser. For example, Safari is likely to place your key in a path such as `Downloads/cosc349-2022.pem` within your home directory (noting that this filename embeds the key pair's name).
- Keys should be protected in terms of file system permissions, and stored in a consistent, useful place. It is usually ideal for SSH key pairs to be stored in the `.ssh` directory within your home directory, since this is where tools such as OpenSSH (i.e., `ssh`) will default to looking. To address the location and permissions of your file, execute commands similar to the following (for macOS / Linux):

```shell=-
mv ~/Downloads/cosc349-2022.pem ~/.ssh/
chmod 700 ~/.ssh/cosc349-2022.pem
```

- You need to update this lab's `Vagrantfile` to contain the key pair name (`cosc349-2022` here), and the path to your `.pem` file. Comments within the `Vagrantfile` will (hopefully) guide you to the right place.

## Set your EC2 instance type

- You need to choose the type of EC2 instance you want to deploy—keeping in mind the different costs that types of instances incur.
- The `Vagrantfile` for this lab suggests one of the cheapest instances `t2.micro` in a source-code comment. This is a safe (but slow and underpowered) default, but if you do not uncomment this line, then you will get the AWS Vagrant provider's default, which is a somewhat larger and more expensive instance type.

## Configure Vagrant and EC2 networking

The configuration of security groups, subnet IDs, AMIs, etc., often involve work that only needs to be done once. For example, once you have settled on an AMI for the OS you want to use for your EC2 instances, you can simply copy and paste that data into subsequent `Vagrantfile`s that you might need to set up.

### Security groups

- Amazon security groups define network properties such as open network ports. They are scoped within the "virtual private cloud" (VPC) that EC2 launches VMs within.
- We will not seek to interact with the VPC itself, but just create security groups of use within the VPC.
- In the EC2 console, under "Network & Security", navigate to "Security Groups":

![](https://i.imgur.com/IHyu2lX.png)

- Click the "Create security group" button:

![](https://i.imgur.com/6BtqDEI.png)

- Give your security group a name (e.g., `cosc349-ssh`) and a description.

![](https://i.imgur.com/9mUYrNo.png)

- Also, check that the outbound rule for "All traffic" is set to "Custom" "0.0.0.0/0", which means that your VMs will be able to initiate outbound connections without restriction. (This was already the default in my case.)
- For the "Inbound" configuration, click "Add Rule" and for type "SSH" allow source "Anywhere-IPv4". (Of course in production use commercially you would want to lock this down much more specifically.)

![](https://i.imgur.com/m2aP86h.png)

- After clicking "Create", you should see your new security group listed:
 
![](https://i.imgur.com/0EHCpjt.png)

- Copy the "Group ID" (something like `sg-016880c1cb05410dd`) and paste it as the value of the `aws.security_groups` parameter within your `Vagrantfile`.

### Availability zone and subnet ID

- In general, creating an EC2 instance should only involve selection of the Amazon region (e.g., `us-east-1`, as is required by Amazon Academy), and EC2 will select the particular availability zone to use within the region.
- Using Vagrant within Amazon Academy accounts seems to require selecting a specific availability zone within the region (it does not matter which). Availability zones are usually named with a letter after the region, such as `us-east-1a`.
- Select an availability zone, and put your choice into the `Vagrantfile` for this lab.
- Now you need to find the subnet ID for your chosen availability zone. The approach taken in this lab to finding the subnet ID is to use the `aws` command line tool, using it from within the VM you created in [Lab 8]. You are welcome to find a different way to get this to work.
- Run the command `aws ec2 describe-subnets --region us-east-1` (e.g., in your [Lab 8] VM terminal, or using `aws` on the CS lab macOS environment).
- This command provides a JSON response:

```json=
{
    "Subnets": [
        {
            "AvailabilityZone": "us-east-1c",
            "AvailabilityZoneId": "use1-az1",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.0.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-08656356657f8cf9e",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-08656356657f8cf9e",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        },
        {
            "AvailabilityZone": "us-east-1d",
            "AvailabilityZoneId": "use1-az2",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.80.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-0191621f39c96720e",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-0191621f39c96720e",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        },
        {
            "AvailabilityZone": "us-east-1e",
            "AvailabilityZoneId": "use1-az3",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.48.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-03d00644d7228e902",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-03d00644d7228e902",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        },
        {
            "AvailabilityZone": "us-east-1a",
            "AvailabilityZoneId": "use1-az4",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.16.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-00bc365d464af3faf",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-00bc365d464af3faf",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        },
        {
            "AvailabilityZone": "us-east-1f",
            "AvailabilityZoneId": "use1-az5",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.64.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-003126171f9868015",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-003126171f9868015",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        },
        {
            "AvailabilityZone": "us-east-1b",
            "AvailabilityZoneId": "use1-az6",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.32.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-03c26616dbd442774",
            "VpcId": "vpc-09a8d17e143ab4828",
            "OwnerId": "409523232788",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:us-east-1:409523232788:subnet/subnet-03c26616dbd442774",
            "EnableDns64": false,
            "Ipv6Native": false,
            "PrivateDnsNameOptionsOnLaunch": {
                "HostnameType": "ip-name",
                "EnableResourceNameDnsARecord": false,
                "EnableResourceNameDnsAAAARecord": false
            }
        }
    ]
}
```

- Find the block corresponding for the `AvailabilityZone` that you selected and copy its `SubnetId` value into your `Vagrantfile`.

## Find an appropriate AMI

- Before you can launch your EC2 instance, you need to determine what disk image the VM will boot from—an Amazon Machine Image (AMI).
- From the EC2 Dashboard, under "Images" you can select "AMIs", and use the search facilities provided. Note that the initial display defaults to showing AMIs that you have created.

![](https://i.imgur.com/1iByLep.png)

- ... but changing the "Owned by me" to "Public images", will show a longer list (where "longer" can be interpreted here as "a list with more than 120,000 items in it").

![](https://i.imgur.com/vAgXQrY.png)

- You want to know the origin of these AMIs, as it is possible that they are not secure.
- An alternative, if you want to use a Ubuntu VM, is to use Ubuntu's "[Amazon EC2 AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/)": 

![](https://i.imgur.com/2gFcvkn.png)

- The "Search" box allows quick filtering of the list. In this case the terms entered were:
    - `us-east-1` since each region has different AMIs;
    - `hvm` since that is the type of virtualisation that we wish to use;
    - `amd64` since that's the CPU architecture I desired (for some reason I find `amd64` visually similar to `arm64` in some typefaces, but they're very different choices!).

- Having made those search selections, the list narrows to different versions of Ubuntu.

![](https://i.imgur.com/NK8Piz1.png)

- Copy and paste the AMI into the `Vagrantfile` for this lab. (I chose `focal`, since it's the most recent LTS (long term support) release shown.)
- Note that when using Ubuntu, it is likely that the login user should be `ubuntu`, and thus the `override.ssh.username` parameter should be set as suggested in the comments within the `Vagrantfile`.

## Start an instance

:::danger
:warning: 
The `vagrant` command invocation below this warning box may fail with a Ruby error that concludes with `undefined method 'except'`, and if so, you can try this workaround.

The CS Lab environments require that you make a change to your `Vagrantfile` to fix a glitch in the installed Vagrant version. Since 2021, I found that I needed to make the same change on my work iMac too. 

You also need to do this in Windows 10 machines in order to get it to work properly

Insert the following at line 3 of your `Vagrantfile` (just above the comment `All Vagrant configuration is done below`): ([source of fix](https://github.com/mitchellh/vagrant-aws/issues/566#issuecomment-637184574))
```ruby=-
class Hash
  def slice(*keep_keys)
    h = {}
    keep_keys.each { |key| h[key] = fetch(key) if has_key?(key) }
    h
  end unless Hash.method_defined?(:slice)
  def except(*less_keys)
    slice(*keys - less_keys)
  end unless Hash.method_defined?(:except)
end
```
:::

- You should now be able to use Vagrant to start an EC2 instance on AWS, using:

```
vagrant up --provider=aws
```

- Note that once you have successfully run the above command, the VM state stored within the hidden `.vagrant` directory means that you do not need to add `--provider=aws` to subsequent Vagrant command invocations within or below that directory.
- Running the above `up` command should produce output such as the following:

```
Bringing machine 'default' up with 'aws' provider...
==> default: Warning! The AWS provider doesn't support any of the Vagrant
==> default: high-level network configurations (`config.vm.network`). They
==> default: will be silently ignored.
==> default: Warning! You're launching this instance into a VPC without an
==> default: elastic IP. Please verify you're properly connected to a VPN so
==> default: you can access this machine, otherwise Vagrant will not be able
==> default: to SSH into it.
==> default: Launching an instance with the following settings...
==> default:  -- Type: t2.micro
==> default:  -- AMI: ami-079ca844e323047c2
==> default:  -- Region: us-east-1
==> default:  -- Availability Zone: us-east-1a
==> default:  -- Keypair: cosc349 2022
==> default:  -- Subnet ID: subnet-00bc365d464af3faf
==> default:  -- Security Groups: ["sg-016880c1cb05410dd"]
==> default:  -- Block Device Mapping: []
==> default:  -- Terminate On Shutdown: false
==> default:  -- Monitoring: false
==> default:  -- EBS optimized: false
==> default:  -- Source Destination check:
==> default:  -- Assigning a public IP address in a VPC: false
==> default:  -- VPC tenancy specification: default
==> default: Waiting for instance to become "ready"...
==> default: Waiting for SSH to become available...
==> default: Machine is booted and ready for use!
==> default: Rsyncing folder: /Users/dme26/tmp/lab09-vagrant-aws/ => /vagrant
```

- It should now be possible to connect to that EC2 instance using the normal Vagrant invocation `vagrant ssh`, for which output should be similar to what follows (if your choice of AMI was similar to mine):

```
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.15.0-1019-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Sep 12 11:45:08 UTC 2022

  System load:  0.18              Processes:             104
  Usage of /:   19.9% of 7.57GB   Users logged in:       0
  Memory usage: 24%               IPv4 address for eth0: 172.31.20.114
  Swap usage:   0%


0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '22.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


ubuntu@ip-172-31-20-114:~$ ls /vagrant/
Vagrantfile
```

- The `ls` command that was run on this EC2 instance indicates that the local directory containing this lab's `Vagrantfile` was copied over to the EC2 instance.

:::warning
:warning: 
Note that the synchronisation of files to EC2 **only happens once** during the `vagrant up` process, and is not dynamically synchronised. This is different behaviour to how shared folders work using Vagrant's VirtualBox provider.
:::

- You can verify that outgoing network access is permitted, by running commands such as the following on your EC2 instance (I'm essentially manually tracking how https://www.cs.otago.ac.nz/ redirects to https://www.otago.ac.nz/computer-science/index.html through an intermediate step):

```
ubuntu@ip-172-31-20-114:~$ curl --head https://www.cs.otago.ac.nz/
HTTP/1.1 301 Moved Permanently
Date: Mon, 12 Sep 2022 11:46:22 GMT
Server: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16 SVN/1.7.14
Strict-Transport-Security: max-age=63072000; includeSubDomains;
Location: https://www.otago.ac.nz/computer-science/
Content-Type: text/html; charset=iso-8859-1

ubuntu@ip-172-31-20-114:~$ curl --head https://www.otago.ac.nz/computer-science/
HTTP/2 301
date: Mon, 12 Sep 2022 11:46:34 GMT
content-type: text/html; charset=utf-8
content-length: 108
location: /computer-science/index.html
x-oracle-dms-rid: 0
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
x-frame-options: SAMEORIGIN
cache-control: private, max-age=60
set-cookie: JSESSIONID=8c4xhcZTe6z438QAKu_se_b_1zsqB90ijiCB48CWyc3CC7Nr6q6x!-1162065392; path=/; HttpOnly
vary: Accept-Encoding
set-cookie: servedby=ffffffff094a0a5145525d5f4f58455e445a4a42378c;expires=Mon, 12-Sep-2022 11:48:41 GMT;path=/;secure;httponly

ubuntu@ip-172-31-20-114:~$ curl --head https://www.otago.ac.nz/computer-science/index.html
HTTP/2 200
date: Mon, 12 Sep 2022 11:46:48 GMT
content-type: text/html; charset=utf-8
content-length: 28193
x-oracle-dms-rid: 0
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
x-frame-options: SAMEORIGIN
cache-control: private, max-age=60
set-cookie: JSESSIONID=rpoxhf7PgQq8lA_V1w9-7axOMuNEass8S0MUZ1JhttFMwDyLW51f!-1162065392; path=/; HttpOnly
vary: Accept-Encoding
set-cookie: servedby=ffffffff094a0a5145525d5f4f58455e445a4a42378c;expires=Mon, 12-Sep-2022 11:48:55 GMT;path=/;secure;httponly
```

## Setting up a web server

:::success
:book: 
Exercise:
- Using your access to your VM through `vagrant ssh`, install a web server. 
- You can use commands such as `curl` or `wget` to test it locally, but to be able to access your web server from the Internet you will need to change the security groups for this VM, as described in the following section.
:::

### Changing security groups for a VM

- You can change dynamically the security groups that a VM is within.
- For example, to support web access to your VM, create another security group, such as:

![](https://i.imgur.com/oePxV7r.png)

- Select (tick box at left-hand-side of instance table) the EC2 instance that you wish to reassign security groups for, and navigate through the menu options shown. (Actions → Security → Change security groups.)

![](https://i.imgur.com/HFcdeYE.png)

- You can then add and remove the security groups you wish the VM to be associated with:

![](https://i.imgur.com/Hjnhqnv.png)

- ... adding web access, in this case:

![](https://i.imgur.com/397I3YU.png)

### Testing your web server

- On the EC2 Dashboard, when an instance is selected, you can see its "Public DNS" entry, as shown in the following screen capture. Note that a "Copy to clipboard" helper is included. Also, an "open address" link will open a new tab, but note that it defaults to using `https` when for my test I needed to change this to `http` (I hadn't set up `https` on the Apache web server running on my VM).

![](https://i.imgur.com/lC7UTaF.png)

- You should be able to open this address in a web browser, if your server is set up correctly:

![](https://i.imgur.com/88DSFbq.png)

### Reworking this deployment

:::success
:book: Exercise:
- The ability to use provisioning scripts within Vagrant should continue to work when Vagrant is provisioning EC2 instances.
- Incrementally rework at least some of the manual steps (e.g., installing a web server) into a provisioning script that sets up your EC2 instance more automatically.
:::

## Check Vagrant status

- The `vagrant status` command should provide some information about the EC2 instance(s) that it is managing:

```
Current machine states:

default                   running (aws)

The EC2 instance is running. To stop this machine, you can run
`vagrant halt`. To destroy the machine, you can run `vagrant destroy`.
```

## Destroy your EC2 instance

- When you wish to remove your EC2 instance, the `vagrant destroy` command should work in a similar way to how it operates on VirtualBox VMs.

```
$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Terminating the instance...
$ vagrant status
Current machine states:

default                   not created (aws)

The EC2 instance is not created. Run `vagrant up` to create it.
```

- You should also check on the EC2 Dashboard that your instance really has been terminated... and if it hasn't been, you can always terminate it from the EC2 Dashboard manually.


