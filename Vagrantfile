# Devbox Vagrant file.
#
# Look at default.config.yml for setting configuration parameters on this file.
VAGRANTFILE_API_VERSION = "2"

required_plugins = %w(vagrant-hostsupdater)
plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }

if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Create devbox with vagrant parameters
  config.vm.define "devbox" do |devbox|
    devbox.vm.box = "ubuntu/xenial64"
    devbox.vm.hostname = "dev.box"
    devbox.vm.network "private_network", ip: "10.10.10.10"
    devbox.vm.synced_folder "~/projects", "/home/ubuntu/projects",
      owner: "ubuntu", group: "www-data", mount_options: ["dmode=775,fmode=664"]
  end

  # Virtualbox parameters
  config.vm.provider "virtualbox" do |vb|
    vb.name = "devbox"
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # Workaround for ubuntu/xenial not having /usr/bin/python
  config.vm.provision "shell",
    inline: "if [[ ! -f /usr/bin/python ]]; then sudo ln -s /usr/bin/python3 /usr/bin/python; fi"

  # Provision with Ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end

end
