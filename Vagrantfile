# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify Vagrant version, Vagrant API version, and Vagrant clone location
Vagrant.require_version '>= 1.6.0'
VAGRANTFILE_API_VERSION = '2'
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

# Require 'yaml' module
require 'yaml'

# Read details of containers to be created from YAML file
# Be sure to edit 'containers.yml' to provide container details
containers = YAML.load_file(File.join(File.dirname(__FILE__), 'containers.yml'))

Vagrant.configure(2) do |config|
  # Iterate through the entries in the YAML file
  containers.each do |container|
    config.vm.define container['name'] do |cntnr|
      cntnr.vm.network "private_network", type: "dhcp"
      cntnr.vm.provider "docker" do |docker, override|
        # docker doesnt use boxes
        override.vm.box = nil
    
        # this is where your Dockerfile lives
        docker.build_dir = "."
    
        # Make sure it sets up ssh with the Dockerfile
        # Vagrant is pretty dependent on ssh
        override.ssh.insert_key = true
        docker.has_ssh = true
        docker.name = container['name']
    
        # Configure Docker to allow access to more resources
        docker.privileged = true
        #docker.ports = container['ports']
      end
    
      cntnr.vm.provision "ansible_local" do |ansible|
        ansible.install_mode = "pip3"
        ansible.version = "latest"
        ansible.verbose = "vv"
        ansible.playbook = "provisioning/playbook.yaml"
        ansible.extra_vars = "provisioning/extra-vars.yaml"
      end
    end # config.vm.define
  end # containers.each
end # Vagrant.configure
