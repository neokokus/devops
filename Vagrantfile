Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-24.04"
  config.vm.box_architecture = "amd64"

  config.vm.define "harbor" do |harbor|
    harbor.vm.hostname = "harbor.local"
    harbor.vm.network "private_network", ip: "192.168.56.11"
    harbor.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
      vb.name = "harbor-vm"
    end
  end

  config.vm.define "docker" do |docker|
    docker.vm.hostname = "docker.local"
    docker.vm.network "private_network", ip: "192.168.56.12"
    docker.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 1
      vb.name = "docker-vm"
    end
  end

  config.vm.define "storage" do |storage|
    storage.vm.hostname = "storage.local"
    storage.vm.network "private_network", ip: "192.168.56.10"
    
    storage.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
      vb.name = "storage-vm"
      
      vb.customize ['createhd', '--filename', 'storage_disk.vdi', '--size', 40 * 1024]
      
      # Подключаем диск к виртуалке как SATA-контроллер (второй диск)
      vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', 'storage_disk.vdi']
    end
  end
end