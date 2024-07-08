## Lab 9—Amazon EC2 using Hashicorp Terraform

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

This lab involves deploying virtual machines into Amazon EC2 using the [Terraform tool](https://www.terraform.io) from Hashicorp (i.e., the company that makes Vagrant).

:::warning
:warning: 
Note that if you receive confusing looking error messages from Terraform, you should check that your AWS Academy credentials have not expired. The `AWS_SESSION_TOKEN` etc., all roll-over periodically (every few hours). VMs that you created before your AWS session token expires will be accessible after you refresh your AWS session token, although it's possible that the VMs may need to be brought back from a paused or shutdown state.
:::

## Using Vagrant to access Terraform

The Owheo CS Labs do not have the AWS command line tools or Terraform installed on them. To provide a standardised environment that should also work on your home computers (apart from Arm Macs, for now, sorry---soon to be fixed), we instead install and use Terraform within a Vagrant VM.


:::danger
:bomb: The Docker branch of the provided Vagrant repository does not yet support Apple M! CPUs. This edition will be comming soon... :crossed_fingers:

However if you are willing to install Terraform under macOS and do all of the steps below on your host rather than the helper VM, you should be able to get the lab done equivalently.

Note that you do not need to automate deployment of your assignment two code and configuration, although you must document how you set up your assignment, otherwise.
:::

- A Git repository that installs Terraform inside a helper VM is available at  https://altitude.otago.ac.nz/cosc349/lab09-terraform-ec2
- `git clone` this repository so that you acquire a local copy.
- Change into your Git clone's directory
- `vagrant up` to set up the helper VM
- `vagrant ssh` into your helper VM
- Working inside the helper VM, as for [Lab 8], you will need to have your AWS Academy API credentials set up so that Vagrant can work on your behalf. As suggested by the AWS Academy page that provides you with your credentials (that remain valid for a few hours), store the crentials at `~/.aws/credentials`.

## (Optional) Check that your `aws` command is working

- This lab does not require use of the `aws` command line tools, but they can be used to test (and modify) the AWS configuration files (e..g, within `~/.aws` ) that Terraform _does_ rely on.
- Inside the helper VM, test that your `aws` command line tools are working (e.g., also testing your `credentials`).
- In my case I ran:
```
vagrant@ubuntu-focal:~$ aws s3 ls
2023-09-05 10:50:48 dme26-2023-test
2023-09-05 10:51:51 logs.dme26-2023-test
```
- ... with both indicates that my `aws` command line tools are working as expected, and also indicates that I did not follow my own advice to delete the S3 buckets that I was no longer using... (!)
- Run the `aws configure` command and set your region to `us-east-1`, since that is the only region that we can access using AWS Academy. The other options' default values you can accept just by pressing <kbd>return</kbd> or <kbd>enter</kbd>.

## Create a keypair on EC2, if you need to

:::info
:confused: 
For some reason, I have described the process here in lab 9 for creating a new key pair as if you have never done so... but you probably created a key pair back in lab 4! You can use the key pair you created back in lab 4, and skip creating another one, and thus this section. In any case, having multiple key pairs will not break anything---provided that you match up the name of the key pair (used by AWS to look it up) with the private key file that you specify (used by your local software to prove to AWS that it's your key).
:::

- you can see what keypairs you have available by running a command such as the following:
```
vagrant@ubuntu-focal:~$ aws ec2 describe-key-pairs | grep KeyName | grep -v vockey | cut -d'"' -f 4
cosc349-2023
```
- if you still have the private key file for the key name that is shown, you do not need to create another key pair and can skip to the next section.

:::info
:information_source: 
The screenshots in this section are from a past year (since 2022 I've chosen to re-use my lab 4 keypair!), but should provide enough context to be useful even so. Let the teaching team know if this is not the case!
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

- From there, click the red "Create key pair" button, and provide a key pair name (e.g., `cosc349-2023`):

![](https://i.imgur.com/Zw0EDrT.png)

- Your key should now be listed:

![](https://i.imgur.com/N4iYXeK.png)

- ... but **in addition**, the private portion of your key-pair will have been downloaded by your web browser into a file such as `cosc349-2023.pem`.
- Keys should be protected in terms of file system permissions, and stored in a consistent, useful place. It is usually ideal for SSH key pairs to be stored in the `.ssh` directory within your home directory, since this is where tools such as OpenSSH (i.e., `ssh`) will default to looking. To address the location and permissions of your file, execute commands similar to the following (for macOS / Linux / Git Bash on Windows):

```shell=-
mv ~/Downloads/cosc349-2023.pem ~/.ssh/
chmod 700 ~/.ssh/cosc349-2023.pem
```
- I launched SSH connections to my VMs below from my host computer. If you want to launch SSH connections from within the helper VM, you'll need to ensure that that VM has your `cosc349-2023.pem` (or equivalent) file in _its_ `~/.ssh/` directory, with the appropriate file permissions.

## Getting started with Terraform

- Let's create an initial test of Terraform within a folder checked out from the Git repository for lab 9. First, inside your VM, change into the shared folder: `cd /vagrant/tf-deploy`
- Note that I am creating this directory within the directory shared with the VM host: this is so that I can run a handy editor on the host or edit the configuration from within the VM.
- I then opened VSCode on my host computer pointed at the `tf-deploy` directory.
- Create a file named `main.tf` with the content:
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow inbound SSH traffic"

  ingress {
    description = "SSH from anywhere"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web_server" {
  ami           = "ami-010e83f579f15bba0"
  instance_type = "t2.micro"
  key_name      = "cosc349-2023"

  vpc_security_group_ids = [aws_security_group.allow_ssh.id]

  user_data = <<-EOF
              #!/bin/bash
              sudo apt update
              sudo apt install -y apache2
              sudo systemctl start apache2
              sudo systemctl enable apache2
              EOF

  tags = {
    Name = "WebServer"
  }
}

output "web_server_ip" {
  value = aws_instance.web_server.public_ip
}
```
- This is a declarative syntax that contains four top-level parts:
    - The `provider` is AWS operating in the `us-east-1` region
    - A security group is set up called "allow_ssh" that allows SSH traffic to reach a VM that uses that security group
    - A `resource` that defines a VM named "web_server"
    - An output section that indicates information that Terraform will display when its finished its work. 
- Run the `terraform init` command from which I get output:
```
Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v5.16.2...
- Installed hashicorp/aws v5.16.2 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
- Then run the `terraform plan` command, for which I get output:
```
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.web_server will be created
  + resource "aws_instance" "web_server" {
      + ami                                  = "ami-010e83f579f15bba0"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "cosc349-2023"
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "WebServer"
        }
      + tags_all                             = {
          + "Name" = "WebServer"
        }
      + tenancy                              = (known after apply)
      + user_data                            = "07c9939940fed692444ad659f6257659122880ac"
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

  # aws_security_group.allow_ssh will be created
  + resource "aws_security_group" "allow_ssh" {
      + arn                    = (known after apply)
      + description            = "Allow inbound SSH traffic"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = "SSH from anywhere"
              + from_port        = 22
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 22
            },
        ]
      + name                   = "allow_ssh"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + web_server_ip = (known after apply)

───────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't
guarantee to take exactly these actions if you run "terraform apply" now.
```
- If you're happy with the proposal Terraform has made, then you can enact something very similar to the present plan using the `terraform apply` command.
- Terraform will prompt you for confirmation, to which you say "yes" if you wish to proceed.
- For me, when that apply command finished, I was presented with lots of diagnostics, and an "Outputs:" section including `web_server_ip = "52.71.252.201"`.
- You should reload your AWS dashboard to confirm that you see the EC2 VM that you created using Terraform
## Connect to your EC2 VM

- Unlike Vagrant, Terraform does not provide a built in way to connect to your EC2 VM using SSH.
- However, you know the public IP address from the above, and should have your private key file handy, and thus be able to connect to your VM using SSH. In this case I am connecting from my macOS host computer:
```
→ ssh -i ~/.ssh/cosc349-2023.pem ubuntu@52.71.252.201
The authenticity of host '52.71.252.201 (52.71.252.201)' can't be established.
ED25519 key fingerprint is SHA256:nLF1ZIdUH7/HUaxP46Z+Jm3+bWFNroR6f1YECpFAuk4.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Welcome to Ubuntu 23.04 (GNU/Linux 6.2.0-1011-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Sep 12 09:56:10 UTC 2023

  System load:  0.08              Processes:             101
  Usage of /:   24.7% of 7.58GB   Users logged in:       0
  Memory usage: 22%               IPv4 address for enX0: 172.31.43.128
  Swap usage:   0%

0 updates can be applied immediately.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-43-128:~$
```
- You can test whether this VM is running the webserver that the `user_data` section intalled using:
- `sudo lsof -Pni`
```
ubuntu@ip-172-31-43-128:~$ sudo lsof -Pni
COMMAND    PID            USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd      1            root   98u  IPv6  16773      0t0  TCP *:22 (LISTEN)
systemd-r  238 systemd-resolve   13u  IPv4  15595      0t0  UDP 127.0.0.53:53
systemd-r  238 systemd-resolve   14u  IPv4  15596      0t0  TCP 127.0.0.53:53 (LISTEN)
systemd-r  238 systemd-resolve   15u  IPv4  15597      0t0  UDP 127.0.0.54:53
systemd-r  238 systemd-resolve   16u  IPv4  15598      0t0  TCP 127.0.0.54:53 (LISTEN)
systemd-n  303 systemd-network   18u  IPv4  16404      0t0  UDP 172.31.43.128:68
chronyd    488         _chrony    5u  IPv4  17358      0t0  UDP 127.0.0.1:323
chronyd    488         _chrony    6u  IPv6  17359      0t0  UDP [::1]:323
apache2   1920            root    4u  IPv6  21311      0t0  TCP *:80 (LISTEN)
apache2   1922        www-data    4u  IPv6  21311      0t0  TCP *:80 (LISTEN)
apache2   1923        www-data    4u  IPv6  21311      0t0  TCP *:80 (LISTEN)
sshd      2247            root    3u  IPv6  16773      0t0  TCP *:22 (LISTEN)
sshd      2278            root    4u  IPv6  23904      0t0  TCP 172.31.43.128:22->139.80.239.160:8237 (ESTABLISHED)
sshd      2398          ubuntu    4u  IPv6  23904      0t0  TCP 172.31.43.128:22->139.80.239.160:8237 (ESTABLISHED)
```
- Which on my VM indicates `apache2` is listening on TCP port 80.
- You can confirm that your VM is able to access the Internet, e.g., by running a `curl` command, such as in:
```
ubuntu@ip-172-31-43-128:~$ curl --head https://www.cs.otago.ac.nz/
HTTP/1.1 301 Moved Permanently
Date: Tue, 12 Sep 2023 10:00:18 GMT
Server: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16 SVN/1.7.14
Strict-Transport-Security: max-age=63072000; includeSubDomains;
Location: https://www.otago.ac.nz/computer-science/
Content-Type: text/html; charset=iso-8859-1

```
- However, we cannot access the machine from the Internet as the security groups block HTTP traffic.

:::success
:book: Using the `allow_ssh` security group resource as a starting point, add into your `main.tf` a resource defining a security group named `allow_web` that opens port 80 and port 443 to all internet traffic.
:::

- When you have defined your `allow_ssh` resource, add your new security group ID into the `vpc_security_group_ids` list wtihin the resource defining your web server.
- You should be able to run `terraform apply` and Terraform should be able to figure out what to do.
- Now see whether you can access the public IP address in your web browser, for example I accessed http://52.71.252.201/ (take care not to have your browser switch this to HTTPS). I see:
![](https://hackmd.io/_uploads/ByKazAaC2.png)

## Add a database server

- Add to your `main.tf` a new `aws_instance` resource named `mysql_server`. You can use the following `user_data` to install MySQL.
```
user_data = <<-EOF
            #!/bin/bash
            sudo apt update
            sudo apt install -y mysql-server
            sudo systemctl start mysql
            sudo systemctl enable mysql
            EOF
```
- You will need to create an `allow_mysql` security group (for port 3306).
- You should also ensure that your output shows the MySQL server public IP address
- Test that you can SSH to your MySQL server, and that you can use `lsof -Pni` to verify that your MySQL server is running.

## Telling the web server the database server's IP

- A useful feature of Terraform is that it will determine the necessary order to carry out actions.
- For example, if you add this into your `user_data` for your web server:
```
echo MYSQL_SERVER_IP=${aws_instance.mysql_server.private_ip} | sudo tee -a /etc/environment
```
- Then Terraform will replace the `${}` construct with the private IP of your database server, including determining that the database server needs to be created before your web server.
- You can test this if you want to, but note that you may need your VM to be redeployed to re-run the intitialisation in `user_data`.
- The command `terraform taint aws_instance.web_server` will inform Terraform that at the next `apply` the web server should be recreated.

## Use a template file to run setup shell scripting

We will replace the `user_data` structure within each VM to a form that instead replaces Terraform variables within an external file, rather than including the shell commands to run within `main.tf`. Your webserver can use a form such as:
```
user_data = templatefile("${path.module}/build-webserver-vm.tpl", { mysql_server_ip = aws_instance.mysql_server.private_ip })
```
... and your database seerver can use a form such as:
```
user_data = templatefile("${path.module}/build-dbserver-vm.tpl", { })
```

## Test the web server

The web server is configured in a slightly different way to the configuration used in the multi-VM Vagrant example that you've seen earlier in the paper.

The `index.html` file is at `/var/www/html/`, and the database testing page will be within the directory too. By default, Apache will continue to show the `index.html` page: you need to explicitly add `index.php` onto the URL in order to test that the PHP script installed on your web server VM does indeedd connect to your database server, as expected.

## If you get stuck in the exercises...

<details>
<summary>Just in case you are really stuck producing a `main.tf` that works, you can reveal one that works, here....</summary>

```
provider "aws" {
  region = "us-east-1"
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow inbound SSH traffic"

  ingress {
    description = "SSH from anywhere"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "allow_web" {
  name        = "allow_web"
  description = "Allow inbound HTTP(S) traffic"

  ingress {
    description = "HTTP from anywhere"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTPS from anywhere"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "allow_mysql" {
  name        = "allow_mysql"
  description = "Allow inbound MySQL traffic"

  ingress {
    description = "MySQL from anywhere"
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web_server" {
  ami           = "ami-010e83f579f15bba0"
  instance_type = "t2.micro"
  key_name      = "cosc349-2023"

  vpc_security_group_ids = [aws_security_group.allow_ssh.id,
              aws_security_group.allow_web.id]

  user_data = templatefile("${path.module}/build-webserver-vm.tpl", { mysql_server_ip = aws_instance.mysql_server.private_ip })

  tags = {
    Name = "WebServer"
  }
}

resource "aws_instance" "mysql_server" {
  ami           = "ami-010e83f579f15bba0"
  instance_type = "t2.micro"
  key_name      = "cosc349-2023"

  vpc_security_group_ids = [aws_security_group.allow_ssh.id,
                aws_security_group.allow_mysql.id]

  user_data = templatefile("${path.module}/build-dbserver-vm.tpl", { })

  tags = {
    Name = "MySQLServer"
  }
}

output "web_server_ip" {
  value = aws_instance.web_server.public_ip
}

output "mysql_server_ip" {
  value = aws_instance.mysql_server.public_ip
}
```
</details>

## Destroy your EC2 instance

- When you wish to remove your EC2 instance, the `terraform destroy` command should work to remove resources, after you confirm this action.
- You should also check on the EC2 Dashboard that your instance really has been terminated... and if it hasn't been, you can always terminate it from the EC2 Dashboard manually.
