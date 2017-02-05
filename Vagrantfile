# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Language pack to install in Ubuntu
  language_pack = "de"

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  config.vm.provider "virtualbox" do |vb|
    # Don't display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "1024"

    # Fix problems with VirtualBox's wonky NAT
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  # Getting rid of the annoying "stdin: no tty" error (See: http://foo-o-rama.com/vagrant--stdin-is-not-a-tty--fix.html)
  config.vm.provision "fix-no-tty", type: "shell" do |shell|
    shell.privileged = false
    shell.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  end

  # Language stuff
  config.vm.provision "shell", inline: <<-SHELL
    echo -e "de_DE.UTF-8\tUTF-8\nde_DE@euro\tISO-8859-15\nde_DE\tISO-8859-1\n" > /var/lib/locales/supported.d/de
    locale-gen
    echo 'LANG=de_DE.utf8
    LC_CTYPE=de_DE.utf8
    LC_COLLATE=de_DE.utf8
    LC_TIME=de_DE.utf8
    LC_NUMERIC=de_DE.utf8
    LC_MONETARY=de_DE.utf8
    LC_MESSAGES=en_US.utf8
    LC_PAPER=de_DE.utf8
    LC_NAME=de_DE.utf8
    LC_ADDRESS=de_DE.utf8
    LC_TELEPHONE=de_DE.utf8
    LC_MEASUREMENT=de_DE.utf8
    LC_IDENTIFICATION=en_US.utf8' \
    > /etc/default/locale
    apt-get -y install language-pack-#{language_pack}
  SHELL

  # Installing Ansible
  config.vm.provision "shell", inline: <<-SHELL
    apt-add-repository ppa:ansible/ansible -y
    apt-get update -y && apt-get dist-upgrade -y
    apt-get install -y ansible
    cp /vagrant/misc/ansible.cfg /etc/ansible/ansible.cfg
    cp /vagrant/misc/id_rsa* /home/ubuntu/.ssh/
    chown -R ubuntu:ubuntu /home/ubuntu/.ssh
    cat /vagrant/misc/id_rsa.pub >> /home/ubuntu/.ssh/authorized_keys
    echo "ubuntu ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
  SHELL

  # Setting up hosts file - TODO: Find automatic/better way
  config.vm.provision "shell", inline: <<-SHELL
    echo "192.168.100.1 master" >> /etc/hosts
    echo "192.168.100.2 node" >> /etc/hosts
  SHELL

  # Configure the two VMs required for a standard ansible setup
  ["master", "node"].each_with_index do |node_name, index|
    config.vm.define node_name do |node|
      node.vm.hostname = node_name

      # Configure the private network
      node.vm.network "private_network", ip: "192.168.100.#{index + 1}" #IPs should not start with 0, as that's the Net-ID
    end
  end
end
