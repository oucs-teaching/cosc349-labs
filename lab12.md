---
tags: cosc349
---
# COSC349 Lab 12—Cloud Architecture—2021
## Lab 12—Work on assignment 2

This lab time has been set aside for you to work on assignment 2.

Some suggestions regarding techniques and approaches to problems that have been encountered are listed below. Feel free to augment this information.

## AWS Educate facilities

Note that there is documentation about the services supported (or not) by AWS Educate Starter Accounts (although apparenty accessing some of the database APIs don't behave as expected).

## Apache error handling

When Apache presents error pages, it writes additional information into its log files. For example a 403 error (access denied) can be due, for example, to the Apache user (e.g., `www-data`) not being able to read files owned by `root`.

When SSHed into your Apache VM, you can show the end of the error log using `sudo tail /var/log/apache2/error.log` (this is the location where Debian and Ubuntu Linux put their Apache log files: if you're using a different variant of Linux, you should check the its documentation for details).

As far as I can tell there is not an easy way to cause Apache to present this detailed information on the error pages themselves. In general this would be a very bad idea in terms of computer security, which is why I suspect that the facility is not provided.

## Transfer of files

When using the AWS provider in `vagrant` your local repository's files get copied over to your instances once at provisioning time. Note, however, that they get copied over as user `root`.

Thus, you are likely to need to change permissions and/or Unix user and/or group, so that your web server is able to read the files that have been transferred into `/vagrant`.

Lab 1's shell material links to COSC241's first lab, which covers use of relevant commands, such as `chmod` (change the permissions on a set of file), `chgrp` (change the Unix group of a set of files), and `chown` (change the Unix owner (and optionally also the group) of a set of files).

## Facilitating VMs finding out about each other's addresses

:::info
This section discusses mechanisms for automating the setup of sets of VMs. However please remember that automated deployment is **not** a requirement of assignment two! You are free to manually `ssh` into your instances to give them the details required to get your application running in the cloud. Having said that, automation is more convenient when it works smoothly, but you don't want to get stuck on it.
:::

- It's a common problem that a set of VMs need to find out each other's addresses so that they can interlink.
- You may have one-way needs (e.g., VM2 needs to know VM1's IP addresses), or two-way needs (VM1 and VM2 both need to find out each other's addreses).
- A common situation is that you will want, say, to SSH to a resource before SSH is actually ready on that resource. A typical solution is to try to SSH, setting a reasonably short time-out, then if the `ssh` command fails, `sleep` for a short time (a few seconds) and then try again.

### Multi-phase deployment

One approach is to do your deployment in multiple phases.
- Phase 1: provision your VMs
- Phase 2: accumulate information required to connect to your machines (since they've now been provisioned), then connect to each of your VMs to update this information, and run a script to complete the start up of the services your application needs.

For example, I've applied this type of approach running `vagrant up --provider=aws` within a shell script on my host machine. When the `vagrant` command is completed, there is information in the local Vagrant environment about the address of each machine (that's how `vagrant ssh` knows where to connect). This information can be collected and passed to VMs in phase 2.

### Use a shared resource to coordinate configuration

If you're using an approach such as `vagrant` to copy files to your VM, you can set up `.aws/credentials` to be made available, so that the provisioning script can run `aws` commands as "you".

You can set up a resource that all of your VMs can access, such as an S3 bucket that contains a set of objects giving the addresses of your VMs.

If your VMs have access to your AWS credentials, you can use the `aws` command (or equivalent) to read and write to your S3 bucket.

There will be other ways to scan for AWS resources: although I have not used the mechanism, I believe that tags can be applied to EC2 instances, and would likely be able to be queried using the `aws` command.

## Permissions if using Vagrant for deployment

:::info
:bulb: Note that it is absolutely **not required** that you use Vagrant to deploy your application to the cloud.
:::

However, if you do use Vagrant to deploy your application to the cloud remember that your provisioning script will be running as `root` whereas if you `ssh` into an EC2 instance that you've provisioned, then you will be running as `ubuntu` or whatever the default user is for the operating system AMI that you're deploying.

One useful approach is to run scripts as user `ubuntu` from your Vagrant provisioning scripting. The `su` command can be used to do this.

Looking at `Vagrantfile`s I've used in the past for research work, I see lines such as—
```
su -c "echo 'run some shell script'; ./some-shell-script.sh" ubuntu
```
—which will run run a shell as user `ubuntu` and execute the scripting that's in quotes. Note that in this case, the shell that's run as `ubuntu` goes and runs another script, namely `./some-shell-script.sh`.

## Expiry of the AWS Educate sessions

The validity of the "role" that allows you to use AWS Educate seems to be set to be around one hour, by default.

Keep this in mind when deploying your applications to the cloud, since it means that you should check that after an hour or so, your application still works. In particular, just passing your `.aws/credentials` to your cloud scripting means it will only work until your role refreshes.

It may be that you need to organise ways to access resources that leave them more open than you would do in production.