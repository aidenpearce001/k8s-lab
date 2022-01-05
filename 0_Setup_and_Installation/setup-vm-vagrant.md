## 1. Requirements
### 1.1 Hardware configuration:
- Memory: 8 GiB or more
- CPUs: At least 4 vCPUs on the control plane machine.

### 1.2 Softwares:
- Down Virtualbox by this link: [Virtualbox Pages](https://www.virtualbox.org)
- Down Vagrant by this link: [Vagrant Pages](https://www.vagrantup.com/downloads)
- kubectl
- lens(optional)

## 2. Create a 3-nodes cluster with Vagrant
Create Vagrantfile
```
VM_ID = "kube"
NUM_WORKER_NODES = 2

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = true

# Single Control-Plane node  
  config.vm.define "#{VM_ID}-master" do |node|
    node.vm.provider "virtualbox" do |vb|
      vb.name = "#{VM_ID}-master"
      vb.memory = 8192
      vb.cpus = 2
    end
    node.vm.hostname = "#{VM_ID}-master"
    node.vm.network :private_network, ip: "192.168.11.10"

  end

# Worker nodes
  (1..NUM_WORKER_NODES).each do |i|
    config.vm.define "#{VM_ID}-worker-#{i}" do |node|
      node.vm.provider "virtualbox" do |vb|
        vb.name = "#{VM_ID}-worker-#{i}"
        vb.memory = 4096
        vb.cpus = 2
      end
      node.vm.hostname = "#{VM_ID}-worker-#{i}"
      node.vm.network :private_network, ip: "192.168.11.1#{i}"

    end
  end
end
```

Spin up 3 linux node using Vagrant

```
vagrant up
```

Access to server
```
vagrant ssh <vagrant_machine_name>
```

Shutdown all virtual machines
```
vagrant halt
```

Cleanup
```
vagrant destroy
```
