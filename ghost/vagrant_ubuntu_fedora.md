# Vagrant multiple boxes - Ubuntu 16.04 and Fedora 27

| Info              |      Fedora             |                                        Ubuntu |
|:----------------- | -----------------------:| ---------------------------------------------:|
| Hostname          |                fedora27 |                                      ubuntu16 |
| IP                |           192.168.33.11 |                                 192.168.33.12 |
| Box               |         bento/fedora-27 |                            bento/ubuntu-16.04 |
| Virtual CPU cores |                       2 |                                             2 |
| CPU usage cap     |                     50% |                                           50% |
| RAM               |                      2G |                                            2G |
| GUI               |                   false |                                         false |
| X11 forwarding    |                    true |                                          true |
| Shared folder     |                   false |                                         false |
| Provision with    | 'dnf -y install python' | 'apt-get update && apt-get -y install python' |

You can generate a new Vagrant file with the following command or you can just copy and paste the one below.

    vagrant ini bento/fedora-27

**Vagrantfile**

```
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = '2'.freeze
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  #
  # Virtualbox configs
  config.vm.provider :virtualbox do |vb|
    vb.cpus = 2
    vb.gui = false
    vb.customize ['modifyvm', :id, '--memory', '2048']
    vb.customize ['modifyvm', :id, '--cpus', '2']
    vb.customize ['modifyvm', :id, '--cpuexecutioncap', '50']
  end
  config.ssh.forward_x11 = true
  config.vm.synced_folder '', '', disabled: true

  # Fedora box
  config.vm.define 'fedora' do |fedora|
    fedora.vm.hostname = 'fedora27'
    fedora.vm.box = 'bento/fedora-27'
    fedora.vm.provision 'shell', inline: 'dnf -y install python'
    fedora.vm.network 'public_network', ip: '192.168.33.11', bridge: %w[
      en0
      eth0
      enp0s31f6
      wlp4s0
    ]
  end

  # Ubuntu box
  config.vm.define 'ubuntu' do |ubuntu|
    ubuntu.vm.hostname = 'ubuntu16'
    ubuntu.vm.box = 'bento/ubuntu-16.04'
    ubuntu.vm.provision 'shell', inline: 'apt-get update && apt-get -y install python'
    ubuntu.vm.network 'public_network', ip: '192.168.33.12', bridge: %w[
      en0
      eth0
      enp0s31f6
      wlp4s0
    ]
  end
end
```

**Add your network interface to the list, if it's not already there**
**%w[en0 eth0 enp0s31f6 wlp4s0]**

Bring these boxes up with

    vagrant up
    ...
    ...

Once they're finished building, check the status

    vagrant status
    Current machine states:

    fedora                    running (virtualbox)
    ubuntu                    running (virtualbox)

Ansible inventory.ini file

    [vagrant]
    fedora27 ansible_host=192.168.33.11
    ubuntu16 ansible_host=192.168.33.12

    [vagrant:vars]
    ansible_connection=ssh
    ansible_ssh_user=vagrant
    ansible_ssh_pass=vagrant
    ansible_python_interpreter=/usr/bin/python2.7

Reach them at the static IP assigned to the bridged network.

    ansible -i inventory.ini all -m ping

