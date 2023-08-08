## Lab 5—An introduction to Docker

Docker Desktop should be able to be installed on recent versions of macOS (including Apple computers with Arm CPUs!), Windows and Linux. However, the assumption in the instructions below is that you will be trying to work through the lab on a Windows computer in the Owheo CS Labs.

:::info
:eyes: 
The next lab will explore Vagrant further, and will soon be linked from the labs page. You may want to look at it now given that it is one technology that you can use to complete your assignment 1 work.
:::

[Lab 3]: /bi1pAIlXT3O4WezjVtqPrA

### Lab objectives

1. Understand what Docker aims to achieve.
2. Learn the main command line invocations for managing Docker.
3. Examine the Docker Hub, and explore some of the virtual appliances on offer.

## Docker lab materials

I (David) initiated an introduction to Docker in the style of a Software Carpentry lesson (recall the Git lab included material from a Software Carpentry lesson).

You are very welcome to follow the instructions in those materials to install Docker Desktop (most likely) on your own computers. 

See below for suggetsions on how to get a Docker environment within the CS Labs.

Rather than copy the material over from the Software Carpentry lesson here, please work through this snapshot of the  [Introduction to Docker](https://dme-forks.github.io/2021-07-07-docker-introduction/) material that I used for a workshop I ran in July 2021. It would be good for you to complete the core lesson material, you are of course welcome to complete the extension exercises if you want to.

For your interest, ongoing development of the Docker lesson is continuing collaboratively on the [Carpentries Incubator](https://carpentries-incubator.github.io/docker-introduction/), after I contributed the initial verison of the material.

Comments and pull-requests are most welcome on that material. It has been used for teaching at many other institutions, but still is at a reasonably early stage of development. Because you all (now) have experience with the Unix shell, and a CS background, it is likely that you will progress through the material more quickly than the suggested time guide.

## Working with Docker in the CS Labs

The Software Carpentry lesson is intended to be used on learners' computers rather than a managed environment such as the CS labs, where you don't have administrator privileges. (You are welcome to work through the lesson on your own computer, too, of course.)

There are two routes to running Docker containers, and hopefully you only need to look at the first one, namely using Docker Desktop.

### Using the Docker Desktop application

Docker Desktop is installed on the CS Labs.

:::warning
:fire: 
Getting Docker Desktop to start up for the first time in the CS Labs involves ignoring some error messages... but here's what worked for me:
- Run the Docker Desktop application
- Agree the terms and conditions presented
- Acknowledge the need to update the Windows Subsystem for Linux (WSL)
- Quit the Docker Desktop application
- Run a command shell
- Execute the command `wsl --update` in the shell
- Cancel the UAP alert that comes up (does this mean we'll get broken WSL networking I wonder?) since our student / staff accounts are not administrator accounts
- Run Docker Desktop again, and wait for a few minutes for it to start (not sure why it's quite so slow)
:::

In your systray (bottom right) a whale icon should eventuall appear—when the square containers on top of the whale within the icon stop animating, then the underlying Linux VM has started up.

After that point, `docker` commands in the terminal will work, and you can switch from these instructions over to the Software Carpentry lesson.

Once you have completed the core parts of the Docker lesson, you may wish to click on the systray whale-shaped icon to open up the Docker Desktop dashboard. Explore the GUI to discover what operations and diagnostics you can acquire regarding your Docker containers.

When you have finished with the lab exercise you should delete your containers, e.g., from the Docker Desktop dashboard window. Ideally you should also delete the Docker image files that were downloaded in the course of the lab exercise. In a terminal window `docker image ls` will list the container images on your computer, including displaying numerical IDs for each. The `docker image rm` command followed by a space-separated list of container image IDs will remove all of those container images from your computer.

### Hosting Docker containers within a Linux VM

If you are unable to get Docker Desktop working, it is possible to run Docker containers first applying an extra level of virtualisation: using Vagrant to run Ubuntu, which in turn can run Docker containers (since the Ubuntu VM provides a Linux kernel on which to run containers).

You can clone a Git repository containing a `Vagrantfile` that creates Ubuntu boxes ready to run the Docker command-line tools. This is similar to a step you performed in lab 3. After changing to the directory in which you want to store your local clone of the Git repository, you can run a command sequence such as—
```
git clone https://altitude.otago.ac.nz/cosc349/lab05-docker cosc349-lab05-docker
cd cosc349-lab05-docker
vagrant up
vagrant ssh
```

:::info
:eyes: 
Because Vagrant sets up a share between your working directory and `/vagrant` on the VM, it is recommended that you change into the `/vagrant` directory before running Docker commands that could usefully read and write files that your host OS can access. 

Also remember that the default home directory of the `vagrant` user is `/home/vagrant`, which is not the same place as `/vagrant`.
:::

:::warning
:warning: 
One change that needs to be made in the Docker lesson is that `docker` commands with a form such as `-p 127.0.0.1:4000:4000` will need to be changed to `-p 0.0.0.0:4000:4000`. The `-p` flag is an option to `docker` to set up network port forwarding.

Using `127.0.0.1` (or `localhost`) means limiting access to the computer that's running Docker. So if you install Docker Desktop under macOS on your laptop, the browser that you use to connect to port 4000 is also running in the same macOS on your laptop. 

In our case, though, the web browser you're viewing things from is _not_ on the same computer as the computer running Docker: Docker is running on a Vagrant VM. The `0.0.0.0` tells Docker to allow connections from anywhere, and not just the local computer.
:::

Note that network port forwarding needs to be configured explicitly. For example, at the end of the Docker lessons referred to below, there is an exercise that opens a web browser on localhost port 4000. This will not be visible unless you add a port-forwarding line to the `Vagrantfile`. Since 4000 is not a privileged port (privileged ports are those less than 1024), you can simply port forward 4000 to 4000. This has not been done for you, but you can put off this configuration until you reach the last episode of the Docker introduction lesson. Refer back to [Lab 3] if you need example `Vagrantfile` lines to work from, or even better refer to the Vagrant documentation.

To reboot your Vagrant VM and adopt changes you've made in your `Vagrantfile`, such as port forwarding, you can run the `vagrant reload` command from your host. Note that this will destroy any Docker containers that were running within the Vagrant VM, but it won't remove their disk images.

After setting up port forwarding, let's say `localhost` port 4000 to `localhost` port 4000, a web browser running on your host connecting to http://localhost:4000 will be forwarded through to connect to http://localhost:4000 on your Ubuntu machine (which in turn Docker may forward to a particular Docker container).

When you have completed the lab exercise, you should `vagrant destroy` the VM that you have used for hosting Docker containers: this will remove the VM itself, as well as the Docker images that were downloaded in the course of the lab exercises.

