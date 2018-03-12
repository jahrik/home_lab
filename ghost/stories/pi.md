#  pi

Learn to setup a raspberry pi with the base needed to get rolling with ansible provisioning.  With a setup like this you can ease future project deployments and save everything to code control allowing for more efficient replication of your work down the road.  You can find the [source code on github](https://github.com/jahrik/pi).

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
Manual configuration steps for preparing the pi for ansible provisioning

### Installing raspbian
Download 2017-09-07-raspbian-stretch.zip and unzip it to your workstation and unzip raspbian_latest
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
Run `vncviewer` to connect to a remote desktop on your pi with the following creds:

user: pi
pass: raspberry

Or ssh to the pi and run the command line tool.
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
I'm using this setup to run my pi on a large monitor above my desk with a bit of a HUD.  I run chromium with a few tabs open and then installed an addon that cycles through the few tabs I have open every few minutes.  This lets me have a few dashboards up for work, like serverdensity, JIRA, and hipchat, etc...
* ansible will configure this here: [link to config](https://github.com/jahrik/pi/blob/977dcd2abf5837d2e78461a408d2ed154e499617/templates/lightdm.conf.j2#L169)
```
[SeatDefaults]
xserver-command=X -s 0 -dpms 
```

## Ansible
Update the IP for your raspberry pi in the [inventory.ini](https://github.com/jahrik/pi/blob/master/inventory.ini) file.
```
[raspbian]
pi ansible_host=192.168.1.129

[raspbian:vars]
ansible_user=pi
ansible_python_interpreter=/usr/bin/python2.7
```

Update the [site.yml](https://github.com/jahrik/pi/blob/master/site.yml) file with the basics and go from there.

This example will:
* Run apt-get update
* Install some base packages
  * realvnc-vnc-server
  * realvnc-vnc-viewer
  * vim
* generate a new /etc/lightdm/lightdm.conf
  * Disable screen locking
  * Notify a handler to restart lightdm service.
* Install python tools
  * python-virtualenv
  * virtualenvwrapper
* Use the lineinfile module to
  * Update your .bashrc file
  * Add lines if they don't exist
  * configure virtualenv
* Trigger the handler if lightdm.conf has been updated.

**site.yml**
```
---
- hosts: raspbian
  become: true
  become_method: sudo

  roles:
  # - placeholder

  tasks:

    - name: Install base
      apt:
        update_cache: yes
        name: "{{ item }}"
        state: present
      with_items:
        - realvnc-vnc-server
        - realvnc-vnc-viewer
        - vim
      tags:
        - base

    - name: generate /etc/lightdm/lightdm.conf
      template:
        src: lightdm.conf.j2
        dest: /etc/lightdm/lightdm.conf
        owner: root
        group: root
        mode: 0644
        notify: restart lightdm service
      tags:
        - lightdm

    - name: python virtualenv and wrapper
      apt:
        update_cache: yes
        name: "{{ item }}"
        state: present
      with_items:
        - python-virtualenv
        - virtualenvwrapper
      tags:
        - python

    - name: update .bashrc
      lineinfile:
        path: /home/pi/.bashrc
        line: "{{ item }}"
        owner: root
        group: root
        mode: 0644
      with_items:
        - export WORKON_HOME=~/.virtualenvs
        - source /usr/local/bin/virtualenvwrapper.sh

  handlers:

    - name: restart lightdm service
      service:
        name: lightdm
        state: restarted
      tags:
        - lightdm
```

Run the site.yml playbook against the pi
```
ansible-playbook -l raspbian site.yml

PLAY [raspbian] ***************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [pi]

TASK [Install base] ***********************************************************************************************************
ok: [pi] => (item=[u'realvnc-vnc-server', u'realvnc-vnc-viewer', u'vim'])

TASK [generate /etc/lightdm/lightdm.conf] *************************************************************************************
changed: [pi]

TASK [python virtualenv and wrapper] ******************************************************************************************
ok: [pi] => (item=[u'python-virtualenv', u'virtualenvwrapper'])

TASK [update .bashrc] *********************************************************************************************************
ok: [pi] => (item=export WORKON_HOME=~/.virtualenvs)
ok: [pi] => (item=source /usr/local/bin/virtualenvwrapper.sh)

RUNNING HANDLER [restart lightdm service] *************************************************************************************
changed: [pi]

PLAY RECAP ********************************************************************************************************************
pi                         : ok=6    changed=2    unreachable=0    failed=0
```

## Vagrant lab

Fire up a virtual machine running debian for testing some of these things.
```
vagrant up
ansible-playbook -l vagrant site.yml 
```
