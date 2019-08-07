---
tags: cosc349
---
# COSC349 Lab 5—Cloud Architecture—2019
## Lab 5—An introduction to Docker

The computers in Lab E and Lab F can be (re)booted into macOS or Linux. This lab requires you to boot into macOS (which I note is considered undesirable by at least one logged in user who changed the lab text!), since the CS Lab Linux environment has not been set up to directly support Docker.

:::info
The next lab will explore Vagrant further, and is now linked from the labs page. You may want to look at it now given that it is likely to have relevance to your assignment 1 work.
:::

[Lab 3]: /bi1pAIlXT3O4WezjVtqPrA

### Lab objectives

1. Understand what Docker aims to achieve.
2. Learn the main command line invocations for managing Docker.
3. Examine the Docker Hub, and explore some of the virtual appliances on offer.

## Docker lab materials

David initiated an introduction to Docker in the style of a Software Carpentry lesson (recall the `git` lab included material from a Software Carpentry lesson).

### Working with Docker in the CS Labs

The lesson is intended to be used on learners' computers rather than a managed environment such as the CS labs, where you don't have administrator privileges. (You are welcome to work through the lesson on your own computer, too, of course.)

To use Docker in the CS labs, we apply an extra level of virtualisation: using Vagrant to run Ubuntu, which in turn can run Docker containers.

You can clone a `git` repository containing a `Vagrantfile` that creates Ubuntu boxes ready to run the Docker command-line tools. This is similar to a step you performed in lab 3. After changing to the directory in which you want to store your local clone of the `git` repository, you can run a command sequence such as—
```
git clone https://altitude.otago.ac.nz/cosc349/lab05-docker cosc349-lab05-docker
cd cosc349-lab05-docker
vagrant ssh
```

Because Vagrant sets up a share between your working directory and `/vagrant` on the VM, it is recommended that you change into the `/vagrant` directory before running Docker commands that could usefully read and write files that your host OS can access.

:::warning
One change that needs to be made in the Docker lesson is that `docker` commands with a form such as `-p 127.0.0.1:4000:4000` will need to be changed to `-p 0.0.0.0:4000:4000`. The `-p` flag is an option to `docker` to set up network port forwarding.

Using `127.0.0.1` (or `localhost`) means limiting access to the computer that's running Docker. So if you install Docker Desktop under macOS on your laptop, the browser that you use to connect to port 4000 is also running in the same macOS on your laptop. 

In our case, the web browser you're viewing things from is _not_ on the same computer as the computer running Docker: Docker is running on a Vagrant VM. The `0.0.0.0` tells Docker to allow connections from anywhere, and not just the local computer.
:::

Note that network port forwarding needs to be configured explicitly. For example, at the end of the Docker lessons referred to below, there is an exercise that opens a web browser on localhost port 4000. This will not be visible unless you add a port-forwarding line to the `Vagrantfile`. Since 4000 is not a privileged port (privileged ports are those less than 1024), you can simply port forward 4000 to 4000. This has not been done for you, but you can put off this configuration until you reach the last episode of the Docker introduction lesson. Refer back to [Lab 3] if you need example `Vagrantfile` lines to work from, or even better refer to the Vagrant documentation.

To reboot your Vagrant VM and adopt changes you've made in your `Vagrantfile`, such as port forwarding, you can run the `vagrant reload` command from your host. Note that this will destroy any Docker containers that were running within the Vagrant VM, but it won't remove their disk images.

After setting up port forwarding, let's say localhost 4000 to localhost 4000, a web browser running on your host connecting to http://localhost:4000 will be forwarded through to connect to http://localhost:4000 on your Ubuntu machine (which in turn Docker may forward to a particular Docker container).

### Over to the actual learning material...

Rather than copy the material from that lesson here, please work through his existing [Introduction to Docker](https://dme26.github.io/docker-introduction/) lesson web pages (https://dme26.github.io/docker-introduction/). Comments and pull-requests are most welcome on that material. It has been used for teaching at other institutions, but still is at an early stage of development. Because you all have experience with the Unix shell, and a CS background, it is likely that you will progress through the material more quickly than the suggested time guide.