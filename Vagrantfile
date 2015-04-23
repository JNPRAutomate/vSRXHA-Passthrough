# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

require "vagrant-host-shell"
require "vagrant-junos"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "client", primary: true do |ndo|
    ndo.vm.box = "juniper/netdevops-ubuntu1404-headless"
    ndo.vm.hostname = "Client"
    ndo.vm.network "private_network",
                   ip: "172.16.0.10",
                   virtualbox__intnet: "NetDevOps-Client"
    ndo.vm.synced_folder "", "/vagrant"
    ndo.ssh.password = "vagrant"

    #Virtualbox configuration
    ndo.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", "1024"]
    end

    #VMware configuration
    ndo.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "1024"
      v.vmx["vhv.enable"] = "TRUE"
      v.vmx["ethernet1.generatedAddress"] = nil
      v.vmx["ethernet1.connectionType"] = "custom"
      v.vmx["ethernet1.present"] = "TRUE"
      v.vmx["ethernet1.vnet"] = "vmnet0"
    end

    ndo.vm.provision "shell" do |s|
      # this script provisions the ndo box for you
      s.path = "scripts/ndo-setup-client.sh"
    end
  end

  config.vm.define "server", primary: true do |ndo|
    ndo.vm.box = "juniper/netdevops-ubuntu1404-headless"
    ndo.vm.hostname = "Server"
    ndo.vm.network "private_network",
                   ip: "192.168.0.10",
                   virtualbox__intnet: "NetDevOps-Server"
    ndo.vm.synced_folder "", "/vagrant"
    ndo.ssh.password = "vagrant"

    #Virtualbox Configuration
    ndo.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", "1024"]
    end

    #VMware configuration
    ndo.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "1024"
      v.vmx["ethernet1.generatedAddress"] = nil
      v.vmx["ethernet1.connectionType"] = "custom"
      v.vmx["ethernet1.present"] = "TRUE"
      v.vmx["ethernet1.vnet"] = "vmnet1"
    end

    ndo.vm.provision "shell" do |s|
      # this script provisions the ndo box for you
      s.path = "scripts/ndo-setup-server.sh"
    end
  end

  config.vm.define "srx-node0" do |srx|
    srx.vm.box = "juniper/ffp-12.1X47-D20.7"
    srx.vm.hostname = "vSRX"
    srx.vm.network "private_network",
                   ip: "172.16.0.1",
                   nic_type: 'virtio',
                   virtualbox__intnet: "NetDevOps-Client"
    srx.vm.network "private_network",
                   ip: "192.168.0.1",
                   nic_type: 'virtio',
                   virtualbox__intnet: "NetDevOps-Server"

    srx.vm.synced_folder "", "/vagrant", disabled: true

    srx.vm.provider "virtualbox" do |v|
      # increase RAM to support AppFW and IPS
      # comment out to make it run at 2GB
      v.customize ["modifyvm", :id, "--memory", "3072"]
      v.check_guest_additions = false
    end

    #VMware configuration
    srx.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "3072"
      v.vmx["ethernet1.generatedAddress"] = nil
      v.vmx["ethernet1.connectionType"] = "custom"
      v.vmx["ethernet1.present"] = "TRUE"
      v.vmx["ethernet1.vnet"] = "vmnet0"
      v.vmx["ethernet2.generatedAddress"] = nil
      v.vmx["ethernet2.connectionType"] = "custom"
      v.vmx["ethernet2.present"] = "TRUE"
      v.vmx["ethernet2.vnet"] = "vmnet1"
    end

    #
    srx.vm.provision "file", source: "vSRX-configs/node0-provision.cfg", destination: "/cf/root/node0-provision.cfg"
    srx.vm.provision "file", source: "vSRX-configs/nopolicy.cfg", destination: "/cf/root/nopolicy.cfg"
    srx.vm.provision :host_shell do |host_shell|
      # provides the inital configuration
      host_shell.inline = 'vagrant ssh srx-node0 -c "/usr/sbin/cli -f /cf/root/inital.cfg"'
    end
  end
end
