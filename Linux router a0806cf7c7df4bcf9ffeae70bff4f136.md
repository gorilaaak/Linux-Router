# Linux router

Internet - vast interconnected network which affects our everyday life. But how does it actually work? Routing is a key principle of Internet - an mechanism which delivers the packets across the globe even to most remote locations out there from our cozy household. 

It looks overwhelming at first glance and it actually is - but to understand basic routing principles we dont even need to use dedicated router - all we need is couple of VM’s.

### What is router?

In case you are not familliar with basic networking let me explain - router is just an computer - it has CPU, memory, flash, PSU and OS. But main purpose of router is to “route” network traffic. In simple terms it means router is a device which routes traffic from one network to another. 

Very fammiliar situation - you are at home connected to the Internet - in order to fetch that meme of the day of your’s - home router needs to send (route) traffic from your home network to the network’s beyond your router and offc back. 

**This is a basic principle - router is special NW device which has 2 or more network interfaces in multiple networks.**

### Basic Setup

Our basic setup requires hypervisor (VMware,Oracle VBox ) in order to spin 3 VM’s. In my case i am using Oracle VBox - Linux distro does not matter as well. Basic functions used in this project are supported by every Linux distro out there. Lets roll:

1.**Spin three Linux VM’s**

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled.png)

2.**Add NW interfaces** - in order to simulate routing we need to have two separate network segments - in other words two separate networks.  Why? Because devices on the same network does not require router - they can communicate.

**Tools > Network > Choose Host-only Networks (this will create LAN inside the Oracle VBox) and Click create**

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%201.png)

**!!! Remember - we need two separate networks like this.**

**Interface#1**

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%202.png)

**Interface#2**

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%203.png)

**Also enable DHCP to automatically assign IP adresses**

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%204.png)

**3.Assign Interfaces to VM’s - each Client VM (VM#1 and VM#2) will belong to the one network - Linux Router VM will belong to both** 

VM#1 will belong to the Network#1 - Click on VM > Settings > Network

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%205.png)

VM#2 will belong to the Network#2 - Click on VM > Settings > Network

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%206.png)

Linux Router needs to have both

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%207.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%208.png)

## ICMP and configuration test

VM’s are ready, lets start them and verify the NW configuration and IP connectivity.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%209.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2010.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2011.png)

### IP connectivity VM1 > Router

Ping from VM1 to Router

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2012.png)

Verify ICMP messages on Router side

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2013.png)

### IP connectivity VM2 > Router

Ping from VM2 to Router

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2014.png)

Verify ICMP messages on Router side

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2015.png)

IP connectivity from both VM’s towards the router is working - lets try to ping from VM1 to VM2.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2016.png)

This error message tells us that there is no route to the destination host. Since we are able to reach the Router from both VM’s next step is to configure Router VM to full his purpose.

## Configure routing

First thing we need to do in order to use VM as a router is to enable IPv4 forwarding on the Router VM. IPv4 is a mechanism of Linux system to be able to forward packets from one interface to another. When IPv4 forwarding is enabled, the system can forward packets not explicitly addressed to itself.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2017.png)

Now we need acquire the network preferences for both networks.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2018.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2019.png)

Since we have all NW details fetched lets add static route from VM1 > VM2 and check the routing table.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2020.png)

Now lets verify the IP connectivity from VM1 > VM2

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2021.png)

Looks like no reply on VM2 side, lets see the Router and VM2 side.

Here we can see the ICMP echo is going through Router.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2022.png)

Also there is ICMP echo seen on the VM2 as well.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2023.png)

Reason why we can see only the ICMP echo and no reply resides here:

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2024.png)

There is no route back to the source, so we the connection is only one way - lets fix that.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2025.png)

Cool, now we can see both routes are added on both VM’s towards the router and now verify the connection from VM1 > VM2.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2026.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2027.png)

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2028.png)

As we can see ping replies are getting back, also we can see there is ICMP echo and ICMP reply on Router side and also on VM2 side. Lets verify the VM2 > VM1.

![Untitled](Linux%20router%20a0806cf7c7df4bcf9ffeae70bff4f136/Untitled%2029.png)

Done, our Linux router is functional and we can forward packets between two network segments.