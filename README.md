### learning-ansible

This repository is a starting point for learning Ansible.
It configures two virtual machines with Ansible and a private network set up.

OS on both VMs: Ubuntu 16.04 LTS "Xenial".
Language pack: German (de).

You can choose another language pack by changign the variable in the Vagrantfile.

The following VMs are configures by default:
master => The place where all the playbooks are ran from
node => The server playbooks are applied to - It represents your standard server.

You can simply create more VMs by slightly altering the Vagrantfile.

Please fork this project to your own GitHub account or download the ZIP in order to play with this setup ;)
