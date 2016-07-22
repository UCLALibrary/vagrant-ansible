# -*- mode: ruby -*-
# vi: set ft=ruby :

### Ansible Provisioner ###
$install_ansible = <<SCRIPT
echo 'Installing ansible...'
yum -y install wget
wget https://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm -O /tmp/epel.rpm
rpm -Uvh /tmp/epel.rpm
yum -y install ansible
SCRIPT
### END ###

### Create ansible user ###
$create_ansible_user = <<SCRIPT
HOME='/home/ansible'
ANSIBLEUSER='ansible'
echo 'Creating ansible user...'
sudo useradd $ANSIBLEUSER -d $HOME -G wheel
sudo mkdir $HOME/.ssh
sudo chmod 700 $HOME/.ssh
sudo chown -R $ANSIBLEUSER:wheel $HOME
echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
SCRIPT

Vagrant.require_version ">=1.8.0"

Vagrant.configure("2") do |config|

	config.vm.provider "virtualbox" do |vbox|
		vbox.memory = 1024
		vbox.cpus = 2
	end

	config.vm.define "ansible" do |ansible|
		ansible.vm.box = "rhel6-dev"
		ansible.vm.hostname = "ansible"
		ansible.vm.network "private_network", type: "dhcp", ip: "192.168.60.3"
		ansible.vm.provision "shell", inline: $install_ansible
		ansible.vm.provision "shell", inline: $create_ansible_user
		ansible.vm.provision "file", source: "./vagrant-ansible-privkey", destination: "/tmp/id_rsa"
		ansible.vm.provision "shell", inline: "mv /tmp/id_rsa /home/ansible/.ssh/id_rsa"
		ansible.vm.provision "file", source: "./vagrant-hosts", destination: "/tmp/hosts"
		ansible.vm.provision "shell", inline: "mv /tmp/hosts /etc/hosts"
		ansible.vm.provision "shell", inline: "sudo chown -R root:root /etc/hosts; sudo chmod 644 /etc/hosts"
		ansible.vm.provision "shell", inline: "sudo chown -R ansible:wheel /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/id_rsa"
	end

	config.vm.define "node1" do |node1|
		node1.vm.box = "rhel6-dev"
		node1.vm.hostname = "node1"
		node1.vm.network "private_network", ip: "192.168.60.22"
		node1.vm.provision "shell", inline: $create_ansible_user
		node1.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		node1.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		node1.vm.provision "shell", inline: "sudo chown -R ansible:wheel /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
end