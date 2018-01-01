# Firewall

## How I made sense of pfSense
When I first signed up with my current ISP a few years back, I started with a student plan that was around $35 a month for basic speeds.  More than enough for me to attend online classes and play the occasional MMO.  Fast-forward a few years and they were charging me about $180 a month because of our family's usage of video streaming services and massive game downloads for the PS4.  Me working from home more often and constantly downloading vagrant boxes and docker images all day didn't help either.  I called up my local ISP and asked about a business account.  I had to sign a 3 year contract, but they gave me the same speeds I had without the data caps, plus a public IP to use.  So I thought to myself, "Neat!, what can I do with that?  Maybe I could tinker around with hosting something?"

It took me about a week to get the IP somewhat working.  I'm still not happy with it and it still needs more work.  I'll try and explain it all here in hopes that I can save someone else out there some pain.

### Installation
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
I was able to install and run pfSense on an old netbook with very little power.  The system requirements are [ridiculously low!](https://www.pfsense.org/products/#requirements) I used a [usb to ethernet converter](https://www.amazon.com/AmazonBasics-1000-Gigabit-Ethernet-Adapter/dp/B00M77HMU0/ref=sr_1_5_sspa?s=electronics&ie=UTF8&qid=1514528693&sr=1-5-spons&keywords=ethernet+addapter&psc=1) to get a second NIC.

#### Public IP
When I switched to the business plan with my local ISP, they gave me use of a public IP.  I used this to setup vpn access to the home network and to test out hosting something (this blog).  They gave me 3 values over the phone that I used to set it all up on the WAN interface of the firewall.

* public ip (ex. 123.123.123.123)
* gateway (ex. 123.123.123.122)
* subnet mask (ex. 255.255.255.252)
    * This would equate to a /30 [subnet](https://subnettingpractice.com/cheatsheet.html)

#### TODO
* Upgraded to 1u server after testing
* Configure WAN interface with static IP
* LAN interface
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

#### Testing http proxy
```
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/5MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/10MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
wget http://ipv4.download.thinkbroadband.com/20MB.zip
```
