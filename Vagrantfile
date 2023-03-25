Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"  
  config.vm.hostname = "yoloappvm"
  config.vm.provision "ansible", playbook: "playbook.yml"
  config.vm.network "private_network", ip:"192.168.56.11"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end
end
