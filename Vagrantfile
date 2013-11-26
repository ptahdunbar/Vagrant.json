# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'rubygems'
require 'json'

# Can't run vagrant without Vagrantfile.json
unless File.exists?("Varrgrant.json") then
    raise "Please create a Varrgrant.json configuration file to configure your vagrant environment."
    exit
end

boxfile = "Varrgrant.json"
boxes = JSON.parse(File.read(boxfile));

puts "[info] Loading box configuration from #{boxfile}"

# Vagrantfile API version.
VAGRANTFILE_API_VERSION = "2"

# provider: vmware_fusion, aws, virtualbox
case "#{ARGV[1]}"; when '--provider=aws'; provider = 'aws'; when '--provider=vmware_fusion'; provider = 'vmware_fusion'; else; provider = 'virtualbox'; end if ARGV[1]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    # SETUP global configuration for boxes
    box_config_global = proc do |box_config|

        box_config.vm.box = "precise"

        case provider
        	when 'vmware_fusion'
        		box_config.vm.box_url = "http://files.vagrantup.com/precise64_vmware.box"

        	when 'aws'
        		box_config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

        	else
        		box_config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"
		end

		if defined? VagrantPlugins::Cachier
			# plugin: vagrant-cachier
			puts "[info] vagrant-cachier enabled."
			box_config.cache.scope = :machine
        end
    end

    # SETUP box configuration
    boxes.each do |box|
    	next unless box["hostname"]

    	hostname = (box["hostname"].kind_of? String) ? box["hostname"] : box["hostname"].first

        config.vm.define hostname do |config|
            box_config_global.call(config)

            config.vm.hostname = hostname

            if box["ip"]
            	if box["ip"].kind_of? String
					config.vm.network "private_network", ip: box["ip"]
				else
					box["ip"].each do |ipaddress|
						config.vm.network "private_network", ip: ipaddress
					end
				end
			end

			if defined? VagrantPlugins::HostsUpdater and box["hostname"] and box["ip"]
				# plugin - hostsupdater
				puts "[info] vagrant-hostsupdater enabled."
				# temp until I get host entries to be unique.
				box["hostname"].shift
				config.hostsupdater.aliases = box["hostname"]
			end

			if box["port_fowarding"]
				box["port_fowarding"].each do |port|
					config.vm.network "forwarded_port", guest: port["guest"], host: port["host"]
				end
			end

			if box["synced_folders"]
				box["synced_folders"].each do |params|
					next unless ( params.include?('host') or params.include?('guest') )

					folder_args = Hash[params.map{ |key, value| next if ( !params.include? 'host' or !params.include? 'guest' ); [key.to_sym, value] }]

					# Make the directory if it doesnt exists
					FileUtils.mkdir_p(params["host"]) unless File.exists?(params["host"])

					config.vm.synced_folder params["host"], params["guest"], folder_args
				end
			end

            if box["customize"]
                config.vm.provider "virtualbox" do |node|
                    node.gui = true if box["customize"]["gui"]
                    node.customize ["modifyvm", :id, "--cpus", box["customize"]["cpus"] ] if box["customize"]["cpus"]
                    node.customize ["modifyvm", :id, "--memory", box["customize"]["memory"] ] if box["customize"]["memory"]
                end

                config.vm.provider "vmware_fusion" do |node|
					node.gui = true if box["customize"]["gui"]
					node.vmx["numvcpus"] = box["customize"]["cpus"] if box["customize"]["cpus"]
					node.vmx["memsize"] = box["customize"]["cpus"] if box["customize"]["memory"]
				end
            end

			box["ami"] and config.vm.provider :aws do |aws, override|
				# I'd like to use environment variables for these,
				# but cant seem to get it working for the life of me. grrr!
				aws.access_key_id 				= box["ami"]["access_key_id"]
				aws.secret_access_key 			= box["ami"]["secret_access_key"]
				aws.keypair_name 				= box["ami"]["keypair_name"]
				override.ssh.private_key_path 	= box["ami"]["private_key_path"]
				override.ssh.username 			= box["ami"]["username"]

				aws.ami 						= box["ami"]["id"] if box["ami"]["id"]
				aws.availability_zone			= box["ami"]["availability_zone"] if box["ami"]["availability_zone"]
				aws.region						= box["ami"]["region"] if box["ami"]["region"]
				aws.instance_type				= box["ami"]["instance_type"] if box["ami"]["instance_type"]
				#aws.security_groups			= box["ami"]["security_groups"] if box["ami"]["security_groups"]
				aws.iam_instance_profile_arn 	= box["ami"]["iam_instance_profile_arn"] if box["ami"]["iam_instance_profile_arn"]
				aws.iam_instance_profile_name 	= box["ami"]["iam_instance_profile_name"] if box["ami"]["iam_instance_profile_name"]
				aws.use_iam_profile 			= box["ami"]["use_iam_profile"] if box["ami"]["use_iam_profile"]
				aws.private_ip_address			= box["ami"]["private_ip_address"] if box["ami"]["private_ip_address"]
				aws.subnet_id					= box["ami"]["subnet_id"] if box["ami"]["subnet_id"]
				aws.tags						= box["ami"]["tags"] if box["ami"]["tags"]
				aws.user_data					= box["ami"]["user_data"] if box["ami"]["user_data"]
			end

            if box["chef"] and defined? VagrantPlugins::Omnibus
                # plugin: vagrant-omnibus
                puts "[info] vagrant-ominbus enabled."
                config.omnibus.chef_version = :latest

				# plugin: vagrant-berkshelf
                puts "[info] vagrant-berkshelf enabled."
                config.berkshelf.enabled = true

                config.vm.provision :chef_solo do |chef|
                    if box["chef"]["recipes"]
                        if box["chef"]["recipes"].kind_of? String
                            chef.add_recipe "recipe[#{box["chef"]["recipe"]}]"
                        else
                            box["chef"]["recipes"].each do |recipe|
                                chef.add_recipe "recipe[#{recipe}]"
                            end
                        end
                    end

					# Register custom chef attributes
					chef.json = box["chef"]["json"] if box["chef"]["json"]
                end
            end
        end
    end
end