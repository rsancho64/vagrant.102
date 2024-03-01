# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = 2
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.provider :virtualbox do |vb|
 
    vb.name = "myvm"
    vb.memory = 2048
    vb.cpus = 4

    vb.gui = true

    # vb.linked_clone = true

    file_to_disk = 'tmp/disk.vdi'
    unless File.exist?(file_to_disk)
      vb.customize ['createhd',
        '--filename', file_to_disk,
        '--size', 500 * 1024]
      vb.customize ['storageattach', :id,
        '--storagectl', 'SATAController',
        '--port', 1,
        '--device', 0,
        '--type', 'hdd',
        '--medium', file_to_disk]
    end

  end
end
