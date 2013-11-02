# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'rubygems'
require 'json'

# Can't run vagrant without Vagrantfile.json
unless File.exists?("boxes.json") then
    raise "Please create a boxes.json configuration file to configure your vagrant environment."
    exit
end

boxes = JSON.parse(File.read("boxes.json"));

puts "[info] Loading box configuration from boxes.json"

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    # SETUP global configuration for boxes
    box_config_global = proc do |box_config|
        box_config.vm.box = "precise"

		if defined? VagrantPlugins::Cachier
			# plugin: vagrant-cachier
			box_config.cache.scope = :machine
        end
    end

    # SETUP box configuration
    boxes.each do |box|
        config.vm.define box["name"] do |config|
            box_config_global.call(config)

            config.vm.hostname = "%s.vm" % box["name"].to_s
            config.vm.network "private_network", ip: box["ip"] if box["ip"]

            if defined? VagrantPlugins::HostsUpdater
            	# plugin - hostsupdater
                config.hostsupdater.aliases = box["hosts"] if box["hosts"] and box["ip"]
            end

            if box["customize"]
                config.vm.provider "virtualbox" do |node|
                    node.gui = true if box["customize"]["gui"]
                    node.customize ["modifyvm", :id, "--cpus", box["customize"]["cpus"] ] if box["customize"]["cpus"]
                    node.customize ["modifyvm", :id, "--memory", box["customize"]["memory"] ] if box["customize"]["memory"]
                end
            end

            if box["forward_port"]
                box["forward_port"].each do |port|
                    config.vm.network "forwarded_port", guest: port["guest"], host: port["host"]
                end
            end

            if box["synced_folder"]
                box["synced_folder"].each do |params|
                    next if ( !params.include? 'host' or !params.include? 'guest' )

                    folder_args = Hash[params.map{ |key, value| next if ( !params.include? 'host' or !params.include? 'guest' ); [key.to_sym, value] }]

                    config.vm.synced_folder params["host"], params["guest"], folder_args
                end
            end

            if box["chef"] and defined? VagrantPlugins::Omnibus

                # plugin: vagrant-omnibus
                config.omnibus.chef_version = :latest

                config.berkshelf.enabled = true

                config.vm.provision :chef_solo do |chef|
                    if box["chef"]["recipe"]
                        if box["chef"]["recipe"].kind_of? String
                            chef.add_recipe box["chef"]["recipe"].to_s
                        else
                            box["chef"]["recipe"].each do |recipe|
                                chef.add_recipe recipe
                            end
                        end
                    end

                    chef.json = box["chef"]["json"] if box["chef"]["json"]
                end
            end
        end
    end
end