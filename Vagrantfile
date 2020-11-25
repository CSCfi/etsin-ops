# -*- mode: ruby -*-
# vi: set ft=ruby :

# This script is to be run by saying 'vagrant up' in this folder. This script
# should be run only when creating a local development environment.

# Pre-provisioner shell script installs Ansible into the guest and continues
# to provision rest of the system in the guest. Works also on Windows.
$script = <<SCRIPT
set -e
if [ ! -f /vagrant_bootstrap_done.info ]; then
  sudo yum -y update
  sudo yum -y install epel-release 
  sudo yum -y install libffi-devel
  sudo yum -y install openssl-devel
  sudo yum -y install git 
  sudo yum -y install python3-3.6.8 
  sudo yum -y install python3-devel-3.6.8
  pip3 install ansible
  su --login -c 'cd /etsin/ansible && source install_requirements.sh && ansible-playbook site_provision.yml' vagrant
  sudo touch /vagrant_bootstrap_done.info
fi
SCRIPT


# required_plugins = %w( vagrant-vbguest )
# required_plugins.each do |plugin|
#    exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(" ")}" unless Vagrant.has_plugin? plugin || ARGV[0] == 'plugin'
# end

Vagrant.configure("2") do |config|
  config.vm.define "etsin_local_dev_env" do |server|
    server.vm.box = "centos/7"
    server.vm.network :private_network, ip: "30.30.30.30"

    # Basic VM synced folder mount
    server.vm.synced_folder "./etsin_finder", "/etsin/etsin_finder", :mount_options => ["dmode=777,fmode=777"], create: true
    server.vm.synced_folder "./etsin_finder_search", "/etsin/etsin_finder_search", :mount_options => ["dmode=777,fmode=777"], create: true
    server.vm.synced_folder "./ansible", "/etsin/ansible", :mount_options => ["dmode=775,fmode=775"]

    server.vm.provision "shell", inline: $script
    server.vm.provision "shell", run: "always", inline: "sudo systemctl restart rabbitmq-consumer"

    server.vm.provider "virtualbox" do |vbox|
        vbox.name = "etsin_local_development"
        vbox.gui = false
        vbox.memory = 4096
        vbox.customize ["modifyvm", :id, "--nictype1", "virtio"]
    end
  end
end
