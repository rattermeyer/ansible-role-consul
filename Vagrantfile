# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
    machines = {
                   :server1 => {:ip => '192.168.56.11', :box => 'geerlingguy/ubuntu1604', :mem => '256', :cpu => 1 },
                   :server2 => {:ip => '192.168.56.12', :box => 'geerlingguy/ubuntu1604', :mem => '256', :cpu => 1 },
                   :server3 => {:ip => '192.168.56.13', :box => 'geerlingguy/ubuntu1604', :mem => '256', :cpu => 1 },
                   :agent   => {:ip => '192.168.56.14', :box => 'geerlingguy/ubuntu1604', :mem => '256', :cpu => 1 },
                 }

      machines.each do |machine_name, machine_details|
       config.vm.define machine_name do |machine_config|
         machine_ip = machine_details[:ip]
         machine_config.vm.box = machine_details[:box]
         machine_config.vm.network :private_network, ip: machine_ip
         machine_config.vm.hostname = machine_name.to_s
         machine_config.vm.provider :virtualbox do |vb|
           reserved_mem = machine_details[:mem] || default_mem
           reserved_cpu = machine_details[:cpu] || default_cpu
           vb.name = machine_name.to_s
           vb.customize ["modifyvm", :id, "--groups", "/Consul"]
           vb.customize ["modifyvm", :id, "--memory", reserved_mem]
           vb.customize ["modifyvm", :id, "--cpus", reserved_cpu]
           vb.gui = false
         end #vb
         machine_config.vm.provision "shell", inline: "apt-get update && apt-get install -y python2.7 python-pip"
       end # machine_config
      end # machines

      config.vm.define "consul-controller", autostart: true do |vm_config|
        vm_config.vm.box = "ubuntu/xenial64"
        vm_config.vm.hostname = "controller"
        vm_config.vm.network :private_network, ip: "192.168.56.10"
        vm_config.vm.provider :virtualbox do |vb|
          vb.name = "consul-controller"
          vb.customize ["modifyvm", :id, "--groups", "/Consul"]
          vb.customize ["modifyvm", :id, "--memory", 256]
          vb.customize ["modifyvm", :id, "--cpus", 1]
          vb.gui = false
        end
        vm_config.vm.provision "shell", inline: "apt-add-repository -y ppa:ansible/ansible && apt-get update && apt-get install -y --force-yes ansible"
        vm_config.vm.provision "shell", inline: "mkdir -p /home/ubuntu/roles && rsync -a /vagrant/ /home/ubuntu/roles/ansible-role-consul"
        vm_config.vm.provision "shell", inline: "cd /home/ubuntu/roles/ansible-role-consul/tests && ansible-playbook -b -v -i inventory test.yml"
      end
end
