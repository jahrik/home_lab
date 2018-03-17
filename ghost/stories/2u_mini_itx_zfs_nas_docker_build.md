# The 2U Mini-ITX ZFS NAS Docker build

After about a years worth of planning, I have finally built my 2U smallish form NAS server.  The original plan was to build a simple FreeNAS box for hosting movies, files, backups and run a few containers for PLEX and whatever else is cool these days.  It turned into an Ubuntu 16.04 box with a 5 disk ZFS pool and Docker Swarm as a container service.  I am really happy with how well it is performing and feel way less restricted by the container services FreeNAS provides.

I have a small 2-post 12U rack that hosts my homelab.  It sits in my home office within ear-shot of my desk, so I have a few requirements when it comes to putting things together.  Most important being size and sound.  I found a 2u chassis on Amazon and stuffed as much power as I could afford in to it.  I'm extremely happy with the results and am having a blast configuring my new machine.

| Part | Details | Amount | Price | Total |
|:---- |:-------:|:-------:|:------:|:-----:| ------:|
| thing | stuff  | do      | that   | thing | stuff  | 


| [Chassis](https://www.amazon.com/gp/product/B00A7NBO6E/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 19 x 15.35 x 3.5 in There's a vent in the lid for the power supply | 1 | $88.91 | $88.91 |
| [Power Supply](https://www.amazon.com/gp/product/B01LWTS2UL/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) | 550W, Modular,3.35in (35.09mm), which fits in the 2U snug. | 1 | $79.99 | $79.99 |
| [Disk Cage](https://www.amazon.com/gp/product/B004IMKTUW/ref=oh_aui_detailpage_o07_s01?ie=UTF8&psc=1) | 2BAY 5.25IN Sas 2X5.25 To 3X3.5 Cage | $67.99 | $67.99 |
| [Hard Drives](https://www.amazon.com/gp/product/B075G1N6MH/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) | Hitachi Ultrastar 7K3000 (0F12471) 3TB 64MB Cache 7200RPM SATA III (6.0Gb/s) 3.5" (Refurbished) | 6 | $54.95 | $329.70 |
| [2U Fan](https://www.amazon.com/gp/product/B00KF7MVI2/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) | I am a huge fan of Noctua!  They make a quality product that's nice and quiet and help fill your spare parts drawer with extras | 2 | $9.95 | $19.90 |
| [CPU Fan](https://www.amazon.com/gp/product/B075SF5QQ8/ref=oh_aui_detailpage_o02_s01?ie=UTF8&psc=1) | Also from Noctua.  This thing barely fit, but I think it was worth the struggle of installation.  The CPU stays nice and cool. | 1 | $49.90 | $49.90 |
| [CPU](https://www.amazon.com/gp/product/B0759FGJ3Q/ref=od_aui_detailpages00?ie=UTF8&psc=1) | Intel BX80684I58400 8th Gen Core i5-8400 Processor | 1 | $179.00 | $179.00 |
| [Motherboard](https://www.amazon.com/gp/product/B07638L88W/ref=od_aui_detailpages01?ie=UTF8&psc=1) | ASRock motherboard Motherboards Z370M-ITX/AC | 1 | $134.99 | $134.99 |
| [Memory](https://www.amazon.com/gp/product/B019FRCQAK/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) | Crucial 32GB Kit (16GBx2) DDR4 2400 MT/s (PC4-19200) DR x8 DIMM 288-Pin Memory - CT2K16G4DFD824A | 1 kit (2x16G sticks) | $329.99 | $329.99 |
| [SSD](https://www.amazon.com/gp/product/B01IAGSDJ0/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | OS disk. Crucial MX300 275GB 3D NAND SATA M.2 (2280) Internal SSD - CT275MX300SSD4.  I started with ubuntu installed on a USB, but it was painfully slow, so I sacrificed SATA_0 and went with this M.2 SSD.  This thing is fast as Hell and I'm OK with the loss of space without that 6th SATA drive. | 1 | $89.99 | $89.99 |

**TOTAL: $1370**

