# -*- mode: ruby -*-
# vi: set ft=ruby :


CURRENT_DIR = File.expand_path(File.dirname(__FILE__))
ANSIBLE_TAGS = ENV.fetch('ANSIBLE_TAGS', 'all')

Vagrant.configure('2') do |config|
  config.vm.box_check_update = false

  config.vm.define 'devbox', primary: true do |devbox|
    devbox.vm.hostname = 'mtg-devbox'
    devbox.vm.network 'private_network', ip: '10.0.0.100'
    devbox.vm.box = 'ubuntu/bionic64'

    devbox.vm.synced_folder '.', '/vagrant',
      type:          'nfs',
      mount_options: ['rw', 'vers=3', 'noatime', 'async']

    devbox.vm.provider 'virtualbox' do |vb|
      vb.gui    = false
      vb.memory = 512
      vb.cpus   = 1
    end

    if not Vagrant.has_plugin?('vagrant-host-shell')
      print 'Ansible Galaxy roles should be installed.'
      print 'Please run ansible-galaxy install -r ansible-requirements.yml'
      print 'Or install vagrant-host-shell plugin'
    end

    devbox.vm.provision :host_shell do |host_shell|
      host_shell.inline = "cd '#{CURRENT_DIR}' && ansible-galaxy install -r '#{CURRENT_DIR}/ansible-requirements.yml'"
    end

    devbox.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'provision/site.yml'
      ansible.tags = ANSIBLE_TAGS
      ansible.compatibility_mode = '2.0'
    end
  end
end
