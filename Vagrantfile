VAGRANTFILE_API_VERSION = "2"

MASTER_NODES = {
  "master" => "192.168.209.130"
}

WORKER_NODES = {
  "worker1" => "192.168.209.131",
  "worker2" => "192.168.209.132"
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "rockylinux/9"

  # Master node(s)
  MASTER_NODES.each do |name, ip|
    config.vm.define name do |node|
      node.vm.hostname = name
      node.vm.network "private_network", ip: ip

      node.vm.provider "vmware_workstation" do |vb|
        vb.memory = 8192
        vb.cpus = 4
      end

      node.vm.provision "shell", path: "script.sh"
    end
  end

  # Worker nodes
  WORKER_NODES.each do |name, ip|
    config.vm.define name do |node|
      node.vm.hostname = name
      node.vm.network "private_network", ip: ip

      node.vm.provider "vmware_workstation" do |vb|
        vb.memory = 2048
        vb.cpus = 2
      end

      node.vm.provision "shell", path: "script.sh"
    end
  end
end
