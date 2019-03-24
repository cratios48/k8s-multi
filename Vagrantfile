# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |v|
    v.cpus = 2
    v.memory = 2048
  end

  (1..2).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.hostname = "k8s-node-#{i}"
      node.vm.network "private_network", ip: "10.10.10.#{(1 + i) * 10}"
    end
  end

  config.vm.define "master" do |master|
    master.vm.hostname = "k8s-master"
    master.vm.network "private_network", ip: "10.10.10.10"

    # To provide nodes key file
    master.vm.provision "file",
      source: ".vagrant",
      destination: "/vagrant/.vagrant"

    # Prevent ansible key file permission error
    master.vm.provision "shell",
      inline: "chmod 400 /vagrant/.vagrant/machines/node*/virtualbox/private_key"

    master.vm.provision "ansible_local" do |ans|
      ans.playbook          = "master.yml"
      #ans.verbose           = true
      ans.install           = true
      ans.install_mode      = "pip"
      ans.version           = "latest"  
      ans.inventory_path    = "inventory.yml"
      ans.limit             = "all"
    end
  end

end