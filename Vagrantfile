# -*- mode: ruby -*-
# vi: set ft=ruby :
# vagrant plugins required:
#

# Inspiratins for this file - playa-mesos, vagrant-mesos projects.

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = '2'

PROJECT_ROOT = File.dirname(__FILE__)
CONFIG = File.expand_path(File.join(File.dirname(__FILE__), 'config.json'))
if ! File.exists?(CONFIG)
  CONFIG = CONFIG + ".sample"
end
require_relative File.join(PROJECT_ROOT, 'lib', 'ruby', 'settings')
settings = Settings.new(CONFIG)

ansible_vars = { ansible_ssh_user: 'vagrant',
                 galaxy_config_role_path: File.join(PROJECT_ROOT, "galaxy-ansible", "roles", "galaxy_config") }

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # if you want to use vagrant-cachier,
  # please install vagrant-cachier plugin.
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.enable :apt
  end

  if settings.respond_to?(:galaxy_repo)
    config.vm.synced_folder settings.galaxy_repo, "/galaxy/repo"
    ansible_vars[ "galaxy_repo" ] = "/galaxy/repo"
  end

  config.vm.provider :virtualbox do |vb, override|
    override.vm.box = "Official Ubuntu 14.04 daily Cloud Image amd64 (No Guest Additions)"
    override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
    #override.vm.box = "Official Ubuntu 13.04 daily Cloud Image amd64 (No Guest Additions)"
    #override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"

    vb.customize ['modifyvm', :id, '--memory', settings.vm_ram]
    vb.customize ['modifyvm', :id, '--cpus',   settings.vm_cpus]

    vb.name = 'vagrant-galaxy'
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.extra_vars = ansible_vars
  end

end
