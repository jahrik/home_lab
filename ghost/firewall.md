# Building a firewall with pfSense

When I first signed up with my current ISP a few years back, I started with a student plan that was around $35 a month for basic speeds.  More than enough for me to attend online classes and play the occasional MMO.  Fast-forward a few years and they were charging me about $180 a month because of our family's usage of video streaming services and massive game downloads for the PS4.  Me working from home more often and constantly downloading vagrant boxes and docker images all day didn't help either.  I called up my local ISP and asked about a business account.  I had to sign a 3 year contract, but they gave me the same speeds I had without the data caps, plus a public IP to use.  So I thought to myself, "Neat!, what can I do with that?  Maybe I could tinker around with hosting something?"

It took me about a week to get the public IP somewhat working.  I'm still not happy with it and it still needs more work.  I'll try and explain it all here in hopes that I can save someone else out there some of the pain I went through figuring it all out.

## Vagrant lab

If you would like to test any of these setting in a virtual environment before committing them to your home lab, you can create a quick virtual environment with a few simple steps.  Here is one way to do so with vagrant and virtualbox. I'll be using a vagrant box from [kennyl/pfsense](https://app.vagrantup.com/kennyl/boxes/pfsense)

Create a file named Vagrantfile using the init command.
```
vagrant init kennyl/pfsense
```

Open is with whatever text editor you use and customize it to your liking.  I've added a few things that will help get things moving.

Vagrantfile
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|

  config.vm.box = 'kennyl/pfsense'

  # bridge to WiFi NIC for WAN interface
  config.vm.network 'public_network', bridge: 'wlp4s0'

  # bridge to Ethernet NIC for LAN interface
  config.vm.network 'public_network', bridge: 'enp0s31f6'

  # Disable folder sync to fix BSD mount error on linux
  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.provider 'virtualbox' do |vb|

    # Display the VirtualBox GUI when booting the machine
    vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = '1024'

  end
end
```
* wlp4s0 is my WiFi NIC
* enp0s31f6 is my Ethernet NIC
* You'll want to update the Vagrant file with the values on your machine

I found these with the `ip link` command. Keep in mind that I'm on Arch linux.
```
ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s31f6: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether <redacted> brd ff:ff:ff:ff:ff:ff
3: wlp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether <redacted> brd ff:ff:ff:ff:ff:ff
```

Start the vm with
```
vagrant up
```
![pfsense startup](https://github.com/jahrik/home_lab/raw/master/ghost/images/pfsense_startup.png)

## Installing pfSense to your hardware

From your workstation [download pfSense](https://www.pfsense.org/download/)
I normally burn it to a USB, but you can also use a cd.  The dd command is my go to for this one.

First mount the usb and check to see what /dev/name it has with `lsblk`
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

## Testing on the netbook
I was able to install and run pfSense on an old netbook with very little power.  The system requirements are [ridiculously low!](https://www.pfsense.org/products/#requirements).  I used a [usb to ethernet converter](https://www.amazon.com/AmazonBasics-1000-Gigabit-Ethernet-Adapter/dp/B00M77HMU0/ref=sr_1_5_sspa?s=electronics&ie=UTF8&qid=1514528693&sr=1-5-spons&keywords=ethernet+addapter&psc=1) to get a second NIC.

## Upgrade to the 1u
The netbook actually handled pfSense well enough, but being one of the main fail points of my home lab network, I wanted something a little more reliable.  With a few months worth of [bonus.ly](https://bonus.ly/) dollars from work and saved funds I didn't spend on coffee and beer, I was able to put together a 1u server for use as my firewall.  I put more than enough power in it to handle everything I want to test out in the lab.
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
  * I'm still using the USB to ethernet adapter I was using in tests on the netbook.
  * This is still in development.

## WAN

### Public IP

When I switched to the business plan with my local ISP, they gave me use of a public IP.  I used this to setup vpn access to the home network and to test out hosting something (this blog).  They gave me 3 values over the phone that I used to set it all up on the WAN interface of the firewall.

* public ip (ex. 123.123.123.123)
* gateway (ex. 123.123.123.122)
* subnet mask (ex. 255.255.255.252)
    * This equates to a /30 [subnet](https://subnettingpractice.com/cheatsheet.html)

I plugged the ethernet adapted line into the router provided by my ISP and configured a Static IP the above mentioned information.  The easiest way I found to test this was to temporarily enable a rule to allow port 80 on the WAN interface to the management page.
![wan firewall rule to allow port 80](https://github.com/jahrik/home_lab/raw/master/ghost/images/edit_wan_port_80.png)

### VPN

The basics of setting up VPN and how to create a [service with a little help from ansible](https://github.com/jahrik/ansible-arch-workstation/blob/master/roles/vpn/tasks/main.yml) to enable, start, and stop your vpn connections on a linux system.

To be continued...

## LAN

The LAN network is easy enough to setup.  Pick a subnet that works with your current home network if you have one. 192.168.0.0/16, 10.0.0.0/8, or whatever you prefer.  Just be sure the use a network that works for a [private address space](https://www.arin.net/knowledge/address_filters.html)

### DHCP

I chose to enable [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol).  This lets me plug something in to the LAN side of things and have an IP address automatically assigned to that device without having to manually set that part up.  A nice time saver most people will want to go with, especially if they're just starting out.  Setting up DHCP can be enabled and configured either during the initial installation of pfSense of after through the web interface.  I'll show you how to do both here.  Be sure to check out their [official docs](https://doc.pfsense.org/index.php/DHCP_Server) if you need more.

### DNS

The basics of setting up and enabling DNS on the LAN interface.  How to find and forward to your closest public DNS servers and pass this information on to systems connected to your firewall.

## TODO

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

## Testing http proxy
```
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
```
