# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"

require "yaml"

VMCFG = YAML.load_file('environment.yml')

require 'pp'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  VMCFG.each do |environment, settings|

    next if environment.eql? "defaults"

    config.vm.define environment do |node|
      node.vm.box = settings["box"]

      node.vm.provider settings["provider"] do |v|
        v.customize ["modifyvm", :id, "--hwvirtex", settings['vtx']]
        v.customize ["modifyvm", :id, "--ioapic", settings['apic']]
        v.customize ["modifyvm", :id, "--cpuexecutioncap", settings["maxcpu"]] 

	v.check_guest_additions = settings['guestext']
	v.functional_vboxsf     = settings['vboxsf']

        v.memory  = settings["ram"]
        v.gui     = settings["gui"]
        v.cpus    = settings["cpus"]
      end
    
      node.vm.synced_folder ".", "/vagrant", disabled: true

      settings.files.each do |filename, file|
        node.vm.provision "shell", inline: "echo \"#{file['contents']}\" >> \"#{file['dest']}\""
      end

      node.vm.provision "shell", inline: settings["provisioning"]

    end
  end
end
