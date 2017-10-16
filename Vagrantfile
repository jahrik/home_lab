# -*- mode: ruby -*-
# vi: set ft=ruby :

require_relative './files/key_authorization.rb'
VAGRANTFILE_API_VERSION = '2'.freeze
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define 'ubuntu' do |ubuntu|
    ubuntu.vm.box = 'bento/ubuntu-16.04'
    ubuntu.vm.provider 'virtualbox' do |vb|
      vb.gui = false
      vb.memory = '1024'
    end
    ubuntu.vm.hostname = 'ubuntu.dev'
    ubuntu.vm.network 'public_network', bridge: %w(
      wlp4s0
      wlp3s0
      wlan0
    )
    ubuntu.vm.provision 'shell', inline: 'apt-get update'
    authorize_key_for_root config, '~/.ssh/id_rsa.pub'
  end
end
