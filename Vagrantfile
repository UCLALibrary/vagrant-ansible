# -*- mode: ruby -*-
# vi: set ft=ruby :

### Ansible Provisioner ###
$install_ansible = <<SCRIPT
echo 'Installing ansible...'
yum -y install wget
wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm -O /tmp/epel.rpm
rpm -Uvh /tmp/epel.rpm
yum -y install ansible
SCRIPT
### END ###

Vagrant.require_version ">=1.8.0"

Vagrant.configure("2") do |config|

	config.vm.provider "virtualbox" do |vbox|
		vbox.memory = 1024
		vbox.cpus = 2
	end

	config.vm.network "private_network", type: "dhcp"

	config.vm.define "ansible" do |ansible|
		ansible.vm.box = "rhel6-dev"
		config.vm.hostname = "ansible"
		config.vm.provision "shell", inline: $install_ansible
	end

	config.vm.define "ansibledev" do |ansibledev|
		ansibledev.vm.box = "rhel6-dev"
		config.vm.hostname = "ansibledev"
	end
end