IMAGE_NAME = "ubuntu/bionic64"
BOX_VERSION = "20190612.0.0"

# IMAGE_NAME = "ubuntu/xenial64"
# BOX_VERSION = "20200108.0.0"

N = 1

Vagrant.configure("2") do |config|
  # config.ssh.insert_key = false

  config.vm.define "k8s-master" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.box_version = BOX_VERSION
    master.disksize.size = "30GB"
    master.vm.network "private_network", ip: "192.168.50.10", netmask: "255.255.255.0"
    master.vm.hostname = "k8s-master"
    master.vm.network "forwarded_port", guest: 30001, host: 8000
    master.vm.network "forwarded_port", guest: 30002, host: 1883
    master.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "kubernetes-setup/master-playbook.yml"
      ansible.extra_vars = {
        node_ip: "192.168.50.10",
      }
    end
  end

  (1..N).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.box_version = BOX_VERSION
      node.disksize.size = "50GB"
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}", netmask: "255.255.255.0"
      node.vm.hostname = "node-#{i}"
      node.vm.provider "virtualbox" do |v|
        v.memory = 6144
        v.cpus = 4
      end
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "kubernetes-setup/node-playbook.yml"
        ansible.extra_vars = {
          node_ip: "192.168.50.#{i + 10}",
        }
      end
    end
  end
end
