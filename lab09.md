---
tags: cosc349
---
# COSC349 Lab 9—Cloud Architecture—2019
## Lab 9—Amazon EC2 via Vagrant

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

This lab involves deploying virtual machines into Amazon EC2 using Vagrant. More detailed use of SSH is also explored.

As before, when using Vagrant, in the CS Labs you will need to boot into macOS.

:::warning
Note that if Vagrant presents error messages such as "Request has expired", then you should check your AWS Educate workbench webpage, since the AWS_SESSION_TOKEN etc. sometimes roll-over. 

Refreshing your credentials will not lose the VMs and other resources that you have already created.
:::

## Prepare your Vagrant environment

:::info
These steps should only need to be performed once (for a given home directory), even if you subsequently use Vagrant to deploy VMs to EC2.
:::

- Vagrant does not come with a built-in capability to deploy VMs to Amazon EC2, but Vagrant does have a plug-in system that allows for such extensions in functionality.
- Install the AWS plug-in into your Vagrant environment:

```shell=-
vagrant plugin install vagrant-aws
```

- This will take a while to download, and configure the plug-in. Installation was tested in the CS Lab environments, and should not require super-user privileges.

- The other configuration step required is to download a dummy Vagrant box. Since AWS EC2 provides its own infrastructure for disk images (i.e., AMIs), the Vagrant box facilities are unneeded. (Vagrant boxes can contain AWS configuration parameters, but in this lab we will be also to set these sorts of parameters just as easily, within the `Vagrantfile`.)

```
vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
```



## Repository with an initial `Vagrantfile`

- A git repository that provides a starting point for your `Vagrantfile` is available at https://altitude.otago.ac.nz/cosc349/lab09-vagrant-aws

- `git clone` this repository so that you acquire a local copy.

- You will need to have your AWS API credentials set up so that Vagrant can work on your behalf. 

- Within your `git` working copy for the above repository, create a directory in which to store your credentials:

```shell=-
mkdir .aws
chmod 700 .aws
```

- Initialise the `credentials` file within this `.aws` directory using the process you used within [Lab 8].


## Set environment variables for AWS use

- Amazon has defined a number of shell environment variables that can pass useful configuration information to the `aws` command (see https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html). These environment variables are also used by the Vagrant AWS plug-in.

- Rather than putting your AWS API access credentials into your `Vagrantfile`, in this lab you will define shell environment variables for the three authentication-related parameters required.

- Copy the necessary secret values from your `.aws/credentials` file into the right-hand side of each of the first three of these shell environment variables.

```
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_SESSION_TOKEN=
```

## Prepare a VM that has the `aws` command

- The Vagrant configuration steps below involve information that can be gathered by the `aws` command line interface.
- Thus, you will need to have access to the `aws` command, and in the instructions here for Lab 9 we suggest how to use the VM from [Lab 8] to achieve this.
- [Lab 8] describes how to use Vagrant to create a machine into which you set up AWS credentials, and that has the `aws` command installed.
- Open a terminal window focused on your [Lab 8] VM and a separate terminal window working through this lab.
- Ensure that you have done the usual `vagrant up`, `vagrant ssh` invocations successfully in your [Lab 8] window, and have tested that the `aws` command works as expected.

- Remember that Vagrant isolates state within each directory, so you are able to use Vagrant VMs in multiple directories at once.

- In the [Lab 8] VM, test that your credentials are working as expected: (In this case here showing a lack of resource removal following completion of [Lab 8]...)

```
vagrant@ubuntu-xenial:~$ aws s3 ls
2019-09-02 11:59:35 dme26-test
2019-09-02 12:01:36 logs.dme26-test
```



## Create keypair on EC2

- We will define an AWS keypair with a given name that can be loaded into EC2 instances that you create.

- Navigate your web browser to your AWS console:

![](https://i.imgur.com/wymLCkB.png)

- ... more specifically with the goal of reaching your EC2 console:

![](https://i.imgur.com/chkCKRo.png)

- Note that in case there are "0 Key Pairs" defined in the "Resources" displayed at the top of the console page. Let's change that.

- On the left-menu under "Network & Security", select "Key Pairs":

![](https://i.imgur.com/UGPGt28.png)

- From there, click the "Create Key Pair" button, and provide a key pair name (e.g., `cosc349`):

![](https://i.imgur.com/FtIwR9g.png)

- Your key should now be listed:

![](https://i.imgur.com/EUlh2Co.png)

- ... but in addition, the private portion of your key-pair will have been downloaded by your web browser. For example, Safari is likely to place your key in a path such as `/Downloads/cosc349.pem.txt` (noting that this filename embeds the key pair's name).

- Keys should be protected in terms of file system permissions, and stored in a consistent, useful place. It is usually ideal for SSH key pairs to be stored in the `.ssh` directory within your home directory, since this is where tools such as OpenSSH (i.e., `ssh`) will default to looking. To address the location and permissions of your file, execute commands similar to the following:

```shell=-
mv ~/Downloads/cosc349.pem.txt ~/.ssh/cosc349.pem
chmod 700 ~/.ssh/cosc349.pem
```

- You need to update this lab's `Vagrantfile` to contain the key pair name, and the path to your `.pem` file. Comments within the `Vagrantfile` will (hopefully) guide you to the right place.

## Set your instance type

- You need to choose the type of EC2 instance you want to deploy—keeping in mind the different costs.

- The `Vagrantfile` for this lab suggests one of the cheapest instances `t2.micro` in a source-code comment. This is a safe (but slow and underpowered) default, but if you do not uncomment this line, then you will get the AWS Vagrant provider's default, which is a somewhat larger and more expensive instance type.

## Configure Vagrant and EC2 networking

### Security groups

- Amazon security groups define network properties such as open network ports. They are scoped within the "virtual private cloud" (VPC) that EC2 launches VMs within.

- We will not seek to interact with the VPC itself, but just create security groups of use within the VPC.

- In the EC2 console, under "Network & Security", navigate to "Security Groups":

![](https://i.imgur.com/TSvP7zZ.png)

- Click the "Create Security Group" button: (Note that your web interface will likely have the "Inbound" tab selected, whereas mine is initially showing "Outbound")

![](https://i.imgur.com/EIOOvxr.png)

- Give your security group a name (e.g., `cosc349-ssh`).

- Also, check that the outbound rule for "All traffic" is set to "Custom" "0.0.0.0/0", which means that your VMs will be able to initiate outbound connections without restriction.

![](https://i.imgur.com/WkJkqWe.png)

- For the "Inbound" configuration, click "Add Rule" and for type "SSH" allow source "Anywhere". (Of course in production use commercially you would want to lock this down much more specifically.)

![](https://i.imgur.com/DHehvQw.png)

- After clicking "Create", you should see your new security group listed:
 
![](https://i.imgur.com/jrJxTYT.png)

- Copy the "Group ID" and paste it as the value of the `aws.security_groups` parameter within your `Vagrantfile`.

### Availability zone and subnet ID

- In general, creating an EC2 instance should only involve selection of the Amazon region (e.g., `us-east-1`, as is required by Amazon Educate), and EC2 will select the particular availability zone to use within the region.

- Using Vagrant within Amazon Educate accounts seems to require selecting a specific availability zone within the region (it does not matter which). Availability zones are usually named with a letter after the region, such as `us-east-1a`.

- Select an availability zone, and put your choice into the `Vagrantfile` for this lab.

- Now you need to find the subnet ID for your chosen availability zone. The approach taken in this lab to finding the subnet ID is to use the `aws` command line tool, using it from within the VM you created in [Lab 8]. You are welcome to find a different way to get this to work.

- Run the command `aws ec2 describe-subnets --region us-east-1` (e.g., in your [Lab 8] terminal)

- This command provides a JSON response:

```json=
{
    "Subnets": [
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az3",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.64.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-1183e42f",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-1183e42f",
            "AvailabilityZone": "us-east-1e",
            "State": "available",
            "OwnerId": "532201019705"
        },
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az6",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.32.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-ef89d5b3",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-ef89d5b3",
            "AvailabilityZone": "us-east-1b",
            "State": "available",
            "OwnerId": "532201019705"
        },
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az1",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.0.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-6831620f",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-6831620f",
            "AvailabilityZone": "us-east-1c",
            "State": "available",
            "OwnerId": "532201019705"
        },
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az2",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.80.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-427b256c",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-427b256c",
            "AvailabilityZone": "us-east-1d",
            "State": "available",
            "OwnerId": "532201019705"
        },
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az5",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.48.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-d4283ddb",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-d4283ddb",
            "AvailabilityZone": "us-east-1f",
            "State": "available",
            "OwnerId": "532201019705"
        },
        {
            "VpcId": "vpc-87dfb3fd",
            "AvailabilityZoneId": "use1-az4",
            "AssignIpv6AddressOnCreation": false,
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.16.0/20",
            "MapPublicIpOnLaunch": true,
            "Ipv6CidrBlockAssociationSet": [],
            "DefaultForAz": true,
            "SubnetId": "subnet-95fa19d8",
            "SubnetArn": "arn:aws:ec2:us-east-1:532201019705:subnet/subnet-95fa19d8",
            "AvailabilityZone": "us-east-1a",
            "State": "available",
            "OwnerId": "532201019705"
        }
    ]
}
```

- Find the block corresponding for the `AvailabilityZone` that you selected and copy its `SubnetId` value into your `Vagrantfile`.

## Find an appropriate AMI

- Before you can launch your EC2 instance, you need to determine what disk image the VM will boot from—an Amazon Machine Image (AMI).

- From the EC2 Dashboard, under "Images" you can select "AMIs", and use the search facilities provided. Note that the initial display defaults to showing AMIs that you have created.

![](https://i.imgur.com/tRyZwx6.png)

- ... but changing the "Owned by me" to "Public images", will show a longer list (where "longer" can be interpreted here as "a list with more than 120,000 items in it").

![](https://i.imgur.com/OOnE9WV.png)

- You want to know the origin of these AMIs, as it is possible that they are not secure.

- An alternative, if you want to use a Ubuntu VM, is to use Ubuntu's "[Amazon EC2 AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/)": 

![](https://i.imgur.com/lA6xtU3.png)

- The "Search" box allows quick filtering of the list. In this case the terms entered were:
    - `us-east-1` since each region has different AMIs;
    - `xenial` since that's the Ubuntu version desired in this case;
    - `hvm` since that is the type of virtualisation that we wish to use.

- Having made those selections the top entry is the desired one, since it is `amd64` rather than `arm64`, and since it uses `EBS` rather than an instance store.

![](https://i.imgur.com/FrLyZ93.png)

- Copy and paste the AMI into the `Vagrantfile` for this lab.

- Note that when using Ubuntu, it is likely that the login user should be `ubuntu`, and thus the `override.ssh.username` parameter should be set as suggested in the comments within the `Vagrantfile`.

## Start an instance

- You should now be able to use Vagrant to start an EC2 instance on AWS, using:

```
vagrant up --provider=aws
```

- Note that once you have successfully run the above command, the VM state stored within the hidden `.vagrant` directory means that you do not need to add `--provider=aws` to subsequent command invocations.

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
==> default:  -- AMI: ami-0378588b4ae11ec24
==> default:  -- Region: us-east-1
==> default:  -- Availability Zone: us-east-1a
==> default:  -- Keypair: cosc349
==> default:  -- Subnet ID: subnet-95fa19d8
==> default:  -- Security Groups: ["sg-0c7fbc2e60785fe86"]
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
==> default: Rsyncing folder: /Users/dme26/checkouts/cosc349-lab09-vagrant/ => /vagrant
```

- It should now be possible to connect to that EC2 instance using the normal Vagrant invocation `vagrant ssh`, for which output should be similar to:

```
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-1090-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

New release '18.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


_____________________________________________________________________
WARNING! Your environment specifies an invalid locale.
 The unknown environment variables are:
   LC_CTYPE=en_NZ.UTF-8 LC_ALL=
 This can affect your user experience significantly, including the
 ability to manage packages. You may install the locales by running:

   sudo apt-get install language-pack-en
     or
   sudo locale-gen en_NZ.UTF-8

To see all available language packs, run:
   apt-cache search "^language-pack-[a-z][a-z]$"
To disable this message for all users, run:
   sudo touch /var/lib/cloud/instance/locale-check.skip
_____________________________________________________________________

ubuntu@ip-172-31-22-115:~$ ls /vagrant/
Vagrantfile
```

- The `ls` command that was run on this EC2 instance indicates that the local directory containing this lab's `Vagrantfile` was copied over to the EC2 instance.

:::warning
Note that the synchronisation of files to EC2 only happens during the `vagrant up` process, and is not dynamically synchronised. This is different behaviour to how shared folders work using Vagrant's VirtualBox provider.
:::

- You can verify that outgoing network access is permitted, but running commands such as the following on your EC2 instance:

```
ubuntu@ip-172-31-22-115:~$ curl --head https://www.cs.otago.ac.nz/
HTTP/1.1 301 Moved Permanently
Date: Mon, 09 Sep 2019 11:38:12 GMT
Server: Apache/2.4.6 (CentOS) PHP/5.4.16 SVN/1.7.14 OpenSSL/1.0.2k-fips
Strict-Transport-Security: max-age=63072000; includeSubDomains;
Location: https://www.otago.ac.nz/computer-science/
Content-Type: text/html; charset=iso-8859-1

ubuntu@ip-172-31-22-115:~$ curl --head https://www.otago.ac.nz/computer-science/
HTTP/1.1 301 Moved Permanently
Date: Mon, 09 Sep 2019 11:38:44 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 108
Connection: keep-alive
Location: /computer-science/index.html
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
Cache-Control: public, max-age=300, s-maxage=300
Set-Cookie: JSESSIONID=10IV0ajd_06M1FSMp2f-BmG8UzktYrzeTKCURXwP0bpt-WEls43U!841090299; path=/; HttpOnly
Vary: Accept-Encoding
Set-Cookie: servedby=ffffffff094a0a5045525d5f4f58455e445a4a42378c;expires=Mon, 09-Sep-2019 11:40:44 GMT;path=/;secure;httponly

ubuntu@ip-172-31-22-115:~$ curl --head https://www.otago.ac.nz/computer-science/index.html
HTTP/1.1 200 OK
Date: Mon, 09 Sep 2019 11:39:26 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 27006
Connection: keep-alive
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
Cache-Control: public, max-age=300, s-maxage=300
Set-Cookie: JSESSIONID=oI0V0jpoJ_fpUoXUhGvOgcXiRDT_6lU_7rV8zKW8UrAm2puCo6Qz!841090299; path=/; HttpOnly
Vary: Accept-Encoding
Set-Cookie: servedby=ffffffff094a0a5045525d5f4f58455e445a4a42378c;expires=Mon, 09-Sep-2019 11:41:26 GMT;path=/;secure;httponly
```

## Setting up a web server

:::success
Exercise:
- Using your access to your VM through `vagrant ssh`, install a web server. 
- You can use commands such as `curl` or `wget` to test it locally, but to be able to access your web server from the Internet you will need to change the security groups for this VM, as described in the following section.
:::

### Changing security groups for a VM

- You can change dynamically the security groups that a VM is within.

- For example, to support web access to your VM, create another security group, such as:

![](https://i.imgur.com/WIsBaed.png)

- Select (left-hand box) the EC2 instance that you wish to reassign security groups for, and navigate through the menu options shown. (Actions -> Networking -> Change Security Groups.)

![](https://i.imgur.com/LL81y0X.png)

- You can then select and deselect the groups you wish the VM to be associated with:

![](https://i.imgur.com/PUywyge.png)

- ... adding web access, in this case:

![](https://i.imgur.com/cdwWvpv.png)

### Testing your web server

- On the EC2 Dashboard, when an instance is selected, you can see its "Public DNS" entry, as shown: (a "Copy to clipboard" helper is included)

![](https://i.imgur.com/TiTqj3q.png)

- You should be able to open this address in a web browser, if your server is set up correctly:

![](https://i.imgur.com/aETStsX.png)

### Reworking this deployment

:::success
Exercise:
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
