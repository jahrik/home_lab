# FreeNAS

FreeNAS will be hosting my nfs share and running jails for plex, transmission, etc...

Here's how to install and configure it all.

#### Install FreeNAS on a usb drive.
[Download FreeNAS iso](https://download.freenas.org/11/latest/x64/FreeNAS-11.1-RELEASE.iso)
I'll be installing FreeNAS on a thumb drive in order to free up every possible hard drive slot for storage.  Just like we've been doing with other OS's, such as pfSense and Ubuntu, I'll be using the dd command to burn the image to the drive.
```
sudo dd if=FreeNAS-11.1-RELEASE.iso of=/dev/sdb bs=4M
```

You're going to need a second thumb drive to install the media on.  One that's at least 8G.  I'm going with a 32G drive to ensure I have enough room to install Plex and Transmission and hopefully have plenty left over.
