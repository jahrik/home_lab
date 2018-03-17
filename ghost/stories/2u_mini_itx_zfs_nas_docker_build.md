# The 2U Mini-ITX ZFS NAS Docker build

After about a years worth of planning, I have finally built my 2U smallish form NAS server.  The original plan was to build a simple FreeNAS box for hosting movies, files, backups and run a few containers for PLEX and whatever else is cool these days.  It turned into an Ubuntu 16.04 box with a 5 disk ZFS pool and Docker Swarm as a container service.  I am really happy with how well it's performing and feeling way less restricted by the container services FreeNAS provides.

I have a small 2-post 12U rack that hosts my homelab.  It sits in my home office within ear-shot of my desk, so I have a few requirements when it comes to putting things together.  Most important being size and sound.  I found a 2u chassis on Amazon and stuffed as much power as I could afford in to it.  I'm extremely happy with the results and am having a blast configuring my new machine.

| Part        |                       Details                         |  Amount  |  Price  | Total |
|:----------- |:-----------------------------------------------------:|:--------:|:-------:| -----:|
| [Chassis](https://www.amazon.com/gp/product/B00A7NBO6E/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 19 x 15.35 x 3.5 in There's a vent in the lid for the power supply | 1 | $88.91 | $88.91 |
| [Power Supply](https://www.amazon.com/gp/product/B01LWTS2UL/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) | 550W, Modular,3.35in (35.09mm), which fits in the 2U snug. | 1 | $79.99 | $79.99 |
| [Disk Cage](https://www.amazon.com/gp/product/B004IMKTUW/ref=oh_aui_detailpage_o07_s01?ie=UTF8&psc=1) | 2BAY 5.25IN Sas 2X5.25 To 3X3.5 Cage | 1 | $67.99 | $67.99 |
| [Hard Drives](https://www.amazon.com/gp/product/B075G1N6MH/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) | Hitachi Ultrastar 7K3000 (0F12471) 3TB 64MB Cache 7200RPM SATA III (6.0Gb/s) 3.5" (Refurbished) | 6 | $54.95 | $329.70 |
| [2U Fan](https://www.amazon.com/gp/product/B00KF7MVI2/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) | I am a huge fan of Noctua!  They make a quality product that's nice and quiet and help fill your spare parts drawer with extras | 2 | $9.95 | $19.90 |
| [CPU Fan](https://www.amazon.com/gp/product/B075SF5QQ8/ref=oh_aui_detailpage_o02_s01?ie=UTF8&psc=1) | Also from Noctua.  This thing barely fit, but I think it was worth the struggle of installation.  The CPU stays nice and cool. | 1 | $49.90 | $49.90 |
| [CPU](https://www.amazon.com/gp/product/B0759FGJ3Q/ref=od_aui_detailpages00?ie=UTF8&psc=1) | Intel BX80684I58400 8th Gen Core i5-8400 Processor | 1 | $179.00 | $179.00 |
| [Motherboard](https://www.amazon.com/gp/product/B07638L88W/ref=od_aui_detailpages01?ie=UTF8&psc=1) | ASRock motherboard Motherboards Z370M-ITX/AC | 1 | $134.99 | $134.99 |
| [Memory](https://www.amazon.com/gp/product/B019FRCQAK/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) | Crucial 32GB Kit (16GBx2) DDR4 2400 MT/s (PC4-19200) DR x8 DIMM 288-Pin Memory - CT2K16G4DFD824A | 1 kit (2x16G sticks) | $329.99 | $329.99 |
| [SSD](https://www.amazon.com/gp/product/B01IAGSDJ0/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | OS disk. Crucial MX300 275GB 3D NAND SATA M.2 (2280) Internal SSD - CT275MX300SSD4.  I started with ubuntu installed on a USB, but it was painfully slow, so I sacrificed SATA_0 and went with this M.2 SSD.  This thing is fast as Hell and I'm OK with the loss of space without that 6th SATA drive. | 1 | $89.99 | $89.99 |
|**Total**||||**$1370**|

This server ended up being powerful enough to replace my 3-node, old-laptop docker swarm cluster and my gaming machine turned FreeNAS box.  This means a significant decrease in power consumption and maintenance for me.  Of course, I'll bring an old laptop or two back into the mix soon enough, I do need a security onion box next and the old Samsung r580 will run that just fine.  Overall, I'm really happy with how it's performing.  I did end up fumbling around for the first few hours on the BIOS of the Z370M-ITX/AC until I got used to it.  One main gotcha I ran into was having to add my USB and then SSD to the UEFI boot list in the BIOS in order to get them to boot correctly.  It took me hours of failing to boot to figure this out.  What a pain in the ass...

![complete_lid_on.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_lid_on.jpg?raw=true)
![complete_lid_off.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_lid_off.jpg?raw=true)

![disk_cage_front.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/disk_cage_front.jpg?raw=true)
![disk_cage_open.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/disk_cage_open.jpg?raw=true)
![psu_back.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_back.jpg?raw=true)
![complete_old_motherboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_old_motherboard.jpg?raw=true)
![psu_vent.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_vent.jpg?raw=true)
![holding_server_with_feet.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/holding_server_with_feet.jpg?raw=true)
![fan_install_ready.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_ready.jpg?raw=true)
![fan_install_fans_ready.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_fans_ready.jpg?raw=true)
![fan_install_front.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_front.jpg?raw=true)
![fan_install_front.panel.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_front.panel.jpg?raw=true)
![fans_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fans_install.jpg?raw=true)
![while_putting_side_back_on_push.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/while_putting_side_back_on_push.jpg?raw=true)
![sticky_stuff_hard_to_reach.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_hard_to_reach.jpg?raw=true)
![sticky_stuff_in_screw.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_in_screw.jpg?raw=true)
![sticky_stuff_on_screwdriver.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_on_screwdriver.jpg?raw=true)
![hard_drives_2.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drives_2.jpg?raw=true)
![old_motherboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/old_motherboard.jpg?raw=true)
![complete_old_motherboard_messy.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_old_motherboard_messy.jpg?raw=true)
![hard_drive_power_splitter_thing.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drive_power_splitter_thing.jpg?raw=true)
![cpu_cooler_bottom.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_bottom.jpg?raw=true)
![cpu_cooler_top.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_top.jpg?raw=true)
![cpu_cooler_barely_fits.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_barely_fits.jpg?raw=true)
![asrock_noctua_arms.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/asrock_noctua_arms.jpg?raw=true)
![hard_drives_back_side.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drives_back_side.jpg?raw=true)
![cpu_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_install.jpg?raw=true)
![close_up_of_power_pins.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/close_up_of_power_pins.jpg?raw=true)
![bios_dashboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/bios_dashboard.jpg?raw=true)
![bios_drives.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/bios_drives.jpg?raw=true)
![psu_eco_button.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_eco_button.jpg?raw=true)
![m_2_install_fits.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/m_2_install_fits.jpg?raw=true)
![m_2_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/m_2_install.jpg?raw=true)
![power_it_on.mov](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/power_it_on.mov?raw=true)
![stress_testing.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/stress_testing.jpg?raw=true)
![stress_testing_sensors.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/stress_testing_sensors.jpg?raw=true)
