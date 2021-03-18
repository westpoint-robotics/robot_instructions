# time_synchronization
## Synchronization of Computer Clocks to a Master Clock using NTP


### Assumptions
This document assumes you are working with two or more computers running Ubuntu 18.04 with ROS melodic installed.  You should also have a monitor, keyboard, and mouse for each computer, and should work with two computers at a time.  One computer will act as your NTP server, the other as the NTP client.  If you are working with computers without their own monitors, keyboards, and mice, a KVM switch, or similar is recommended.

### Overview
**Disclaimer: [this article from ubuntugeek](http://www.ubuntugeek.com/network-time-protocol-ntp-server-and-clients-setup-in-ubuntu.html) was used in the creation of this document, and language has been taken from that article and used below in conjunction with some clarification and elaboration.**
If you know you will be using a system involving two or more computers that need to share data through ROS messaging, and those computers are interfacing through an ethernet connection (such as ethernet to USB) that does not support PTP, you will need to use NTP.  The Network Time Protocol is a protocol for synchronizing the clocks of computer systems over packet-switched, variable-latency data networks. NTP uses UDP port 123 as its transport layer. It is designed particularly to resist the effects of variable latency (Jitter).

Using NTP is a great way to keep your system clock set correctly. It works by contacting a number of servers around the world, asking them for the time and then calculating what the correct local time is from their responses.  In your setup, you will need to configure an NTP server and NTP client.  You may need multiple clients, but should set up and test each before moving on to the next.  The simplest case assumes you are using a mobile base, such as a UGV with an onboard computer, and a companion computer.  Use the companion computer as your NTP server, and your mobile base computer, and any auxiliary computers as NTP clients.  According to [NTP FAQ](http://www.ntp.org/ntpfaq/NTP-s-algo.htm), "A time difference of less than 128ms between server and client is required to maintain NTP synchronization. The typical accuracy on the Internet ranges from about 5ms to 100ms, possibly varying with network delays. A recent survey[2] suggests that 90% of the NTP servers have network delays below 100ms, and about 99% are synchronized within one second to the synchronization peer."  The setup described below resulted in an offset of 2.6ms over 48 hours after the setup was completed.  Offsets obtained within the first hour of setup were all under 5ms. 

### 1. Installing NTP Packages
If you have installed the **ntpdate** package before you need to uninstall using the following cli

`sudo apt remove ntpdate`

Install NTP packages with the following cli:

```
sudo apt install ntp
sudo apt install ntpdate
```

### 2. Configure NTP Server
This step involves editing the NTP configuration file on the computer you wish to set up as an NTP server.  The configuration file for ntpd is located at /etc/ntp.conf.  The default Ubuntu file probably requires some modification for optimal performance.  To edit this file, use

`sudo nano /etc/ntp.conf` or use gedit, atom, etc instead of nano as you prefer.

The IP addresses you use may differ from the below example, but the following edits will get your server working.  You may consult the sample ntp.conf file in this directory if you want to see a completed example.  In the sample, there are comments marked by, "RRC edits" that show where the relevant edits are made.

You need to add a number of servers to the server list. The Debian default is [pool.ntp.org](pool.ntp.org) which works but isn’t always amazingly accurate because it makes no attempt to use time servers near you. If you want more accuracy use the time servers either on your continent (for instance [europe.pool.ntp.org](europe.pool.ntp.org)) or your country (for instance [uk.pool.ntp.org](uk.pool.ntp.org)) one of your local country servers.  Consult the [Access Restrictions guide](support.ntp.org/bin/view/Support/AccessRestrictions) for further suggestions.  The optimal number of servers to listen to is three but two will also give a good accuracy.  If your ISP runs a time server for you it is worth including it in your server list as it will often be more accurate than the pooled servers and will help keep the load down on the pool.

I am using the following two servers for my configuration

```
server ntp0.pipex.net
server ntp1.pipex.net
```

Restrict the type of access you allow these servers. In this example the servers are not allowed to modify the run-time configuration or query your Linux NTP server.

```
restrict otherntp.server.org mask 255.255.255.255 nomodify notrap noquery
restrict ntp.research.gov mask 255.255.255.255 nomodify notrap noquery
```

The mask 255.255.255.255 statement is really a subnet mask limiting access to the single IP address of the remote NTP servers.

If this server is also going to provide time for other computers, such as PCs, other Linux servers and networking devices, then you’ll have to define the networks from which this server will accept NTP synchronization requests. You do so with a modified restrict statement removing the noquery keyword to allow the network to query your NTP server. The syntax is:

`restrict 192.168.131.1 mask 255.255.255.0 nomodify notrap`

In this case the mask statement has been expanded to include all 255 possible IP addresses on the local network.  The IP address in this example is the address for the Jackal onboard computer; we are telling the server (companion computer) to accept requests from this client, which will be set up below.

We also want to make sure that localhost (the universal IP address used to refer to a Linux server itself) has full access without any restricting keywords

`restrict 127.0.0.1`

Save the file and exit.

Now you need to restart NTP server for these settings to take effect using cli:

`sudo /etc/init.d/ntp restart`

You will enter your computer's password, and then see:

`[ ok ] Restarting ntp (via systemctl): ntp.service.`


### 3. Configure NTP Client
If you want to configure a computer to be an NTP client, install the following packages

```
sudo apt install ntp
sudo apt install ntpdate
```

Edit the /etc/ntp.conf file using cli:

`sudo nano /etc/ntp.conf`, or use gedit, atom, etc instead of nano as you prefer.

Under the section in the configuration document labeled, "Specify one or more NTP servers", there are a few lines specifying servers from NTP pools, and an additional line used as a fallback that reads, "pool ntp.ubuntu.com".  After this, specify the address of the server, like so

`server 192.168.131.50`

The above was used as the static IP Address on a companion computer for a Clearpath Jackal; the address you use should reflect the IP of the computer you set up as an NTP server in section 2.  Next, restrict the type of access you allow these servers. In this example the servers are not allowed to modify the run-time configuration or query your Linux NTP server.  The two lines below come in the section of the configuration document immediately following the previous edit.

```
restrict default notrust nomodify nopeer
restrict 192.168.131.50
```

We also want to make sure that localhost (the universal IP address used to refer to a Linux server itself) has full access without any restricting keywords

`restrict 127.0.0.1`

Save the file and exit.

Now you need to restart NTP server for these settings to take effect using cli:

`sudo /etc/init.d/ntp restart`

You will enter your computer's password, and then see:

`[ ok ] Restarting ntp (via systemctl): ntp.service.`

### 4. Validate Server/Client Setup
On client (e.g. Jackal)
1. `sudo timedatectl set-ntp 0`
2. `sudo timedatectl set-time hh:mm:ss`
3. `sudo timedatectl set-ntp 1`
4. `sudo ntpdate 192.168.1.50`

On client and server
5. `sudo /etc/init.d/ntp restart`

Between steps 1-3, use 

`timedatectl status`

to verify that each step is working.  You can perform this varification on both the server and client computers; this
is easiest if you have a KVM switch and can just toggle among the two or more computers you are synchronizing.

On the server, make sure offsets are minimal by running the below.  You may have to wait a few minutes (~3-5) to see proper results.

`ntpdate -q administrator`

where, "administrator" is the username on the client computer and could be something else, like "user1".  You should eventually see
something of the form:

```
server 192.168.131.1, stratum 2 offset -0.031994, delay 0.02609
11 Mar 16 16:35:22 ntpdate[5661]: adjust time server 192.168.131.1 offset -0.031994 sec
```

Leave everything running for several minutes, and run the query command again.  The closer the offset number is to the original
result, the better.  Repeat this step as many times as seems prudent.  The longer the offset stays stable, the more confident
you can be that the ntp server and client are set up correctly.

Once this is working for one server and one client, you can edit the /etc/ntp.conf file on the server to include more clients.  Just
add lines of the form: `restrict 192.168.131.1 mask 255.255.255.0 nomodify notrap`.  Each client will have its /etc/ntp.conf file
set up as the one you just got working.  The server address will remain the same (assuming you are augmenting the same network),
and the client's IP is the only detail that will change in each case.  Otherwise the procedures will be the same.  If you are adding
a sensor to the network, and that sensor (e.g. LiDAR) is connected driectly to the server computer's PCI port, you will use PTP, rather
than NTP.  Section 7 of the [usma_ouster](https://github.com/westpoint-robotics/usma_LiDAR/tree/master/usma_ouster) documentation indicates
where to find the instructions for this for the Ouster.  For other devices, check user manuals, including appedicies to determine whether
you may need to synchronize a clock on the device with the rest of your network.


