Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.provider :virtualbox do |vb|
    vb.memory = 256
    vb.cpus = 1
    vb.gui = false
  end

  #Disabling the default /vagrant share
  config.vm.synced_folder ".", "/vagrant", disabled: true
  N = 3
  (1..N).each do |i|
    config.vm.define "pc0#{i}" do |node|
      node.vm.hostname = "pc0#{i}"
      node.vm.box = "debian/bullseye64"
      node.vm.network :private_network, ip: "192.168.56.#{20+i}"
      node.vm.network :forwarded_port, guest: 22, host: 22000 + i, id: "ssh"
      node.vm.provision "shell", inline: <<-SHELL
        echo 'root:vagrant' | chpasswd
        echo 'vagrant:vagrant' | chpasswd
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
        systemctl restart sshd.service
      SHELL
    end
  end
end