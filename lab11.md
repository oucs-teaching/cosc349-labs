## Lab 11—Work on assignment 2

This lab time has been set aside for you to work on assignment 2.

Some suggestions regarding techniques and approaches to problems that have been encountered are listed below. Feel free to augment this information.

## AWS Academy Learner Lab facilities

Note that there is documentation about the services supported (or not) by AWS Academy Learner Lab accounts (although apparenty accessing some of the database APIs don't behave as expected). https://awseducate-starter-account-services.s3.amazonaws.com/AWS_Educate_Starter_Account_Services_Supported.pdf

## Apache error handling

When Apache presents error pages, it writes additional information into its log files. For example a 403 error (access denied) can be due, for example, to the Apache user (e.g., `www-data`) not being able to read files owned by `root`.

When SSHed into your Apache VM, you can show the end of the error log using `sudo tail /var/log/apache2/error.log` (this is the location where Debian and Ubuntu Linux put their Apache log files: if you're using a different variant of Linux, you should check the its documentation for details).

As far as I can tell there is not an easy way to cause Apache to present this detailed information on the error pages themselves. In general this would be a very bad idea in terms of computer security, which is why I suspect that the facility is not provided.

## Facilitating VMs finding out about each other's addresses

:::info
:thought_balloon: 
This section discusses mechanisms for automating the setup of sets of VMs. However please remember that automated deployment is **not** a requirement of assignment two! You are free to manually `ssh` into your instances to give them the details required to get your application running in the cloud. Having said that, automation is more convenient when it works smoothly, but you don't want to get stuck on it.
:::

- It's a common problem that a set of VMs need to find out each other's addresses so that they can interlink.
- You may have one-way needs (e.g., VM2 needs to know VM1's IP addresses), or two-way needs (VM1 and VM2 both need to find out each other's addreses).
- A common situation is that you will want, say, to SSH to a resource before SSH is actually ready on that resource. A typical solution is to try to SSH, setting a reasonably short time-out, then if the `ssh` command fails, `sleep` for a short time (a few seconds) and then try again.

### Multi-phase deployment

One approach is to do your deployment in multiple phases.
- Phase 1: provision your VMs
- Phase 2: accumulate information required to connect to your machines (since they've now been provisioned), then connect to each of your VMs to update this information, and run a script to complete the start up of the services your application needs.

### Use a shared resource to coordinate configuration

You can potentially set up `.aws/credentials` to be made available on your VM, so that the provisioning script can run `aws` commands as "you".

You can set up a resource that all of your VMs can access, such as an S3 bucket that contains a set of objects giving the addresses of your VMs.

If your VMs have access to your AWS credentials, you can use the `aws` command (or equivalent) to read and write to your S3 bucket.

There will be other ways to scan for AWS resources: although I have not used the mechanism, I believe that tags can be applied to EC2 instances, and would likely be able to be queried using the `aws` command.

## Expiry of the AWS Academy Learner Lab sessions

The validity of the "role" that allows you to use AWS Academy seems to be set to be around one hour, by default.

Keep this in mind when deploying your applications to the cloud, since it means that you should check that after an hour or so, your application still works. In particular, just passing your AWS credentials to your cloud scripting means it will only work until your role refreshes. (It may be that you need to organise ways to access resources that leave them more open than you would do in production.)