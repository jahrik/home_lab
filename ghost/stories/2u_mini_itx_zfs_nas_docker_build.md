# The 2U Mini-ITX ZFS NAS Docker build

After about a years worth of planning, I have finally built my 2U smallish form NAS server.  The original plan was to build a simple FreeNAS box for hosting movies, files, backups and run a few containers for PLEX and whatever else is cool these days.  It turned into an Ubuntu 16.04 box with a 5 disk ZFS pool and Docker Swarm as a container service.  I am really happy with how well it's performing and feeling way less restricted by the container services FreeNAS provides.

I have a small 2-post 12U rack that hosts my homelab.  It sits in my home office within ear-shot of my desk, so I have a few requirements when it comes to putting things together.  Most important being size and sound.  I found a 2u chassis on Amazon and stuffed as much power as I could afford in to it.  I'm extremely happy with the results and am having a blast configuring my new machine.

| Part        |  Amount  |  Price  | Total |
|:----------- |:--------:|:-------:| -----:|
| [Chassis](https://www.amazon.com/gp/product/B00A7NBO6E/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 1 | $88.91 | $88.91 |
| [Power Supply](https://www.amazon.com/gp/product/B01LWTS2UL/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) | 1 | $79.99 | $79.99 |
| [Disk Cage](https://www.amazon.com/gp/product/B004IMKTUW/ref=oh_aui_detailpage_o07_s01?ie=UTF8&psc=1) | 1 | $67.99 | $67.99 |
| [Hard Drives](https://www.amazon.com/gp/product/B075G1N6MH/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) | 6 | $54.95 | $329.70 |
| [2U Fan](https://www.amazon.com/gp/product/B00KF7MVI2/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) | | 2 | $9.95 | $19.90 |
| [CPU Fan](https://www.amazon.com/gp/product/B075SF5QQ8/ref=oh_aui_detailpage_o02_s01?ie=UTF8&psc=1) | 1 | $49.90 | $49.90 |
| [CPU](https://www.amazon.com/gp/product/B0759FGJ3Q/ref=od_aui_detailpages00?ie=UTF8&psc=1) | 1 | $179.00 | $179.00 |
| [Motherboard](https://www.amazon.com/gp/product/B07638L88W/ref=od_aui_detailpages01?ie=UTF8&psc=1) | 1 | $134.99 | $134.99 |
| [Memory](https://www.amazon.com/gp/product/B019FRCQAK/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) | 1 kit (2x16g sticks) | $329.99 | $329.99 |
| [SSD](https://www.amazon.com/gp/product/B01IAGSDJ0/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) | 1 | $89.99 | $89.99 |
|**Total**|||**$1370**|

This server ended up being powerful enough to replace my 3-node docker swarm cluster built from old laptops and my gaming machine turned FreeNAS box.  This means a significant decrease in power consumption and maintenance for me.  Overall, I'm really happy with how it's performing.  I did end up fumbling around for the first few hours on the BIOS of the Z370M-ITX/AC until I got used to it.  One main gotcha I ran into was having to add my USB and then SSD to the UEFI boot list in the BIOS in order to get them to boot correctly.  It took me hours of failing to boot to figure this out.  What a pain in the ass...

Here she is with the lid on.
![complete_lid_on.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_lid_on.jpg?raw=true)

It's a snug fit.
![complete_lid_off.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_lid_off.jpg?raw=true)

Loading these three drives was very easy with this thing.  Plus it looks cool.
![disk_cage_front.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/disk_cage_front.jpg?raw=true)
![disk_cage_open.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/disk_cage_open.jpg?raw=true)

The power supply fit as far as size goes.  The screws however did not line up.  I was able to get one screw in the spot that has some wiggle room.  This is a great unit and has all the cables I could ever need for quite a few builds.  It was a tight fit in the box, but is kicking ass.
![psu_back.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_back.jpg?raw=true)

There is a vent built in to the top of the lid for the power supply to breath.
![psu_vent.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_vent.jpg?raw=true)

While installing this server the first couple of times, it wasn't even fully loaded with drives and all the goodies.  I held it in one hand while fumbling with the screwdriver in the other.  I eventually figured out I could save myself some pain by propping it up on my feet while I fasten it to the rack.
![holding_server_with_feet.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/holding_server_with_feet.jpg?raw=true)

Installing the fans.
![fan_install_ready.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_ready.jpg?raw=true)
![fan_install_front.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_front.jpg?raw=true)
![fan_install_front.panel.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_front.panel.jpg?raw=true)
![fan_install_fans_ready.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install_fans_ready.jpg?raw=true)
![fan_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/fan_install.jpg?raw=true)

While putting the front panel back on the screw holes on the left (facing) side did not want to line up again.  I had to push firmly from the top here with a screw driver propped up under-neath to push the push that portion of the chassis into place.
![while_putting_side_back_on_push.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/while_putting_side_back_on_push.jpg?raw=true)

Installing hard disks.
![hard_drives_2.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drives_2.jpg?raw=true)

This spot is really hard to reach with a screw.
![sticky_stuff_hard_to_reach.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_hard_to_reach.jpg?raw=true)
![sticky_stuff_in_screw.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_in_screw.jpg?raw=true)
![sticky_stuff_on_screwdriver.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/sticky_stuff_on_screwdriver.jpg?raw=true)

Of course I was impatient while I waited for the motherboard to be delivered and had to test out this build with an old motherboard.
![old_motherboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/old_motherboard.jpg?raw=true)
![complete_old_motherboard_messy.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_old_motherboard_messy.jpg?raw=true)
![complete_old_motherboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/complete_old_motherboard.jpg?raw=true)
Had to use a splitter to reach that last 6th drive.  Not for a lack of connections with the power supply, but length of cable.
![hard_drive_power_splitter_thing.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drive_power_splitter_thing.jpg?raw=true)

I really enjoy this crazy looking thing.  I think the main reason I bought it was because it looks so cool.  It functions very well and is whisper quiet, like you would expect from Noctua.
![cpu_cooler_bottom.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_bottom.jpg?raw=true)
![cpu_cooler_top.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_top.jpg?raw=true)

This took some time to get a connection on the screws to fasten it to the board.  You have to use the small holed at the top to fit the screw driver in and I wasn't smart enough at the time to think about removing the fan from the unit while installing and adding it back on later.  I think that might help.
![cpu_cooler_barely_fits.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_cooler_barely_fits.jpg?raw=true)

The motherboard.
![asrock_noctua_arms.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/asrock_noctua_arms.jpg?raw=true)

![hard_drives_back_side.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/hard_drives_back_side.jpg?raw=true)

CPU installed.
![cpu_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/cpu_install.jpg?raw=true)

Close up of the power pins
![close_up_of_power_pins.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/close_up_of_power_pins.jpg?raw=true)

The bios dashboard.  I lost SATA_0 when I plugged the M.2 SSD in, so I went from 6 storage disks down to 5 and also lost the 16G Intel Optane memory stick I was testing out as SWAP space.  Not a huge loss, but the ZFS pool took a hit of 3T.
![bios_dashboard.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/bios_dashboard.jpg?raw=true)

The first install was on a USB stick and it proved to be very slow.
![bios_drives.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/bios_drives.jpg?raw=true)

I'm keeping the eco button turned off for now.
![psu_eco_button.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/psu_eco_button.jpg?raw=true)

Installing the M.2 SSD.  This thing is so fast!!!
![m_2_install.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/m_2_install.jpg?raw=true)
![m_2_install_fits.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/m_2_install_fits.jpg?raw=true)

PLEX runs on docker like a champ and is storing Libraries on the ZFS share.  Doing some Transcoding to stress test the system.
![stress_testing.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/stress_testing.jpg?raw=true)
![stress_testing_sensors.jpg](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/stress_testing_sensors.jpg?raw=true)
