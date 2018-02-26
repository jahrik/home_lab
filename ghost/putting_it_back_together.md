# Putting it back together

I recently moved to Northern Idaho and had to [move this blog to the cloud](https://homelab.business/moving-to-the-cloud/) while I made the transition.  After a couple weeks of packing and unpacking, I am finally comfortable in our new little apartment.  I've been putting the homelab back to together and making some improvements along the way.  It's been a fun way to re-assess the positioning and wiring of everything in the 24", 2-post rack I'm using. I went ahead and turned my old gaming rig into a FreeNAS box for now, util I can afford all the parts for the 2u FreeNAS box I have planned.  I bought the case for that already and have a few other parts on the way!  This time around, I am also incorporating another old laptop into the docker swarm cluster; making it a total of 3 old laptops in the cluster.  I'll be able to put quite a bit on this and have persistent data by mounting the FreeNAS box as as NFS share across the cluster.  Should be a pretty reliable setup for what it is.

I am still eagerly waiting for the ISP up here to come run a new cable to my office so I can have a direct connection to the internet.  Until that day comes, I am using an old Cisco E1000 router.  The only router, in a pile of 5 I have lying around, that I was able to flash with dd-wrt.  Sorry WNR2000v3, may you find peace in router heaven.  I configured a wireless bridge from the router in my bedroom to this E1000 in my office on the other side of the house.  I racked my brain for 3 solid days trying to set up WDS with a pair of TRENDnet routers I have and COULD NOT get it working for the life of me!  I finally ended up with a working connection on the E1000 and was able to plug my firewall into a LAN port and connect the homelab to the internet to start rebuilding and updating everything!

I was finally able to order the right PCIe x4 networking card and riser cable for the 1u firewall.  I learned, while sizing PCIe cards, the best way to tell what you need is to just look long and hard at a picture, because the rest doesn't make much sense.  With this, i was able to ditch the usb to ethernet adapter I was using for the WAN port of the firewall.  I feel a lot better about it :-)  I have pfsense reset to the factory defaults for now, in hopes that I will actually write down some of my setup this time around while I configure various things like DNS and Squid proxy.

## Rack

While putting the rack back together this time around, I'm making a little more room for new equipment.  I made sure the put the bottom rack as far down as possible.
![putting rack back together](https://github.com/jahrik/home_lab/raw/master/ghost/images/putting_rack_back_together.jpg)

The box to the left is my old gaming machine that has been converted to a FreeNAS box.  On top of that is the E1000 router that has been flashed with dd-wrt and set up as a wireless client bridge.
![putting rack back together full](https://github.com/jahrik/home_lab/raw/master/ghost/images/putting_rack_back_together_full.jpg)
The rack itself contains the following:
* Monitor up top
* Two trouble-shooting/extra switches
  * One 8 port dumb switch
  * One 5 port managed switch with port mirroring capabilities for security onion sensor
* Netgear 24 port switch
* PfSense Firewall
* EMPTY new 2u box for housing the soon to be FreeNAS box
* Bottom shelf holding the 3 laptop docker swarm cluster
  * 2T external hard drive
  * Samsung r580
    * i5
    * 8G RAM
  * Lenovo Thinkpad x1 Carbon
    * i7
    * 8G RAM
  * Asus Netbook
    * Intel Atom
    * 1G RAM

## DD-WRT

I've been patiently waiting for the ISP up here to install a wire into my office, but no luck so far.  After 3 or 4 weeks of waiting, I did finally get an email back and they'll be out here next Wednesday!  Until that happens, I haven't had a network connection to the rack because it's on the other side of the apartment, in my office.  After a few days worth of failing to setup WDS and have a two-access point Wireless Distributed System, I went with something else.  It's not something I intend to keep around, but works for the time being.

If you're looking into using dd-wrt yourself, you might want to start out here: [www.dd-wrt.com/wiki/index.php/Firmware_FAQ](https://www.dd-wrt.com/wiki/index.php/Firmware_FAQ).  If you start with googling dd-wrt, you might end up using the router database, which doesn't get you very far, as it's no longer kept up to date.  Once you've read through that a bit, you can check to see if your router is in [dd-wrt beta downloads](https://download1.dd-wrt.com/dd-wrtv2/downloads/betas/).

Out of the 5 routers I have, I was able to successfully flash one of them.  The Netgear WNR2000v3 ended up getting bricked, but sometimes those things happen when it's 2 a.m. and you're 5 beers in.  The Linksys E1000 worked like a charm and dd-wrt unlocked a ton of functionality.

I followed the [Client Bridged](https://www.dd-wrt.com/wiki/index.php/Client_Bridged) setup on their site to get a connection from the router in my bedroom to this one and finally start updating and re-configuring the lab!
![Pic from the dd-wrt site for client bridge](https://www.dd-wrt.com/wiki/images/7/7d/Client_Bridge.jpg)

## Firewall

After multiple failed attempts, I was finally able to order [the right PCIe x4 networking card](https://www.amazon.com/gp/product/B000BMXME8/ref=oh_aui_detailpage_o04_s00?ie=UTF8&psc=1) and [riser cable](https://www.amazon.com/gp/product/B06WWNVKT2/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) for the 1u firewall.  Installation worked without any problems and I even did a bit of cable management inside that box, while I was at it.
![putting firewall back together](https://github.com/jahrik/home_lab/raw/master/ghost/images/putting_firewall_back_together.jpg)

This gave me 2 extra ports in the back and let me ditch the USB to Ethernet adapter I was using previously.
![firewall 2 ports](https://github.com/jahrik/home_lab/raw/master/ghost/images/firewall_2_ports.jpg)

I reset PfSense to factory defaults and just have a DHCP connection from WAN to the LAN port of the E1000.  LAN on the firewall stayed the same with a 192.168.2.0/24 network and DHCP server actively handing out IPs. The docker swarm hosts behind the firewall are able to get an IP address and reach the outside world again so I'm happy.

## FreeNAS

I don't do a whole lot of gaming anymore, so I decided to put my old gaming machine to good use by loading whatever hard disks I could find and installing FreeNAS.  It worked out pretty well and now I have 2.5T of storage to work with.  This machine has an Intel i5 and 8G of RAM.  I was able to find two 1T hard drives and one 750G hard drive.  I have it set up with a RAID-Z1 which provides me no redundancy and if one of these drives fails, I'm pretty much screwed as I understand it.  So this is NOT a reliable backup solution, but works for my setup right now as I build the real FreeNAS box.
![old_gaming_machine](https://github.com/jahrik/home_lab/raw/master/ghost/images/old_gaming_machine.jpg)

I ordered and fit the new [2u box](https://www.amazon.com/gp/product/B00A7NBO6E/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) that will be housing FreeNAS in the rack to see how it looks and I'm happy so far.
![2u freenas box](https://github.com/jahrik/home_lab/raw/master/ghost/images/2u_freenas_box.jpg)

A few more parts for this will be here next Monday!
* [3.5 disk cage](https://www.amazon.com/gp/product/B004IMKTUW/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1)
* [1 of 2 fans](https://www.amazon.com/gp/product/B00KF7OMTI/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1)
* [This EVGA power supply](https://www.amazon.com/gp/product/B01LWTS2UL/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1)
  * I'm really hoping this one works out, if I can figure out how to secure it.
  * I spent a long time searching 2U power supplies, but none offered space for 6 SATA connections.
  * This one looks slim enough to fit into a 2U box.

I'm still a ways out to affording [the motherboard](https://www.amazon.com/dp/B00GG94YDS/_encoding=UTF8?coliid=I6IV40DWMM4HD&colid=38OM4T826B6H8&psc=0) I want for this build, but I've got some supplies to get me started.

I'm happy with how it's all coming together this time around and excited to bring up [Ansible AWX](https://homelab.business/trialing-ansible-awx/) and provision docker swarm across the three laptops.  Next, I plan on getting AWS to run on docker swarm as apposed to running it with docker-compose for a little more stability and get an ELK stack, Grafana and finally bring this Ghost blog back down from AWS once I get my public IP working.
