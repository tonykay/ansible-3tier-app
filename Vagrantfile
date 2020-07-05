# -*- mode: ruby -*-
# vi: set ft=ruby :

NODES=4
# Overwrite host locale in ssh session
ENV["LC_ALL"] = "en_US.UTF-8"

# Control host settings

control_hostname = "control.ntier"
control_ip = "192.168.8.2"
#control_box = "geerlingguy/centos7"
control_box = "generic/rhel7"

control_memory = 2048

# Target node settings

#node_box = "geerlingguy/centos7"
node_box = "generic/rhel7"
node_memory = 1024

# Your private ssh key

Vagrant.configure("2") do |cluster|

  # Avoid using the Virtualbox guest additions

  if Vagrant.has_plugin?("vagrant-vbguest")
    cluster.vbguest.auto_update = false
  end
  
  cluster.vm.define "control.ntier" do |control_node|
    control_node.vm.box = control_box
    control_node.vm.hostname = "%s" % control_hostname
    control_node.vm.network "private_network", ip: control_ip

    # setup ssh keys, use own instead of allowing vagrant to generate one

    control_node.ssh.insert_key = false
    #control_node.ssh.private_key_path = ["~/.vagrant.d/insecure_private_key",
    #                                  "~/.ssh/id_rsa"]

    control_node.vm.synced_folder ".", "/vagrant"
    control_node.vm.provider :virtualbox do |vb|
      vb.name = "%s" % control_hostname
      vb.memory = control_memory
    end
    control_node.vm.provision "ansible" do |ansible|
      ansible.playbook = "utilities/post-vagrant-setup.yml"
    end

  end
# hosts to run ansible-core
  
  (1..NODES).each do |i|
    cluster.vm.define "node-0#{i}.ntier" do |node|
      node.vm.box = node_box

      node.vm.hostname = "node-0#{i}.ntier"
      node.vm.network :private_network, ip: "192.168.8.#{i+5}"
    
      node.ssh.insert_key = false
      node.ssh.private_key_path = ["~/.vagrant.d/insecure_private_key",
                                      "~/.ssh/id_rsa"]

      node.vm.provider :virtualbox do |vb|
        vb.name = "node-0#{i}.ntier"
        vb.memory = node_memory
      end
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "utilities/post-vagrant-setup.yml"
      end
    end  
  end
end

