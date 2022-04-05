# -*- mode: ruby -*-
# vi: set ft=ruby :

MEMORY = 4096
CPU_COUNT = 2

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  # config.disksize.size = '20GB'
  config.ssh.username ="vagrant"
  config.ssh.password ="vagrant"
  config.ssh.insert_key = false
  config.vm.network "private_network", ip: "196.168.33.2"

  config.vm.network "forwarded_port", guest: 22, host: 4085
  config.vm.network "forwarded_port", guest: 8080, host: 4086
  config.vm.network "forwarded_port", guest: 9000, host: 4087
  config.vm.network "forwarded_port", guest: 3306, host: 4088

  config.vm.synced_folder  ".", "/vagrant", disabled: false

  config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", MEMORY.to_s]
      vb.customize ["modifyvm", :id, "--cpus", CPU_COUNT.to_s]
  end
end
