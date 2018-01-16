#  pi

Learn to setup a raspberry pi with everything needed to get rolling with automated provisioning with ansible.  Ease future project deployments and save them to code control allowing more efficient replication of your work down the road.  You can find the [source code on github](https://github.com/jahrik/pi).

Table of Contents
=================

  * [Requirements](#requirements)
  * [Pi Setup](#pi-setup)
     * [Installing raspbian](#installing-raspbian)
     * [Login](#login)
     * [Configure passwordless ssh](#configure-passwordless-ssh)
     * [Vncviewer](#vncviewer)
     * [Disable Screen Blanking](#disable-screen-blanking)
  * [Ansible](#ansible)
  * [Vagrant lab](#vagrant-lab)

## Requirements

* [rasbian](https://www.raspberrypi.org/downloads/raspbian/)
* [ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)
* [realvnc-vnc-viewer](https://www.realvnc.com/en/connect/download/viewer/)


## Pi Setup

### Installing raspbian

Download 2017-09-07-raspbian-stretch.zip and unzip it to your workstation and unzip ${raspbian_latest}
```
wget https://downloads.raspberrypi.org/raspbian_latest
unzip 2017-09-07-raspbian-stretch.zip
```

Insert the micro ssd card into your workstation.
Unmount it if your system auto-mounts it.
```
sudo umount /dev/mmcblk0
sudo umount /dev/mmcblk1
```

Format to FAT 32 before flashing it with raspbian.  Don't skip this step because things can go wrong if you do.  I had to install dosfstools on my system to get this to work.  I'm on arch linux.  You may have different results on ubuntu.
```
sudo mkdosfs -F 32 -v /dev/mmcblk0
```

Copy the image to the micro ssd using `dd`
```
sudo dd bs=1M if=2017-09-07-raspbian-stretch.img of=/dev/mmcblk0
```

### Login

user: pi
pass: raspberry
Use gui config or
```
pi@raspberrypi:~ $ raspi-config
```

### Configure passwordless ssh

From your workstation first generate and then copy over an ssh key.
```
ssh-keygen -b 4096 -t rsa -f ~/.ssh/pi_rsa
ssh-copy-id -i ~/.ssh/pi_rsa pi@192.168.1.114
ssh-add ~/.ssh/pi_rsa
```

### Vncviewer

* Install [realvnc-vnc-viewer](https://www.realvnc.com/en/connect/download/viewer/) locally to remote into pi desktop
* Run vncviewer to connect with default creds: pi, raspberry

### Disable Screen Blanking

* disable screen blanking in lightdm.conf
* ansible will configure this here: [link to config](https://github.com/jahrik/pi/blob/9eb5289750f81995cf38d5654c1fa71295d747a4/site.yml#L22)

lightdm.conf
```
[SeatDefaults]
xserver-command=X -s 0 -dpms 
```

## Ansible

Update the IP for your raspberry pi in the inventory.ini file.
```
[pi]
pi ansible_host=192.168.1.114 ansible_user=pi
```

Run ansible playbook on the pi to finish any provisioning you have defined in the site.yml
```
ansible-playbook -l pi site.yml 

PLAY [raspbian] ***************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [pi]

TASK [Install base] ***********************************************************************************************************
ok: [pi] => (item=[u'realvnc-vnc-server', u'realvnc-vnc-viewer', u'vim'])

TASK [generate /etc/lightdm/lightdm.conf] *************************************************************************************
ok: [pi]

TASK [python virtualenv and wrapper] ******************************************************************************************
ok: [pi] => (item=[u'python-virtualenv', u'virtualenvwrapper'])

TASK [update .bashrc] *********************************************************************************************************
ok: [pi] => (item=export WORKON_HOME=~/.virtualenvs)
ok: [pi] => (item=source /usr/local/bin/virtualenvwrapper.sh)

PLAY RECAP ********************************************************************************************************************
pi                         : ok=5    changed=0    unreachable=0    failed=0
```

## Vagrant lab

How to fire up a virtual machine running debian for testing some of these things.
```
vagrant up
ansible-playbook -l vagrant site.yml 
```
