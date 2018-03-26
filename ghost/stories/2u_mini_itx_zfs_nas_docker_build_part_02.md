# The 2U Mini-ITX ZFS NAS Docker build - Part 2 of 2

In [part 1 of this story](https://homelab.business/the-2u-mini-itx-zfs-nas-docker-build/), I give a price listing of all the hardware that went into the new 2U NAS/Docker host and pictures of how it all went together.  With it up and running, I'd like to document the installation process here.

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

With a functional OS and drives online, it came time to configure ZFS.  This turned out to be way easier than I had imagined.  While this motherboard does have support for onboard RAID configuration, I'm opting to test out ZFS, but it's nice to know I have a fall back option if I hate it.

Install ZFS on Ubuntu 16.04

    apt install zfs

The original zpool was created while I still had the OS on a USB stick. This pool consisted of the 6 SATA disks:

    /dev/sda
    /dev/sdb
    /dev/sdc
    /dev/sdd
    /dev/sde
    /dev/sdf

Creating the pool with the following command

    zpool create -f shredder_pool raidz sda sdb sdc sdd sde sdf

This was all it took.  I started loading it up with my collection of Movies and TV shows to host on PLEX.

    mkdir -p /shredder_pool/movies
    mkdir -p /shredder_pool/tv
    rsync -av <old_freenas_box>:/allTheThings /shredder_pool/

When the M.2 drive came and I reinstalled the OS to it, I had to import the zpool with the following.

    zpool import shredder_pool

This worked fine, but because I'm using the M.2 slot, I lost SATA_0 and the pool was in a degraded state because it was now missing `/dev/sda`.  There's probably a way to remove a disk from a zpool, but I ended up destroying it and starting over before I had a chance to find out.  During my unneeded un-rack and check if SATA_0 is plugged in or not, I swapped a couple of the SATA cables because it was knocking off one of the drives in the 3 disk cage and I would prefer that it be the last drive in the chassis itself.  In doing so, I inadvertently knocked 2 of the 6 disks in the zpool offline and the zpool went from degraded to totally destroyed.  Luckily, I wasn't too attached to the data on the pool and I had the option to just start over, but this mistake would suck for someone without a backup.

The new pool consisting of 5 disks was created with the following.  Omitting `/dev/sda`, which is now the OS disk.

    zpool create -f shredder_pool raidz sdb sdc sdd sde sdf

Show status

    zpool status
      pool: shredder_pool
     state: ONLINE
      scan: none requested
    config:

      NAME        STATE     READ WRITE CKSUM
      shredder_pool  ONLINE       0     0     0
        raidz1-0  ONLINE       0     0     0
          sdb     ONLINE       0     0     0
          sdc     ONLINE       0     0     0
          sdd     ONLINE       0     0     0
          sde     ONLINE       0     0     0
          sdf     ONLINE       0     0     0

    errors: No known data errors

Show iostat

    zpool iostat
                      capacity     operations    bandwidth
    pool           alloc   free   read  write   read  write
    -------------  -----  -----  -----  -----  -----  -----
    shredder_pool   867G  12.8T      6    143   799K  2.12M

Grafana Dashboard

![homelab_grafana_zfs](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/homelab_grafana_zfs.png?raw=true)

## NFS

I handled NFS configuration with an ansible script, but it's not very painful to begin with.

Ensure directory exists

    mkdir -p /shredder_pool

Install nfs-server on ubuntu 16.04

    apt install nfs-kernel-server

Create a file at /etc/exports with the following

    /shredder_pool 192.168.0.0/16(rw,sync,no_root_squash,no_subtree_check)

Start and enable the nfs service

    systemctl start nfs-server.service
    systemctl enable nfs-server.service

**NOTE:** because I'm hosting my NFS share on ZFS, I had to run this command to get it to work.

    zfs set sharenfs="rw=@192.168.0.0/16" shredder_pool

This is the playbook.yml file I used to setup an nfs server with ansible.

**nfs_playbook.yml**
```
---
- hosts: nfs-server
  become: true
  become_method: sudo
  vars:
    nfs_exports:
      - /shredder_pool
    nfs_imports:
      - /shredder_pool

  tasks:
    - name: install nfs software
      package:
        name: nfs-kernel-server
        state: present
      tags:
        - nfs

    - name: Ensure directories to export exist
      file:
        path: "{{ item.strip().split()[0] }}"
        state: directory
      with_items: "{{ nfs_exports }}"
      notify: restart nfs
      tags:
        - nfs

    - name: Generate /etc/exports file
      template:
        src: exports.j2
        dest: /etc/exports
        owner: root
        group: root
        mode: 0644
      register: nfs_exports_copy
      notify: restart nfs
      tags:
        - nfs

    - name: Ensure nfs is running
      service:
        name: nfs-server
        state: started
        enabled: yes
      when: nfs_exports|length
      tags:
        - nfs

  handlers:

    - name: restart nfs
      service:
        name: nfs-server
        state: restarted
      when: nfs_exports|length
      tags:
        - nfs
```

And the exports.j2 template used

**templates/exports.j2**
```
{% for export in nfs_exports %}
{{ export }} 192.168.0.0/16(rw,sync,no_root_squash,no_subtree_check)
{% endfor %}
```

## Docker

* [Install Docs](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
* [Swarm Docs](https://docs.docker.com/engine/reference/commandline/swarm_init/)

I also handled the installation and configuration of docker with ansible.  I used a more complicated playbook than the one above, which would be a post in itself, but I'll go over the basic manual commands for setting up a docker swarm cluster of one.

Installation basically boils down to the following commands.

    sudo apt-get update
    sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo apt-key fingerprint 0EBFCD88
    sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) \
      stable"
    sudo apt-get update
    sudo apt-get install docker-ce

Find the network interface docker swarm will use.  In my case, I'm using `enp0s31f6`

    ip link show

    1: lo
    2: enp2s0
    3: enp0s31f6

Initialize the swarm

    docker swarm init --advertise-addr enp0s31f6:2377

Check out the [Swarm Docs](https://docs.docker.com/engine/reference/commandline/swarm_init/) if you need more.

With the swarm initialized, it's ready to start services.  At the time of this writing, I'm up to 17 docker containers running on this box, and it still has a lot of room for growth.

    docker stack ls
    NAME                SERVICES
    awx                 5
    elk                 8
    jenkins             1
    mongo               2
    plex                1

![docker_swarm_monitor_v2](https://github.com/jahrik/home_lab/blob/master/ghost/images/2u_shredder/docker_swarm_monitor_v2.png?raw=true)

I have been having a blast with this build and have already surpassed the needs that sparked the project.

Brief description of what's running on it now:
* PLEX server on docker for watching movies and tv shows.
  * Storage on the ZFS store.
  * Plans for openvpn-client connected Transmission container
  * Plans for Sonarr & Radarr containers
  * The 6 core i5 is paying off, when it comes to video transcoding.
* ELK stack running with logspout
  * Logspout sends all docker container logs to logstash
  * Mounted volumes for persistent storage
* Prometheus server
  * Collecting metrics from
    * Node Exporter
    * Cadvisor
    * Jenkins exporter
    * Prometheus
  * Serving exported metrics to Grafana
* Grafana
  * pulls metrics from
    * Prometheus
    * ELK
* Jenkins master
  * Plans to build a few slaves as additional docker services for
    * testing and continuous deployment of docker stacks
    * Ansible Plugin, maybe kick off AWX through the API too..??
* Ansible AWX (open source tower) stack
  * Running much better on swarm than it did with docker-compose
