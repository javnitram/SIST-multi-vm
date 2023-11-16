Vagrant.configure("2") do |config|
  config.vm.box_download_insecure=true
  config.vm.provider :virtualbox do |vb|
    vb.memory = 256
    vb.cpus = 1
    vb.gui = false
  end

  config.vm.define "profe" do |profe|
    profe.vm.hostname = "profe"
    profe.vm.box = "debian/bullseye64"
    profe.vm.network :private_network, ip: "192.168.56.250"
    profe.vm.network :forwarded_port, guest: 22, host: 22000, id: "ssh"
    profe.vm.synced_folder "../", "/UTIL-gestion-aula", disabled: false
    profe.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install --assume-yes pssh sshpass
    SHELL
  end

  N = 3
  (1..N).each do |i|
    config.vm.define "pc0#{i}" do |node|
      node.vm.synced_folder ".", "/vagrant", disabled: true
      node.vm.hostname = "pc0#{i}"
      node.vm.box = "debian/bullseye64"
      node.vm.network :private_network, ip: "192.168.56.#{20+i}"
      node.vm.network :forwarded_port, guest: 22, host: 22000 + i, id: "ssh"
      node.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install --assume-yes psmisc
        echo 'root:vagrant' | chpasswd
        echo 'vagrant:vagrant' | chpasswd
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
        systemctl restart sshd.service
        mkdir -p /home
        mount -t tmpfs -o size=1M tmpfs /home
        mkdir -p /home/hdssd
        mount -t tmpfs -o size=1M tmpfs /home/hdssd
        for i in 'alumnotd' 'alumnotv' 'examen1' 'examen2'
        do
          mkdir -p /home/{.,hdssd}/$i
          dd if=/dev/urandom of=/home/$i/random_file bs=1K count=$((RANDOM % 1024 + 1))
          dd if=/dev/urandom of=/home/hdssd/$i/random_file bs=1K count=$((RANDOM % 1024 + 1))
        done
      SHELL
      # vagrant plugin install vagrant-useradd
      node.useradd.users = ['alumnotd','alumnotv','examen1','examen2']
    end
  end
end