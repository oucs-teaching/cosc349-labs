---
tags: cosc349
---
# COSC349 Lab 6—Cloud Architecture—2020
[Lab 3]: /bi1pAIlXT3O4WezjVtqPrA

## Lab 6—Additional Vagrant exploration

Everyone has progressed very well in terms of following the lab instructions. However, completing the first assignment requires significant information synthesis and further background reading.

This lab has been added to emphasise some information, methods and techniques that you may find useful. Please let me know what else I might usefully add. You may want to refer back to this page later to see whether additional material has been added: if you note down when you were looking at this page, when you return you can visit the "revisions" available from the page menu, to see whether content has changed.

## Multiple VMs within one `Vagrantfile`

:::info
Material for running multiple VMs within one `Vagrantfile` was referenced at then ed of [Lab 3], but we will explore it in more detail within this lab.
:::

An useful capability of Vagrant is to be able to manage multiple VMs within a single `Vagrantfile`. This type of use of Vagrant is likely to be useful for, say, COSC349 assignment work.

The repository at https://altitude.otago.ac.nz/cosc349/vagrant-multivm shows a webserver and a database server interacting. Let me know if you want further elaboration of what's covered by the code comments contained within the that repository's `Vagrantfile`.

## My method for building the multi-VM `Vagrantfile`

- The `git` commits on the repository give a lot of information about how I built up the multi-VM `Vagrantfile`. This type of `git` commit structure is what should be present in the `git` repositories that you submit for assignment 1.
- What you'll see if you look through the changes in the repository commit by commit, is that I actually first set up a `Vagrantfile` that set up a VM running the web server and the database server on the same VM. I ensured that I could get that working, committed that version, and then moved to separate out the configuration into two separate VMs. I try always to apply this sort of task decomposition and progressive work when I can: over time I've (surprisingly slowly) learnt that it really is faster for me to make small steps and test them as I go, rather than assuming to make big leaps with occasional testing.

## Network configuration and testing

### Very quick primer on IP

There are many resources on the web and COSC papers that cover IP and DNS. While you need to use IP addresses in COSC349, you should not need deep understanding about how they work. Feel free to extend the definitions below or note their flaws—I am trying to provide a minimal description that is workable, and am over-simplifying what's actually going on, in some parts.

IP addresses are used to allow one computer (or VM) to connect to another computer on a network, be it a private network or the Internet. We'll use IPv4 addresses, and refer to an IP address without noting we're talking about a version 4 address. (Ideally everyone on the Internet would have moved to using IPv6 addresses by now, but this certainly hasn't happened: e.g., UoOtago's campus network does not (directly) support IPv6.)

IP addresses are 32-bit numbers, although for additional readability they're shown as four 8-bit numbers ("octets") separated by dots. For example, Google NZ for me appears at 216.58.199.67.

IP addresses are not all globally usable and reachable though. IP addresses are split at some bit position such that the high-order bits give the network number, and the low-order bits give the host number on that particular network. 

All hosts with the same network number can reach each other directly, as it's a local area network. To reach hosts on other network numbers, traffic is set to one particular address on the same network number: that of the gateway or router, which is responsible for interconnecting different network numbers together.

Network addresses are typically written as a full IP address followed by a /n where 'n' indicates the number of network number bits versus host number bits. By convention all the host bits are set to 0 when describing the network's address as an IP address.

Let's work through an example. Say we have a network 192.168.2.0/24. The slash 24 indicates that the top 24-bits of this IP address are the network number, and the remaining 8-bits are the host number. Thus on this network we have space for 256 host numbers. A given VM might be at 192.168.2.11, for example. There are at least three addresses of the 256 that you shouldn't use, though:
- the lowest host number, 0, is usually used to identify the network, so allocating a host there would confuse matters, i.e., don't allocate a VM to 192.168.2.0 but instead start your numbering higher---I'd typically start at 192.168.2.2;
- the highest host number, 255, is usually used for the broadcast address, that transmits to all hosts on that local network, i.e., don't allocate a VM to 192.168.2.255, instead 192.168.2.254 should be the highest IP address that you use;
- for non-private networks there is one address used as a gateway or a router: it is the address to which all traffic not within the current network is sent, and VirtualBox often allocates the second-lowest host number, 1, I think, so don't allocate a VM to 192.168.2.1.

Picking a /24 network simplifies illustrations since the last "octet" of the IP address is the host number. It is entirely possible to have most other /n values, which involves some more binary tinkering. For example, we could instead consider 192.168.2.0/25. Now there are only 128 host numbers, from 192.168.2.0 to 192.168.2.127, since the high-order bit of the last octet belongs to the network number. The address 192.168.2.200 is still valid, but is a host on the nearby network 192.168.2.128/25.

Finally, there are some ranges of IP addresses that never appear on the Internet directly, and are frequently reused for internal networks. These are:
- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

You should avoid using the IP multicast ranges from 224.0.0.0 to 239.255.255.255 as these addresses may be interpreted specially.

Another special address is the `localhost` address 127.0.0.1, which always refers to yourself. Running a server on the `localhost` IP address means that server can only be connected to by processes running on that computer itself, and not by other computers on the LAN or the Internet. Note that a VM connecting to a server running on its host VMM would usually _not_ be considered as "yourself" from the perspective of the host VMM. (Weirdly `localhost` is actually a gigantic network 127.0.0.0/8 giving more than 16 million ways to refer to yourself, but it's only widely safe to use the one particular address, 127.0.0.1. This type of design oddness is partly why we've run out of new IPv4 addresses: the address space is highly fragmented.)

Finally, when starting a server, you usually need to specify which IP address to use, but there is a convention of using 0.0.0.0 to mean that all network interfaces should be used: i.e., that the server should accept connections from any IP address, local or not.

Despite having a non-Internet routable IP address, a host (e.g., a VM) with such a non-Internet routable address can still interact with the Internet using network address translation (NAT), in which a gateway rewrites a host's requests to appear to come from itself. The gateway needs to have two IP addresses, one on the private network, and one on the Internet. Domestic Wi-Fi routers typically function as this sort of gateway. You can see the COSC301 lab book if you want more information.

### Very quick primer on TCP and UDP ports

IP addresses allow computers on the Internet to reach each other, however specifying the purpose of communication involves a higher layer of the networking stack. The IP layer just works in terms of trying to deliver packets of data from one computer to another. TCP and UDP are layered on top of IP in order to build more complete protocols useful to applications. In particular TCP incorporates acknowledgements of data packets, retransmissions of packets and acknowledgements if losses occur, and congestion control, to ensure that the network paths are not overloaded.

An IP address refers to the specific network interface of a specific computer as a whole, but TCP addresses a _service_ running on that computer. TCP introduces port numbers, so that a given IP address may operate multiple independent network services. Port numbers less than 1024 are privileged, i.e., normal user processes cannot open up services on those ports. 

Most operating systems allow you to refer to ports either by number, or by using symbolic names. For example the port for web traffic is port 80, or "http". Secure web traffic uses port 443, or "https". The Secure Shell runs on port 22, or "ssh".

### Very quick primer on DNS

Basically, the domain name service (DNS) provides a way to give computers human-readable names that can be discovered from a distributed database built from intercommunicating DNS servers.

For many of the examples we've seen in COSC349 labs, we have used IP addresses instead of DNS names for VMs. This is almost always acceptable.

The name `localhost` maps to IP address 127.0.0.1. A name like `google.co.nz` can be looked up on the Unix / Windows command line using commands such as `nslookup`, `host` and `dig`. For example:

```
$ host google.co.nz
google.co.nz has address 142.250.66.195
google.co.nz has IPv6 address 2404:6800:4006:80f::2003
google.co.nz mail is handled by 10 aspmx.l.google.com.
google.co.nz mail is handled by 20 alt1.aspmx.l.google.com.
google.co.nz mail is handled by 40 alt3.aspmx.l.google.com.
google.co.nz mail is handled by 30 alt2.aspmx.l.google.com.
google.co.nz mail is handled by 50 alt4.aspmx.l.google.com.
```

### Testing network connectivity

Particularly when using port forwarding, it can be useful to have ways of testing network connectivity.

Let's work through some connectivity testing using the multi-VM Vagrantfile discussed above.

After running `vagrant up`, check that there are no unexpected error messages, and then let's proceed to test that each of the servers is working as expected.

#### Testing MySQL

Let's connect to the database server: `vagrant ssh dbserver`. Note that if this command works, you have already tested that port forwarding to your VM's SSH server is working, since this is how Vagrant does its SSH connection. (You can request to see the OpenSSH configuration that Vagrant uses by running the `vagrant ssh-config` command instead of `vagrant ssh`.)

:::info
Note that the testing of MySQL here is unlikely to generalise usefully to other database engines: you will need to find equivalent commands in other databases' documentation or support communities.
:::

First let's check that the MySQL server is running:

```
vagrant@dbserver:~$ sudo lsof -ni :mysql
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3966 mysql   29u  IPv4  23688      0t0  TCP *:mysql (LISTEN)
```

The command `lsof` normally lists open files, but has the `-i` option to look for open Internet ports instead. (The `-n` option just speeds things up a bit by requesting `lsof` not to look up the DNS names of IP addresses.) We've asked it to search for any address open on the MySQL port, which is numerically 3306 (So `sudo lsof -i :3306` should produce the same output, and the `-P` option would lead `lsof` to tell you the port number in the output instead of the named port number).

So at this point it seems that MySQL is running, and that it's open to receive traffic from the Internet (the `*` within `*:mysql`).

If this step failed, then try starting the MySQL database up again: you may need to look at the MySQL documentation to find out more about how to see more detailed diagnostic messages.

To connect to your database, you need to know an appropriate username and password. This information is contained within the `Vagrantfile`, both for the MySQL `root` user, and for MySQL user `webuser`. I typically configure MySQL passwords using `.my.cnf` files in my home directory (search online how to achieve this), however in this case we can use the fact that MySQL checks the shell (environment) variable `MYSQL_PWD`. If that shell variable is set (you can check if the `MYSQL_PWD` variable is set by running the shell command `echo $MYSQL_PWD`), this is equivalent to you asking MySQL to prompt you for a password, and then typing that password.

I copied and pasted the `export MYSQL_PWD`... line from the `Vagrantfile` for the root user (note that there are two instances of the `export MYSQL_PWD` command within that `Vagrantfile`, so you need to use the correct one), and then I can run a session such as:

```
vagrant@dbserver:~$ mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.7.27-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| fvision            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> USE fvision;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW tables;
+-------------------+
| Tables_in_fvision |
+-------------------+
| papers            |
+-------------------+
1 row in set (0.00 sec)

mysql> DESCRIBE papers;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| code  | varchar(7)  | NO   | PRI | NULL    |       |
| name  | varchar(50) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

mysql> SELECT * FROM papers;
+---------+------------------------------+
| code    | name                         |
+---------+------------------------------+
| COSC326 | Effective Programming        |
| COSC349 | Cloud Computing Architecture |
+---------+------------------------------+
2 rows in set (0.00 sec)

mysql> 
```

Another important check we can perform is to determine the IP address of the database server. While this has been configured in the `Vagrantfile`, it's still worth checking that everything was set up as expected.

There are many commands you can use to determine the IP address(es) of a server. I have used the `ip` command, which is fairly widely installed.

```
vagrant@dbserver:~$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 02:bd:e0:5c:30:55 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::bd:e0ff:fe5c:3055/64 scope link 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:1d:9f:f7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.12/24 brd 192.168.2.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe1d:9ff7/64 scope link 
       valid_lft forever preferred_lft forever
```

So this VM has three IP network interfaces. The first is for localhost access. The second is the network that Vagrant added so that it can SSH to the server for us. The third is the internal network explicitly configured by us in the `Vagrantfile`. The dbserver has address `192.168.2.12`.

We can test whether we can reach ourselves by using the `ping` command, such as in the following. The `-c3` switch just tells `ping` to try three messages before exiting, since otherwise the `ping` command will run until you press <kbd>control</kbd><kbd>c</kbd>.

```
vagrant@dbserver:~$ ping -c3 192.168.2.12
PING 192.168.2.12 (192.168.2.12) 56(84) bytes of data.
64 bytes from 192.168.2.12: icmp_seq=1 ttl=64 time=0.023 ms
64 bytes from 192.168.2.12: icmp_seq=2 ttl=64 time=0.038 ms
64 bytes from 192.168.2.12: icmp_seq=3 ttl=64 time=0.037 ms

--- 192.168.2.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1998ms
rtt min/avg/max/mdev = 0.023/0.032/0.038/0.009 ms
```

#### Testing VM to VM communication

Close your SSH connection to the dbserver if you had not already done so, and instead connect to the webserver: `vagrant ssh webserver`.

Use `ping` to test the webserver's IP-level connectivity to itself, as in the following command invocation. You can use the `ip` command described previously to determine the IP address of the webserver. Here, though, I remember the pattern of addresses I used when manually assigning IP addresses to the VMs in the `Vagrantfile`, and thus remember that 192.168.2.11 was the first of the two VMs (i.e., the web server VM).

```
vagrant@webserver:~$ ping -c3 192.168.2.11
PING 192.168.2.11 (192.168.2.11) 56(84) bytes of data.
64 bytes from 192.168.2.11: icmp_seq=1 ttl=64 time=0.024 ms
64 bytes from 192.168.2.11: icmp_seq=2 ttl=64 time=0.037 ms
64 bytes from 192.168.2.11: icmp_seq=3 ttl=64 time=0.038 ms

--- 192.168.2.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 0.024/0.033/0.038/0.006 ms
```

Now you can test if you can `ping` the database server from the webserver. In the case below, this has been successful for me.

```
vagrant@webserver:~$ ping -c3 192.168.2.12
PING 192.168.2.12 (192.168.2.12) 56(84) bytes of data.
64 bytes from 192.168.2.12: icmp_seq=1 ttl=64 time=0.489 ms
64 bytes from 192.168.2.12: icmp_seq=2 ttl=64 time=0.482 ms
64 bytes from 192.168.2.12: icmp_seq=3 ttl=64 time=0.397 ms

--- 192.168.2.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2008ms
rtt min/avg/max/mdev = 0.397/0.456/0.489/0.041 ms
```

#### Testing a web server for connectivity

We'll continue working with the multi-VM `Vagrantfile` used in the preceding sections.

If you are not already SSHed into your webserver VM, the n do so now (`vagrant ssh webserver`).

First let's test whether the web server appears to be listening on TCP port 80 (or whatever you have chosen to use instead).

```
vagrant@webserver:~$ sudo lsof -ni
COMMAND    PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
dhclient   877     root    6u  IPv4  11675      0t0  UDP *:bootpc 
sshd      1307     root    3u  IPv4  18454      0t0  TCP *:ssh (LISTEN)
sshd      1307     root    4u  IPv6  18456      0t0  TCP *:ssh (LISTEN)
apache2   9325     root    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
apache2  10159 www-data    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
apache2  10160 www-data    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
apache2  10161 www-data    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
apache2  10162 www-data    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
apache2  10163 www-data    4u  IPv6  26513      0t0  TCP *:http (LISTEN)
sshd     10290     root    3u  IPv4  29922      0t0  TCP 10.0.2.15:ssh->10.0.2.2:53499 (ESTABLISHED)
sshd     10329  vagrant    3u  IPv4  29922      0t0  TCP 10.0.2.15:ssh->10.0.2.2:53499 (ESTABLISHED)
```

This output indicates that the `apache2` process (i.e., the webserver that was installed), is indeed listening for connections on the `http` port, as expected.

You can check whether the webserver is handling requests by manually submitting one. This can be done using `wget http://localhost` or `curl http://localhost` if those commands are installed on your VM. Otherwise, you can create a simple HTTP request directly on the command line, as in the following example, if the netcat command `nc` is installed which is usually true these days. Netcat is a utility that supports performing simple network diagnostics. In this case we are using `nc`'s ability to send standard input (the output from the `printf` command) to a network service, and show the network data that gets sent back (if any).

```
vagrant@webserver:~$ printf "GET / HTTP/1.1\r\nHOST: localhost\r\n\r\n" | nc localhost 80
HTTP/1.1 200 OK
Date: Tue, 13 Aug 2019 11:04:17 GMT
Server: Apache/2.4.18 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 538
Content-Type: text/html; charset=UTF-8

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML//EN">
<html>
<head><title>Database test page</title>
<style>
th { text-align: left; }

table, th, td {
  border: 2px solid grey;
  border-collapse: collapse;
}

th, td {
  padding: 0.2em;
}
</style>
</head>

<body>
<h1>Database test page</h1>

<p>Showing contents of papers table:</p>

<table border="1">
<tr><th>Paper code</th><th>Paper name</th></tr>

<tr><td>COSC326</td><td>Effective Programming</td></tr>
<tr><td>COSC349</td><td>Cloud Computing Architecture</td></tr>
</table>
</body>
</html>
```

(Actually I cleaned up this output slightly, as there isn't a newline after the final `</html>` so your shell prompt will appear directly after it.)

If this all works, you can log out from your webserver, and connect back to the database server `vagrant ssh dbserver`, and on that VM repeat the above request for the web page from the webserver, updating the address to be the address of the webserver (and not `localhost`). In my case I have used a different HTTP method—`HEAD`—that just returns the HTTP headers rather than the actual web page content too.

```
vagrant@dbserver:~$ printf "HEAD / HTTP/1.1\r\nHOST: dbserver\r\n\r\n" | nc 192.168.2.11 80
HTTP/1.1 200 OK
Date: Tue, 13 Aug 2019 11:14:27 GMT
Server: Apache/2.4.18 (Ubuntu)
Content-Type: text/html; charset=UTF-8

vagrant@dbserver:~$ 
```

Getting this far has established that you have a functioning internal network that facilitates interactions between the two VMs.

#### Testing port forwarding

Now let's see whether the port forwarding appears to be working correctly on your host, so that your host computer can connect to the webserver running on your VM.

Looking at the `Vagrantfile`, we have the line:

```
    webserver.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
```

So we should expect to be able to connect to localhost port 8080 on our host computer, and have this connection be forwarded to our webserver VM's port 80.

On macOS and Linux the `lsof` command is likely to be available. On Windows I think that `netstat` can list listening ports, but I do not have a recent Windows computer nearby to test. My interaction showed:

```
lsof -Pni :8080
COMMAND     PID  USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
VBoxHeadl 29927 dme26   16u  IPv4 0x675e0d038680649b      0t0  TCP 127.0.0.1:8080 (LISTEN)
```

This is as expected: we see (right-most columns) that there is a process listening on 127.0.0.1:8080. The process is (left-most column) named `VBoxHeadless`, which is a process running a VirtualBox VM that does not have an associated console window being shown.

#### Vagrant machines appear within the VirtualBox GUI

Our use of Vagrant so far just involves Vagrant remote-controlling VirtualBox. As a consequence the actions that Vagrant takes are typically reflected within he VirtualBox GUI.

With a multi-VM configuration up and running, open the VirtualBox GUI and note the VMs that Vagrant has started. Use the VirtualBox GUI to investigate configuration details of your running VMs, such as their port forwarding configuration.

Note that you can use the VirtualBox GUI to open a console window for a Vagrant VM, it's just not expected that this will be the most efficient way to work, compared to Vagrant setting up SSH connections for you to use.

## Building and testing shell provisioning

As mentioned in [Lab 3], when provisioning VMs, I recommend trying to take lots of small steps, repeatedly testing the complete VM building process (i.e. `vagrant destroy`, `vagrant up`).

In the multi-VM `Vagrantfile` we have been discussing in this lab, we have used internal provisioning scripts. You may find it more convenient to work with stand-alone scripts that can be edited independently of the `Vagrantfile`, and called upon from the shell purposes.

Try to factor out two scripts, `build-webserver-vm.sh` and `build-dbserver-vm.sh` from the multi-VM example `Vagrantfile` that we've been working with. You may want to examine the relevant [Vagrant documentation](https://www.vagrantup.com/docs/provisioning/shell.html). You will need to change the `inline` attribute to a `path` attribute that specifies your provisioning script as a relative path from the `Vagrantfile`.

Note that if you are making changes to a `Vagrantfile`, it may be advisable to `vagrant destroy` all the VMs before you make major changes to the `Vagrantfile`.

If you are running on Windows, it may be useful to add your new scripts' names into the .gitattributes file, to tell `git` to use Unix line endings. Looking inside the existing .gitattributes files, you can see the two lines that specify Unix line endings (LF) for the scripts `setup-database.sql` and `test-website.conf`.

## Pitfalls

- The `2` in the top of the `Vagrantfile` in the line `Vagrant.configure("2") do |config|` is not the number of VMs, it's the version number of the `Vagrantfile` format itself, so you shouldn't change it.
- One potential source of confusion is the paths `/home/vagrant` versus `/vagrant`. When you login to your VM, e.g., using the `vagrant ssh` command, you will be in the home directory of the `vagrant` user, namely `/home/vagrant`. However if you want to access the shared folder between the VM and the host, this is at the different folder, `/vagrant`.
- During provisioning your scripting within your `Vagrantfile` runs as `root`, but `vagrant ssh` takes you to a shell for the `vagrant` user. Thus to run privileged commands as they worked within the Vagrantfile, if you've logged in as the `vagrant` user, you will need to use `sudo` before the privileged command (`sudo` stands for "super-user do", i.e., run the command that follows as the (almost) all powerful superuser).
- Remember that `/vagrant` is, essentially, a network drive from the perspective of your VM. Exactly how access control and file permissions operate can depend on the host operating system. For example, a Windows host is unlikely to preserve all of the file permissions you might make within the VM using a command such as `chmod`. Also, we saw in the CS Labs what appears to be a difficulty in managing permissions from inside the VM on paths under `/vagrant` when the host's directory (the one containing the `Vagrantfile`) is itself on a network drive (e.g., your CS home directory).
    - Remember that you do not necessarily have to access files at runtime from under `/vagrant`—you can instead have your provisioning script copy content from under `/vagrant` to some other path (e.g., under `/home/vagrant`), where your files will be sitting on a Linux filesystem where `chmod` will work as expected, rather than the network share that VirtualBox is creating to effect paths under `/vagrant`.