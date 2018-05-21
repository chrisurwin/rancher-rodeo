# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
#
require_relative 'vagrant_rancheros_guest_plugin.rb'
$memory_size = 2048
$number_of_nodes = 3
$node_mem = "2048"
$node_cpus = "2"
$vb_gui = false

Vagrant.configure(2) do |config|

  config.vm.define "rancherserver" do |rancherserver|
    rancherserver.vm.hostname = 'rancherserver'
    rancherserver.vm.box= "chrisurwin/RancherOS"
    rancherserver.vm.box_url = "chrisurwin/RancherOS"
    rancherserver.vm.guest = :linux

    rancherserver.vm.network :private_network, ip: "172.22.101.100",
      nic_type: "82545EM"
    rancherserver.vm.provision "shell", inline: "ros engine switch docker-1.12.6"
    rancherserver.vm.provision "shell", inline: "system-docker restart docker"
    rancherserver.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", $memory_size]
      v.customize ["modifyvm", :id, "--name", "rancherserver"]
    end

  end

 # Rancher Nodes
  (1..$number_of_nodes).each do |i|
    hostname = "node-%02d" % i
    config.vm.define hostname do |node|
      node.vm.box   = "chrisurwin/RancherOS"
      node.vm.guest = :linux
      node.vm.provider "virtualbox" do |vb|
        vb.memory = $node_mem
        vb.cpus = $node_cpus
        vb.gui = $vb_gui
        vb.customize ["modifyvm", :id, "--name", hostname]
      end

      ip = "172.22.101.#{i+100}"
      node.vm.network "private_network", ip: ip,  nic_type: "82545EM"
      node.vm.hostname = hostname
      node.vm.provision "shell", inline: "ros engine switch docker-1.12.6"
      node.vm.provision "shell", inline: "system-docker restart docker"
    end
  end
end