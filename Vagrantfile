# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # workaround the vagrant 1.8.5 bug
  config.ssh.insert_key = false

  config.vm.define 'ansible-vm' do |cfg|
    cfg.vm.box = "bento/oracle-7.3"
    cfg.vm.box_check_update = false
    cfg.vbguest.auto_update = true
    cfg.vm.synced_folder '.', '/vagrant'
    cfg.vm.synced_folder 'files', '/files'
    
    # change memory size
    cfg.vm.provider "virtualbox" do |v|
      v.memory = 8192
      v.cpus = 2
      v.name = "ansible-vm"
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
    end

    cfg.vm.network 'private_network', ip: '192.168.33.101'
    cfg.vm.hostname = "ansible-vm"

    # Oracle port forwarding
    cfg.vm.network "forwarded_port", guest: 1521, host: 1521
    cfg.vm.network "forwarded_port", guest: 7001, host: 7001
    cfg.vm.network "forwarded_port", guest: 8000, host: 8000
    cfg.vm.network "forwarded_port", guest: 8101, host: 8101
    cfg.vm.network "forwarded_port", guest: 5556, host: 5556

    # Provision everything on the first run
    cfg.vm.provision "shell", path: "scripts/install.sh"

    cfg.vm.provision 'ansible' do |ansible|
        ansible.playbook = 'provision.yml'
        ansible.inventory_path = 'roles/ansible-osb/vagrant-inventory.ini'
        ansible.limit = 'ansible-vm'
        ansible.tags = 'install-java,wls-plain-install,osb-create-db-schemas,osb-install-and-init,osb-import-project,oag-install'
        ansible.verbose = 'v'
    end
    cfg.vm.provision 'ansible' do |ansible|
        ansible.playbook = 'provision1213.yml'
        ansible.inventory_path = 'roles/ansible-osb/vagrant-inventory.ini'
        ansible.limit = 'ansible-vm'
        ansible.tags = 'wls-plain-install,oac-plain-install,oac-patch,oac-create-db-schemas,oac-configure,oac-start'
        ansible.verbose = 'v'
    end
    # cfg.vm.provision 'ansible' do |ansible|
    #     ansible.playbook = 'provisiontest.yml'
    #     ansible.inventory_path = 'roles/ansible-osb/vagrant-inventory.ini'
    #     ansible.limit = 'ansible-vm'
    #     ansible.tags = 'osb-import-project'
    #     ansible.verbose = 'v'
    # end

  end
  #if Vagrant.has_plugin?("vagrant-proxyconf")
  #  config.proxy.http     = "http://proxy.who.int:3128"
  #  config.proxy.https    = "http://proxy.who.int:3128"
  #  config.proxy.no_proxy = "localhost,127.0.0.1"
  #end
end
