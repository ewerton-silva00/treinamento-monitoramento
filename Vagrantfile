# -*- mode: ruby -*-
# vi: set ft=ruby :

servers = {
  'monitoring' => { 'ip' => '10.10.10.10', 'memory' => '4096', 'cpus' => '2', 'disk' => '/tmp/monitoring.vdi' }
}

Vagrant.configure("2") do |config|

    servers.each do |name, conf|
      config.vm.define "#{name}" do |cfg|
        cfg.vm.box = "ewerton_silva00/centos-7-x86_64-minimal-2003"
        cfg.vm.box_version = "20200906"
        cfg.vm.box_check_update = true
        cfg.vm.hostname = "#{name}.meudominio.local"
        cfg.vm.network "private_network", ip: "#{conf['ip']}"
        cfg.vm.provision :hosts, :sync_hosts => true
  
        cfg.vbguest.auto_update = false
  
        cfg.vm.provider "virtualbox" do |vb|
          vb.name = "#{name}"
          vb.gui = false
          vb.cpus = "#{conf['cpus']}"
          vb.memory = "#{conf['memory']}"
          # --- Add second disk with 10GB.
          vb.customize ['createhd', '--filename', "#{conf['disk']}", '--size', 10 * 1024]
          vb.customize ['storageattach', :id, '--storagectl', 'SATA', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', "#{conf['disk']}"]
        end
  
        cfg.vm.provision "shell", inline: <<-SHELL
          setenforce 0
          yum clean all && yum install ansible -y
          export ANSIBLE_CONFIG="/vagrant/ansible/ansible.cfg"
          ansible-galaxy collection install --collections-path /vagrant/ansible/collections community.general
          ansible-galaxy collection install --collections-path /vagrant/ansible/collections ansible.posix
        SHELL

        cfg.vm.provision 'ansible_local' do |ansible|
          ansible.playbook = "./ansible/playbook.yml"
        end
      end
    end
end
