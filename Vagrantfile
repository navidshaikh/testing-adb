# -*- mode: ruby -*-
# vi: set ft=ruby :

#Vagrant file for libvirt/kvm and virtualbox provider

Vagrant.configure(2) do |config|
  config.vm.box = "atomicapp/dev"

  config.vm.provider "libvirt" do |libvirt, override|
    libvirt.driver = "kvm"
    libvirt.memory = 1024
    libvirt.cpus = 2
  end

  config.vm.provider "virtualbox" do |vbox, override|
    vbox.memory = 1024
    vbox.cpus = 2

    # Enable use of more than one virtual CPU in a virtual machine.
    vbox.customize ["modifyvm", :id, "--ioapic", "on"]
  end

end
