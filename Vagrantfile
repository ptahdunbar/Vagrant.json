# For a complete reference of vagrant, please see the online documentation at
# https://docs.vagrantup.com.
#
# For a complete reference of DevOps, please see the online documentation at
# https://github.com/ptahdunbar/DevOps

require 'rubygems'
require 'json'

# Default to local version of devops.json
if File.exists? "DevOps.local.json"
	boxfile = "devops.local.json"

# If not try devops.json
elsif File.exists? "devops.json"
	boxfile = "devops.json"

# Create a devops based off the example file.
elsif File.exists? "devops-example.json"
	FileUtils.copy_file("devops-example.json", "devops.json")
	puts "[success] Copied devops-sample.json to devops.json."
	puts "[info] Configure your vagrant environment by adding devops definitions to devops.json."
	exit
end

boxes = JSON.parse(File.read(boxfile));

puts "[info] Loading box configuration from #{boxfile}"

# Vagrantfile API version.
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    #
    # Loop through each node
    #
  	boxes.each_with_index do |node, index|

      # GUARD: hostname is the only required param.
  	  next unless node["hostname"]

  	  # get the hostname and tld
  	  if node["hostname"].include? "."
        tld = node["hostname"].split('.').last;
        hostname = node["hostname"]
        host = node["hostname"].split('.').first
      else
        tld = "dev"
        hostname = node["hostname"] + "." + tld
        host = node["hostname"]
  	  end

      # get the box to use for this node
      box = node["box"] ? node["box"] : "ubuntu/trusty64"

      # get the IP address or setup a sequential one
      node_count = index + 1
      ip_address = node["ip"] ? node["ip"] : "%d.%d.%d.%d" % [10, 10, 10, node_count.to_s.ljust(3, '0')]

      # debug box settings
      #puts "hostname: #{hostname}"
      #puts "host: #{host}"
      #puts "tld: #{tld}"
      #puts "box: #{box}"
      #puts "ip_address: #{ip_address}"

      #
      # VM CONFIGURATION
      #
	  config.vm.define "#{hostname}" do |configure_node|

	    # Set the box to use for this node
	    configure_node.vm.box = box

        # Set the guest IP address
	    configure_node.vm.network "private_network", ip: ip_address, :netmask => "255.255.255.0"

		#
		# Configure: Shell Script provisioning
		# Learn more: https://docs.vagrantup.com/v2/provisioning/shell.html
		#
        if node["scripts"]
            if node["scripts"].kind_of? String
                configure_node.vm.provision "shell", path: "#{node["scripts"]}"
            else
                node["scripts"].each do |path|
                    configure_node.vm.provision "shell", path: "#{path}"
                end
            end
        end

        #
        # DNS settings
        #
        configure_node.hostsupdater.remove_on_suspend = true
        configure_node.vm.hostname = hostname
        #configure_node.hostsupdater.aliases = box["hostname"] if box["hostname"].kind_of?(Array)

        #
        # Port forwarding
        # http://docs.vagrantup.com/v2/networking/forwarded_ports.html
        #
        if node["forwarded_ports"]
            node["forwarded_ports"].each do |port|
                configure_node.vm.network "forwarded_port", guest: port["guest"], host: port["host"]
            end
        end

        #
        # Synced folders
        # http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
        #
        if node["synced_folders"]
            node["synced_folders"].each do |params|
                next unless ( params.include?('host') or params.include?('guest') )

                folder_args = Hash[params.map{ |key, value| next if ( !params.include? 'host' or !params.include? 'guest' ); [key.to_sym, value] }]

                # debug arguments passed
                #puts "folder_args: #{folder_args}"

                configure_node.vm.synced_folder params["host"], params["guest"], folder_args
            end
        end

        #
        # SSH Settings override
        #
        if node["settings"]
            config.ssh.username = node["settings"]["ssh_username"] if node["settings"]["ssh_username"]
            config.ssh.host = node["settings"]["ssh_host"] if node["settings"]["ssh_host"]
            config.ssh.port = node["settings"]["ssh_port"] if node["settings"]["ssh_port"]
            config.ssh.private_key_path = node["settings"]["ssh_private_key_path"] if node["settings"]["ssh_private_key_path"]
            config.ssh.forward_agent = node["settings"]["forward_agent"] if node["settings"]["forward_agent"]
            config.ssh.forward_x11 = node["settings"]["forward_x11"] if node["settings"]["forward_x11"]
            config.ssh.shell = node["settings"]["shell"] if node["settings"]["shell"]

            if node["settings"]["disable_default_synced_folder"]
                config.configure_node.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
            end
        end
        
        #
        # Provider: Virtualbox
        #
        config.vm.provider :virtualbox do |virtualbox, override|
            if node["settings"]
                virtualbox.gui = true if node["settings"]["gui"]
                virtualbox.customize ["modifyvm", :id, "--cpus", node["settings"]["cpus"] ] if node["settings"]["cpus"]
                virtualbox.customize ["modifyvm", :id, "--memory", node["settings"]["memory"] ] if node["settings"]["memory"]
            end
        end
        
        #
        # Provider: VMWare Fusion
        #
        config.vm.provider :vmware_fusion do |vmware, override|
            if node["settings"]
                override.vm.node_url = "http://files.vagrantup.com/precise64_vmware.node"
                vmware.gui = true if node["settings"]["gui"]
                vmware.vmx["numvcpus"] = node["settings"]["cpus"] if node["settings"]["cpus"]
                vmware.vmx["memsize"] = node["settings"]["cpus"] if node["settings"]["memory"]
            end
        end

        #
        # Provider: Amazon Web Services (AWS)
        #
        config.vm.provider :aws do |aws, override|
            if node["ami"]
                override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
                override.ssh.private_key_path = node["ami"]["private_key_path"]
                override.ssh.username = node["ami"]["username"]

                aws.keypair_name = node["ami"]["keypair_name"]

                aws.ami = node["ami"]["id"] if node["ami"]["id"]
                aws.availability_zone = node["ami"]["availability_zone"] if node["ami"]["availability_zone"]
                aws.region = node["ami"]["region"] if node["ami"]["region"]
                aws.instance_type = node["ami"]["instance_type"] if node["ami"]["instance_type"]
                aws.security_groups = node["ami"]["security_groups"] if node["ami"]["security_groups"]
                aws.iam_instance_profile_arn = node["ami"]["iam_instance_profile_arn"] if node["ami"]["iam_instance_profile_arn"]
                aws.iam_instance_profile_name = node["ami"]["iam_instance_profile_name"] if node["ami"]["iam_instance_profile_name"]
                aws.use_iam_profile = node["ami"]["use_iam_profile"] if node["ami"]["use_iam_profile"]
                aws.private_ip_address = node["ami"]["private_ip_address"] if node["ami"]["private_ip_address"]
                aws.subnet_id = node["ami"]["subnet_id"] if node["ami"]["subnet_id"]
                aws.tags = node["ami"]["tags"] if node["ami"]["tags"]
                aws.user_data = node["ami"]["user_data"] if node["ami"]["user_data"]
            end
        end

        #
        # Provider: Digital Ocean
        #
        config.vm.provider :digital_ocean do |digital_ocean, override|
            if node["digital_ocean"]
                override.vm.box_url = "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"
                override.ssh.private_key_path = node["digital_ocean"]["private_key_path"]

                digital_ocean.client_id = node["digital_ocean"]["client_id"]
                digital_ocean.api_key = node["digital_ocean"]["api_key"]

                digital_ocean.ssh_key_name = node["digital_ocean"]["ssh_key_name"] if node["digital_ocean"]["ssh_key_name"]
                digital_ocean.image = node["digital_ocean"]["image"] if node["digital_ocean"]["image"]
                digital_ocean.region = node["digital_ocean"]["region"] if node["digital_ocean"]["region"]
                digital_ocean.size = node["digital_ocean"]["size"] if node["digital_ocean"]["size"]
                digital_ocean.private_networking = node["digital_ocean"]["private_networking"] if node["digital_ocean"]["private_networking"]
                digital_ocean.setup = node["digital_ocean"]["setup"] if node["digital_ocean"]["setup"]
            end
        end

	  end # config.vm.define

  	end # boxes.each
end # Vagrant.configure