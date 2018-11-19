# Devbox Vagrant file.
require 'yaml'

# Look at default.config.yml for setting configuration parameters on this file.
VAGRANTFILE_API_VERSION = "2"

# Load configuration parameters from config file.
config_yml = YAML.load_file("config.yml")

# Project domains
project_domains = []

config_yml['projects'].each do |project|
  project.each do |key, value|
    if key == 'domain' and value != ""
      project_domains.push(value)
    end
  end
end

# Install if required and load plugins.
required_plugins = %w(vagrant-hostsupdater vagrant-bindfs vagrant-vbguest)
plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }

if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

# Start configuring Vagrant
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Define VM parameters
  config.vm.define config_yml['vm_name'] do |devbox|
    
    # Operating system
    devbox.vm.box = "ubuntu/bionic64"

    # Network
    devbox.vm.network "private_network", ip: config_yml['vm_ip']
    devbox.vm.hostname = config_yml['vm_hostname']
    devbox.hostsupdater.aliases = project_domains

    # Folders
    devbox.vm.synced_folder config_yml['host_path'], config_yml['tmp_path'], type: config_yml['vm_network_type']

    devbox.bindfs.bind_folder config_yml['tmp_path'], config_yml['guest_path'],
      perms: config_yml['vm_folder_perm'],
      force_user: config_yml['vm_user'],
      force_group: config_yml['vm_group'],
      create_as_user: true
  end

  # Virtualbox parameters
  config.vm.provider "virtualbox" do |vb|
    vb.name = config_yml['vm_name']
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--memory", config_yml['vm_ram']]
  end

  # Workaround for ubuntu/xenial not having /usr/bin/python
  config.vm.provision "shell",
    inline: "if [[ ! -f /usr/bin/python ]]; then sudo ln -s /usr/bin/python3 /usr/bin/python; fi"

  # Provision with Ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end

end
