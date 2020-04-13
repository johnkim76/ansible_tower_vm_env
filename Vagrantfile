# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  public_key = File.read("ssh_keys/ansible_rsa.pub")

  config.vm.define "tower" do |tower|
    tower.vm.hostname = "ansible-tower.example.com"
    tower.vm.network "private_network", ip: "192.168.37.20"
    tower.vm.box = "ansible/tower"
    tower.vm.provision :hosts, :sync_hosts => true
    tower.vm.provision "file", source: "ssh_keys/ansible_rsa", destination: "/home/vagrant/.ssh/id_rsa"
    tower.vm.provision "file", source: "ansible/ansible.cfg", destination: "/home/vagrant/ansible/ansible.cfg"
    tower.vm.provision "file", source: "ansible/hosts", destination: "/home/vagrant/ansible/hosts"
    tower.vm.provision :shell, :inline =>"
      echo 'Copying ansible-vm public SSH Keys to the VM'
      mkdir -p /home/vagrant/.ssh
      chmod 700 /home/vagrant/.ssh
      echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
      chmod -R 600 /home/vagrant/.ssh/authorized_keys
      echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
      echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
      echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
      chmod -R 600 /home/vagrant/.ssh/config
      ", privileged: false
  
    tower.vm.provider "virtualbox" do |vb|
      vb.memory = 4096
      vb.cpus = 4
    end
  end

  # Total number of nodes that Ansible Tower will manage
  N = 3
  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.box = "centos/8"
      machine.vm.hostname = "ansible-node#{machine_id}.example.com"
      machine.vm.network "private_network", ip: "192.168.37.#{20+machine_id}"
      machine.vm.provider "virtualbox" do |vb|
        # Display the VirtualBox GUI when booting the machine
        vb.gui = false
        # Customize the amount of memory on the VM:
        vb.memory = 1024
        vb.cpus = 2
      end
      machine.vm.provision :hosts, :sync_hosts => true
      machine.vm.provision "file", source: "ssh_keys/ansible_rsa", destination: "/home/vagrant/.ssh/id_rsa"
      machine.vm.provision :shell, :inline =>"
        echo 'Copying ansible-vm public SSH Keys to the VM'
        mkdir -p /home/vagrant/.ssh
        chmod 700 /home/vagrant/.ssh
        echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
        chmod -R 600 /home/vagrant/.ssh/authorized_keys
        echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
        echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
        echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
        chmod -R 600 /home/vagrant/.ssh/config
        ", privileged: false
      end
  end
end
