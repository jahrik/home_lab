# Moving to the Cloud

I will be moving to northern Idaho in less than a month and will need somewhere else to host homelab during the time it takes to do so.  I already have an Amazon account, so I'll most likely spin up their smallest ec2 instance and upload it to that.  I'm still going to need a Public IP to point cloudflare at and a few other things I'm sure.  I'll go over all of that here.  You can download the [source code](https://github.com/jahrik/home_lab) from github to follow along.

## AWS
Defaults will normally work for this
* A default VPC
* Subnet within the VPC
* An ssh key pair generated to access the ec2 instance.
* An Elastic IP address linked to the instance to point DNS at.
* t2.micro ubuntu ec2 instance
* A security group
  * Enable 22 while we provision (disable after finished)
  * Enable port 80 for http traffic

### EC2 Creation
I started up a t2.micro ubuntu 16.04 instance
* 1 Core vCPU (up to 3.3 GHz)
* 1 GiB Memory RAM
* 8 GB Storage
* FREE TIER ELIGIBLE

Download the pem file at the end of creation to access the instance.
`chown` it to restrict permission to the file and allow it to be added ssh-agent
```
chmod 600 homelab.pem
ssh-add homelab.pem
```

Next create an Elastic IP and link it to the instance from the 'Actions' drop down.  With this newly acquired public IP we can now get to business with provisioning our new machine and deploying ghost to the cloud.

First lets test that we can login.
Be sure and use the default user `ubuntu@<ip_address>` for a newly created ubuntu instance
```
ssh ubuntu@52.25.231.162
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-1047-aws x86_64)
...
ubuntu@ip-172-31-0-177:~$
```

## Ansible provisioning

Next I'll create an [aws_inv.ini](https://github.com/jahrik/home_lab/blob/master/aws_inv.ini) file for ansible to access this machine with a few key variables
* hostname
* username
* ip address
* network interface for swarm configs
* swarm availability mode

**aws_inv.ini**
```
[manager]
homelab

[manager:vars]
ansible_host=52.25.231.162
ansible_user=ubuntu
swarm_iface=eth0
swarm_availability=active
```

If we test ansible now, we'll see this error output.
```
ansible -i aws_inv.ini all -m ping
homelab | FAILED! => {
    "changed": false,
    "module_stderr": "Connection to 52.25.231.162 closed.\r\n",
    "module_stdout": "/bin/sh: 1: /usr/bin/python: not found\r\n",
    "msg": "MODULE FAILURE",
    "rc": 0
}
```

Easy enough to fix this by installing python on the system first.
```
ssh ubuntu@52.25.231.162
sudo apt-get update && sudo apt-get install python
```

Now with python installed we should get a pong back.
```
ansible -i aws_inv.ini all -m ping
homelab | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### Install the base

With that, we are ready to kick off a few scripts to get everything installed and running.  First I need to update my site.yml file and take out the custom setup I have in there that I only needed for my laptop, like ignoring lid closure, etc...

Starter [site.yml](https://github.com/jahrik/home_lab/blob/master/site.yml) file to build on and add any dependencies needed.

**site.yml**
```
---
- hosts: all
  become: true
  become_method: sudo
  tasks:

  - name: apt-get update && install deps
    apt:
      update_cache: yes
      name: "{{ item }}"
      state: present
    with_items:
      - vim
      - wget
      - rsync
      - make
    tags:
      - setup
      - deps
```

Run this against the instance
```
ansible-playbook -i aws_inv.ini site.yml

PLAY [all] ********************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [homelab]

TASK [apt-get update && install deps] *****************************************************************************************
changed: [homelab] => (item=[u'vim', u'wget', u'rsync', u'make'])

PLAY RECAP ********************************************************************************************************************
homelab                    : ok=2    changed=1    unreachable=0    failed=0
```

#### Install docker and enable swarm mode

Next I will install docker and start a one node swarm cluster for hosting the ghost blog using [an ansible playbook](https://github.com/jahrik/home_lab/blob/master/swarm.yml)
```
ansible-playbook -i aws_inv.ini swarm.yml                                                          
```

After that finishes running I can log into the instance and see that docker is in-fact running and in swarm mode.
```
sudo docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
i1hjapxrz3tfkddva4nztnqhs *   ip-172-31-0-177     Ready               Active              Leader
```

### homelab backup

From my homelab I will tar up the docker volume in preparation to move it to AWS
```
tar -czvf content.tar.gz /data/ghost/content
mv content.tar.gz /home/jahrik/
```

Pull it to my local workstation
```
scp 192.168.2.106:/home/jahrik/content.tar.gz .
content.tar.gz                                                                               100%   19MB   7.3MB/s   00:02
```

### SCP to AWS host

Move it up to the cloud
```
scp content.tar.gz ubuntu@52.25.231.162:/home/ubuntu/
content.tar.gz                                                                               100%   19MB 639.9KB/s   00:31
```

Log back in and Inflate it
```
sudo tar -xzvf content.tar.gz
```

Create the right directories to place it in.
```
sudo mkdir -p /data/ghost/content
```

And finally, move it into place
```
sudo cp -R data/ghost/content/* /data/ghost/content/
```

### Start the docker service with a stack file

Clone the home_lab repository off github and start up the docker service.
```
git clone https://github.com/jahrik/home_lab.git

cd home_lab

# edit stack file and change:
# - '2368:2368'
# to
# - '80:2368'
vim ghost/ghost-stack.yml
```

Start it up
```
sudo docker stack deploy -c ghost/ghost-stack.yml ghost
Creating network ghost_default
Creating service ghost_blog
```

Tail the logs to it start up
```
sudo docker service logs -f ghost_blog
ghost_blog.1.oxam1j4poq3m@ip-172-31-0-177    | [2018-01-17 08:02:06] INFO Finished database migration!
ghost_blog.1.oxam1j4poq3m@ip-172-31-0-177    | [2018-01-17 08:02:08] INFO Ghost is running in production...
ghost_blog.1.oxam1j4poq3m@ip-172-31-0-177    | [2018-01-17 08:02:08] INFO Your blog is now available on http://homelab.business/
ghost_blog.1.oxam1j4poq3m@ip-172-31-0-177    | [2018-01-17 08:02:08] INFO Ctrl+C to shut down
ghost_blog.1.oxam1j4poq3m@ip-172-31-0-177    | [2018-01-17 08:02:08] INFO Ghost boot 2.106s
```

Browse to the public IP [52.25.231.162](http://52.25.231.162/) and I can see it running.

![ghost default](https://github.com/jahrik/home_lab/blob/master/ghost/images/ghost_default.png?raw=true)

#### Redirect Cloudflare to the new public IP.

![google domain](https://github.com/jahrik/home_lab/blob/master/ghost/images/cloudflare.png?raw=true)

#### Test it all out.

[homelab.business](https://homelab.business/)
