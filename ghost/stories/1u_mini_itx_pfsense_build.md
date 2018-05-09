# The 1u mini-itx pfsense build

I've had my 1u pfsense box running for a few months now and am pleased with how it performs.  I don't put a very big load on it, but with what I've tested so far, it exceeds my needs in every way.  I have the WAN interface bridged to the public IP I rent from my local ISP.  It runs an OpenVPN Server on this IP that let's me connect to my home network while I'm out and about.  It handles DHCP and DNS Resolution on the LAN network, making it easy to call my lab equipment by hostname and not have to worry about IP addressing as much.  I have plans to add an OpenVPN client, I've been playing around with Squid as a transparent proxy, and so on.

I have an old Linksys E1000 router flashed with dd-wrt that sits on the LAN network and acts as a WiFi access point with pfsense handling DHCP that occasionally has had a quirk or two, but for the most part seams stable enough.  This made it easy to have everything connecting to WiFi to be on the same subnet as my homelab and connect to the PLEX server with the TV and whatever else.  There's still plenty of room for improvement, but it's a start.

| Part        |  Amount  |  Price  | Total |
|:----------- |:--------:|:-------:| -----:|
| [Chassis](https://www.amazon.com/gp/product/B00A7NBO6E/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 1 | $88.91 | $88.91 |
| [Power Supply](https://www.amazon.com/gp/product/B01LWTS2UL/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) | 1 | $79.99 | $79.99 |
| [1U Fan]() | 2 | $9.95 | $19.90 |
| [Motherboard](https://www.amazon.com/gp/product/B07638L88W/ref=od_aui_detailpages01?ie=UTF8&psc=1) | 1 | $134.99 | $134.99 |
| [Memory](https://www.amazon.com/gp/product/B019FRCQAK/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) | 1 (2x16G) | $329.99 | $329.99 |
| [SSD](https://www.amazon.com/gp/product/B01IAGSDJ0/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 1 | $89.99 | $89.99 |
| [Network Card](https://www.amazon.com/gp/product/B000BMXME8/ref=oh_aui_detailpage_o08_s00?ie=UTF8&psc=1) | 1 | $25.00 | $25.00 |
|**Total**|||**$1370**|

![complete_lid_on.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_lid_on.jpg?raw=true)
