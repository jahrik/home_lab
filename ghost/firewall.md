# Building a firewall with pfSense

When I first signed up with my current ISP a few years back, I started with a student plan that was around $35 a month for basic speeds.  More than enough for me to attend online classes and play the occasional MMO.  Fast-forward a few years and they were charging me about $180 a month because of our family's usage of video streaming services and massive game downloads for the PS4.  Me working from home more often and constantly downloading vagrant boxes and docker images all day didn't help either.  I called up my local ISP and asked about a business account.  I had to sign a 3 year contract, but they gave me the same speeds I had without the data caps, plus a public IP to use.  So I thought to myself, "Neat!, what can I do with that?  Maybe I could tinker around with hosting something?"

It took me about a week to get the public IP somewhat working.  I'm still not happy with it and it still needs more work.  I'll try and explain it all here in hopes that I can save someone else out there some of the pain I went through figuring it all out.

## Installing pfSense

[Download pfSense](https://www.pfsense.org/download/)
I normally burn it to a usb, but you can also use a cd.  The dd command is my go to for this one.

First mount the usb and check to see what /dev/name it has.
```
lsblk
NAME                        MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sdb                           8:16   1   1.9G  0 disk
├─sdb1                        8:17   1   825M  0 part
└─sdb2                        8:18   1   2.3M  0 part
```
**Be sure to not overwrite your OS installation with this command!**
**If you run this against /dev/sda it's possible you will wipe out your system.**
**Be very careful with dd!**

Knowing that we have the right device, your command will look something like this.
```
sudo dd if=pfSense-CE-memstick-2.3.5-RELEASE-amd64.img of=/dev/sdb bs=1M
```
if = input file
of = output file
bs = bit size (size of chunks that are written at a time.)

#### Testing on the netbook
I was able to install and run pfSense on an old netbook with very little power.  The system requirements are [ridiculously low!](https://www.pfsense.org/products/#requirements).  I used a [usb to ethernet converter](https://www.amazon.com/AmazonBasics-1000-Gigabit-Ethernet-Adapter/dp/B00M77HMU0/ref=sr_1_5_sspa?s=electronics&ie=UTF8&qid=1514528693&sr=1-5-spons&keywords=ethernet+addapter&psc=1) to get a second NIC.

#### Upgrade to the 1u
The netbook actually handled pfSense well enough, but being one of the mail fail points of my home lab network, I wanted something a little more reliable.  With a few months worth of [bonus.ly](https://bonus.ly/) dollars from work and saved funds I didn't spend on coffee and beer, I was able to put together a 1u server for use as my firewall.  I put more than enough power in it to handle everything I want to test out in the lab.
* [1u server chassis](https://www.amazon.com/gp/product/B0053YKPCG/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
* [cutest little motherboard cpu combo I could find](https://www.newegg.com/Product/Product.aspx?Item=N82E16813119016)
  * Added bonus being it's fanless and super quiet to run.
* [quiet fans](https://www.amazon.com/gp/product/B009NQLT0M/ref=oh_aui_detailpage_o09_s01?ie=UTF8&psc=1)
  * Currently unplugged right now because it stays cool enough without it so far.
* [200W PSU](https://www.amazon.com/gp/product/B004VP6YGY/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
  * Should be enough
* I already had a few other things lying around that I didn't have to buy for this setup
  * 60G ssd
  * 2x4G RAM
* Plans to find a 2 to 4 port ethernet card and flexible riser card, but have not yet.
  * I'm still using the usb to ethernet adapter I was using in tests on the netbook.
  * I'll get around to it eventually.

## WAN

#### Public IP

When I switched to the business plan with my local ISP, they gave me use of a public IP.  I used this to setup vpn access to the home network and to test out hosting something (this blog).  They gave me 3 values over the phone that I used to set it all up on the WAN interface of the firewall.

* public ip (ex. 123.123.123.123)
* gateway (ex. 123.123.123.122)
* subnet mask (ex. 255.255.255.252)
    * This equates to a /30 [subnet](https://subnettingpractice.com/cheatsheet.html)

I plugged the ethernet adapted line into the router provided by my ISP and configured a Static IP the above mentioned information.  The easiest way I found to test this was to temporarily enable a rule to allow port 80 on the WAN interface to the management page.
![wan firewall rule to allow port 80](https://github.com/jahrik/home_lab/raw/master/ghost/images/edit_wan_port_80.png)

## LAN

The LAN network is easy enough to setup.  Pick a subnet that works with your current home network if you have one. 192.168.0.0/16, 10.0.0.0/8, or whatever you prefer.  Just be sure the use a network that works for a [private address space](https://www.arin.net/knowledge/address_filters.html)

#### DHCP

#### DNS

## VPN

#### TODO
* DHCP
* DNS
    * Resolver
    * Forwarder
    * Able to ping 8.8.8.8, but not resolving hostnames
    * hostname resolution now, but apt-get on LAN DOES NOT WORK, why?
* Squid proxy
    * Local Cache
    * Light Squid
    * Transparent http
    * https proxy setup and handling certs
        * Uploading ca-certificates to your ubuntu machines with ansible
* NAT
  * Port forwarding public_ip:80 to the ghost blog

#### Testing http proxy
```
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
```
