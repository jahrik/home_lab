# The 2U Mini-ITX ZFS NAS Docker build - Part 2 of 2

In [part 1 of this story](https://homelab.business/the-2u-mini-itx-zfs-nas-docker-build/), I give a price listing of all the hardware that went into the new 2U Docker host and pictures of how it all went together.  With it up and running, I'd like to document the installation process here.

## OS

I had originally planned to install FreeNAS on this box for Network storage, while using any left over resources for containers running PLEX and whatever else I could get working.  After a few tests with jails and virtual machines spun up in FreeNAS, I realized I would be much better off sticking with something I'm more comfortable with, Docker Swarm.  I still like FreeNAS and it works great as a NAS, it just lacks the control I need for container management.  I chose ubuntu because it's a distro I work with on a daily basis.  I'm comfortable in that environment and it's a common distro with plenty of support that is easy for most people to use.  I imagine, this setup should work great with any other distro that supports ZFS and Docker.  With Ubuntu 16.04, [ZFS](https://wiki.ubuntu.com/ZFS) is supported out of the box now.

Because I wanted to use every last SATA drive for the ZFS pool, my first few installations of the OS were on a USB drive.  I played around with creating a mirrored OS raid across 2x 32G USB drives, which did not works so well.  I don't think I was able to get this setup to boot, but this was before I figured out I needed to add my boot device to the UEFI bootable list on the Z370M-ITX/AC.  I installed it on 1 USB drive with an LVM setup in-case I wanted to add the second 32G drive for more storage.  This also had poor results and was very slow for reasons I was unable to pin point.  Finally, I did a basic partition scheme straight to one USB drive without LVM or anything else.  This had the best results as far as running the OS from a USB went.  It was still painfully slow when it came to installing the OS on the stick and doing an 'apt update' or clean install of base packages.  We're talking hours wasted here... (in the long run of multiple installations), for something that should be a very quick installation on this new fast hardware and a quick internet connection.

I eventually gave up on the idea of running the OS on a USB stick and opted instead to us the M.2 slot that was currently holding a [16G Optane Memory Module](https://www.amazon.com/gp/product/B06XSMTN31/ref=oh_aui_detailpage_o03_s01?ie=UTF8&psc=1) with an SSD instead.  I experimented a little bit with the 16G Optane stick while it was there and didn't really find a better use for it than as a SWAP space, which did work very well, but seams like a waste.  I ordered a [275GB 3D NAND SATA M.2 SSD](https://www.amazon.com/gp/product/B01IAGSDJ0/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) and installed the OS on that instead.  This thing is blazing fast and is working great for the OS installation drive.  While I was experimenting with the USB stick install, I had Docker writing to `/<zfs_pool>/var/lib/docker`, to avoid doing too many writes to the USB drive, but with this SSD, I just have docker writing to the root of this drive again at `/var/lib/docker` and docker is all the faster for it.  After configuring the SSD, I noticed that I lost one of the SATA drives in the ZFS raid.  I thought maybe I unplugged it on accident while installing the M.2, but after an unnecessary un-racking, open up and inspect, and re-racking of the 2u box, I found that not to be the case.  Reading through the manual informed me that in plugging in the M.2 slot with an SSD, you lose SATA_0... DAMN!!  But, what do you do?  I need my OS to keep up with the rest of this beast!

With my limited knowledge of hardware, I hadn't thought of using the PCIe slot with an SSD, but that was the advice of /u/wolffstarr on [Reddit](https://redd.it/8533k3), and I think that is exactly what I will try next to get back the 6th SATA slot.
> Nice build, think I've got a similar chassis somewhere (might have sold it off, but I recognize the drive cage mountings). Couple of thoughts:
> 1. The splitter you're using looks like it's molded, and while it's not molex to SATA, it's still making me twitchy looking at it. Keep an eye on that so you don't lose the splitter (and the drives connected to it) to burnout/melting. If you can, see about finding one that's crimped instead of molded.
> 2. To avoid the 3TB loss, why not pick up a cheap SATA card? It doesn't look like you're using anything in the PCIe slot, at least on the permanent board. I'm not even talking an HBA, just a Syba 4-port SATA card for $25-30 or whatever.
> - u/wolffstarr

I will also need to replace the molded splitter, when it comes time to use that drive again.  Thanks again, [r/homelab/](https://www.reddit.com/r/homelab/) :-)

## ZFS

With a functional OS and drives online, it came time to configure ZFS.  This turned out to be way easy than I had imagined.


## Docker
