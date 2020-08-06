# -*- mode: ruby -*-
# vi: set ft=ruby :

IMAGE_NAME = "generic/ubuntu2004"
N = 2

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.provider :libvirt do |libvirt|
    libvirt.memory = 1024
    libvirt.cpus = 2
    libvirt.qemu_use_session = false
  end

  config.vm.define "k8s-master" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.network :private_network,
      :ip => "192.168.100.10",
      :libvirt__network_name => "kubernetes",
      :libvirt__network_address => "192.168.100.0",
      :type => "dhcp",
      :libvirt__domain_name => "kube.local"
    master.vm.hostname = "k8s-master"
    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible-roles/master-playbook.yml"
      ansible.extra_vars = {
          node_ip: "192.168.100.10",
      }
    end
  end

  (1..N).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.network :private_network,
        :ip => "192.168.100.#{i + 10}",
        :libvirt__network_name => "kubernetes",
        :libvirt__network_address => "192.168.100.0",
        :type => "dhcp",
        :libvirt__domain_name => "kube.local"
      node.vm.hostname = "node-#{i}"
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "kubernetes-setup/node-playbook.yml"
        ansible.extra_vars = {
            node_ip: "192.168.100.#{i + 10}",
        }
      end
    end
  end

end
