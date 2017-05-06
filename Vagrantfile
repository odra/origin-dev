# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'
require 'yaml'


def load_config(path)
  if !File.file?(path) then
    abort("config file #{path} not found")
  end

  data = YAML.load(IO.readlines(path)[1..-1].join)

  if !data.key?('origin') then
    abort("root element \"origin\" not found in config file \"#{path}\"")
  end

  return data['origin']
end

cfg = load_config('./config.yaml')

Vagrant.configure(2) do |config|
  #base config
  config.vm.box = 'centos/7'
  config.vm.hostname = cfg['hostname']

  #resources
  ['libvirt', 'virtualbox'].each do |provider|
    config.vm.provider provider do |vm|
      vm.cpus = cfg['resources']['cpus']
      vm.memory = cfg['resources']['memory']

      if provider == 'virtualbox' then
        vm.name = cfg['name']
      end
    end
  end

  #ssh config
  config.ssh.insert_key = false
  config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', cfg['key']['private']]
  config.vm.provision 'file', source: cfg['key']['public'], destination: "~/.ssh/authorized_keys"

  #network config
  if cfg['network'].key?('private') then
    config.vm.network "private_network", ip: cfg['network']['private']['ip']
  end
  if cfg['network'].key?('public') then
    #to-do: handle public network config
    abort('Error: public network not supported')
  end
end

