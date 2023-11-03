# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "generic/debian12"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.50.4"

  config.vm.network :forwarded_port, guest: 80, host: 80, host_ip: "127.0.0.1"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    # Add Docker's official GPG key:
    set -x
    sudo apt-get update
#     sudo sed -i 's/GRUB_TIMEOUT=5/GRUB_TIMEOUT=0/' /etc/default/grub
#     sudo update-grub



#     export DEBIAN_FRONTEND=noninteractive
#     sudo apt-get -yq dist-upgrade



#     sudo DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" dist-upgrade
#     sudo apt-get upgrade -y

#     sudo DEBIAN_FRONTEND=dialog dpkg --configure grub-pc
#     sudo dpkg --configure -a

#     sudo dpkg-reconfigure grub-pc --frontend=text


# ## Install the 'expect' package if it's not already installed
#  sudo apt-get install expect
#
#  # Create an 'expect' script to select a menu entry in GRUB
#  expect << EOF
#  spawn grub-reboot "menu entry title"
#  expect "Booting into"
#  EOF
#
#  # Reboot the system
#  sudo reboot
    #instalez pachetele ca-certificates curl gnupg
    sudo apt-get install ca-certificates curl gnupg
    #creez directorul keystrings in /etc/apt/ cu permisiunile read(4)+ write(2) + execute(1) pt owner, r si e pt group si r si e pt others
    sudo install -m 0755 -d /etc/apt/keyrings
    #ia Docker GNG key-ul de la url il converteste intr-un ASCII ARMORED FORMAT si il salveaza ca /etc/apt/keyrings/docker.gpg
    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    # seteaza permisiunea de read(r) a file-ului la toti utilizatorii(owner, group si others)
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

    #creaza o intrare de repository pt Docker si ii da append in file-ul /etc/apt/sources.list.d/docker.list permitand APT sa foloseasca repository-ul pt package management
    # Add the repository to Apt sources:
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    #instaleaza Docker si pachete docker-related
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

    #adauga userul vagrant la grupul docker; (-a) append la un grup fara sa-l scoata din cele existente; -G docker grupul la care userul este adaugat
    sudo usermod -aG docker vagrant

    sudo mkdir /opt/app
    cd /opt/app
    sudo touch docker-compose.yml
    #schimb ownerul folderului /opt/app si tot ce e in interiorul lui (-R recursiv) ca userul vagrant sa aiba control complet asupra acestuia
    sudo chown -R vagrant:vagrant /opt/app

    echo 'version: "3"
services:
  app:
    image: nginxdemos/hello
    restart: always
    ports:
      - "80:80"' > /opt/app/docker-compose.yml
    echo /opt/app/docker-compose.yml
    cd /opt/app

    #dau run la fisierul yml in modul detached(ruleaza in background)
    sudo docker compose up -d

    sudo apt install ufw -y

    sudo ufw allow ssh

    sudo ufw --force enable

#     sudo ufw deny incoming
#
#     sudo ufw deny outgoing

    sudo ufw allow 443

    sudo ufw allow 80

    sudo ufw allow 22

    sudo ufw reload

    sudo ufw status verbose


    sudo shutdown -r now
  SHELL
end
