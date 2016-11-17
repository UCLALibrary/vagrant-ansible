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
ANSIBLEGROUP='ansible'
echo 'Creating ansible user...'
sudo groupadd $ANSIBLEGROUP -g 655
sudo useradd $ANSIBLEUSER -u 655 -d $HOME -g $ANSIBLEGROUP -G wheel
sudo mkdir $HOME/.ssh
sudo chmod 700 $HOME/.ssh
sudo chown -R $ANSIBLEUSER:$ANSIBLEGROUP $HOME
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
		ansible.vm.network "private_network", ip: "192.168.60.10"
		ansible.vm.provision "shell", inline: $install_ansible
		ansible.vm.provision "shell", inline: $create_ansible_user
		ansible.vm.provision "file", source: "./vagrant-ansible-privkey", destination: "/tmp/id_rsa"
		ansible.vm.provision "shell", inline: "mv /tmp/id_rsa /home/ansible/.ssh/id_rsa"
		ansible.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/id_rsa"
		ansible.vm.provision "file", source: "./vagrant-ansible-hosts", destination: "/tmp/hosts"
		ansible.vm.provision "shell", inline: "mv /tmp/hosts /etc/hosts"
		ansible.vm.provision "shell", inline: "sudo chown -R root:root /etc/hosts; sudo chmod 644 /etc/hosts"
		ansible.vm.provision "file", source: "./vagrant-known_hosts", destination: "/tmp/known_hosts"
		ansible.vm.provision "shell", inline: "mv /tmp/known_hosts /home/ansible/.ssh/known_hosts"
		ansible.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh/known_hosts; sudo chmod 644 /home/ansible/.ssh/known_hosts"
		ansible.vm.provision "file", source: "./vagrant-ansible_key", destination: "/tmp/ansible_key"
		ansible.vm.provision "shell", inline: "mv /tmp/ansible_key /home/ansible/ansible_key"
		ansible.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/ansible_key; sudo chmod 600 /home/ansible/ansible_key"
		ansible.vm.synced_folder "ansibleroot/", "/etc/ansible", mount_options: ["uid=655,gid=655"]
	end

	config.vm.define "node1" do |node1|
		node1.vm.box = "rhel6-dev"
		node1.vm.hostname = "node1"
		node1.vm.network "private_network", ip: "192.168.60.22"
    		node1.vm.network "forwarded_port", guest: 22, host: 2250, protocol: "tcp"
		node1.vm.provision "shell", inline: $create_ansible_user
		node1.vm.provision "file", source: "./vagrant-node-hosts", destination: "/tmp/hosts"
		node1.vm.provision "shell", inline: "mv /tmp/hosts /etc/hosts"
		node1.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		node1.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		node1.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
	config.vm.define "node2" do |node2|
		node2.vm.box = "rhel6-dev"
		node2.vm.hostname = "node2"
		node2.vm.network "private_network", ip: "192.168.60.23"
    		node2.vm.network :forwarded_port, guest: 22, host: 2251, protocol: "tcp"
		node2.vm.provision "shell", inline: $create_ansible_user
		node2.vm.provision "file", source: "./vagrant-node-hosts", destination: "/tmp/hosts"
		node2.vm.provision "shell", inline: "mv /tmp/hosts /etc/hosts"
		node2.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		node2.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		node2.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
	config.vm.define "node3" do |node3|
		node3.vm.box = "rhel6-dev"
		node3.vm.hostname = "node3"
		node3.vm.network "private_network", ip: "192.168.60.19"
    		node3.vm.network :forwarded_port, guest: 22, host: 2252, protocol: "tcp"
		node3.vm.provision "shell", inline: $create_ansible_user
		node3.vm.provision "file", source: "./vagrant-node-hosts", destination: "/tmp/hosts"
		node3.vm.provision "shell", inline: "mv /tmp/hosts /etc/hosts"
		node3.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		node3.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		node3.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
	config.vm.define "haproxylb0" do |haproxylb0|
		haproxylb0.vm.box = "rhel6-dev"
		haproxylb0.vm.hostname = "haproxylb0"
		haproxylb0.vm.network "private_network", ip: "192.168.60.24"
    haproxylb0.vm.network :forwarded_port, guest: 22, host: 2253, protocol: "tcp"
		haproxylb0.vm.provision "shell", inline: $create_ansible_user
		haproxylb0.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		haproxylb0.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		haproxylb0.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
	config.vm.define "haproxylb1" do |haproxylb1|
		haproxylb1.vm.box = "rhel6-dev"
		haproxylb1.vm.hostname = "haproxylb1"
		haproxylb1.vm.network "private_network", ip: "192.168.60.25"
    haproxylb1.vm.network :forwarded_port, guest: 22, host: 2254, protocol: "tcp"
		haproxylb1.vm.provision "shell", inline: $create_ansible_user
		haproxylb1.vm.provision "file", source: "./vagrant-ansible-authorized-keys", destination: "/tmp/authorized_keys"
		haproxylb1.vm.provision "shell", inline: "mv /tmp/authorized_keys /home/ansible/.ssh/authorized_keys"
		haproxylb1.vm.provision "shell", inline: "sudo chown -R ansible:ansible /home/ansible/.ssh; sudo chmod 600 /home/ansible/.ssh/authorized_keys"
	end
end