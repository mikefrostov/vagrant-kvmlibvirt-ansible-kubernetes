IMAGE_NAME = "generic/ubuntu1604"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = 'false'
    config.vm.provider :libvirt do |libvirt|
        libvirt.memory = 8192
        libvirt.cpus = 4
        libvirt.cputopology :sockets => '2', :cores => '2', :threads => '1'
        libvirt.storage :file, :size => '50G'
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "public_network", bridge: "br0", dev: "br0", type: "bridge", mode: "bridge", ip: "192.168.1.170"
        master.vm.hostname = "k8s-master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.1.170",
            }
        end
    end
end

Vagrant.configure("2") do |config|
    config.ssh.insert_key = 'false'
    config.vm.provider :libvirt do |v|
        v.memory = 4096
        v.cpus = 2
        v.storage :file, :size => '50G'
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "public_network", bridge: "br0", dev: "br0", type: "bridge", mode: "bridge", ip: "192.168.1.#{i + 170}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.1.#{i + 170}",
                }
            end
        end
    end
end
