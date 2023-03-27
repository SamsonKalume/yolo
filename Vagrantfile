Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"  
  config.vm.hostname = "yoloappvm"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
  config.vm.network "private_network", ip:"192.168.56.10"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end
end
