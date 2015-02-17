# The most common configuration options are documented and commented below.
# For a complete reference, please see the online documentation at
# https://docs.vagrantup.com.
#
# Can't run vagrant without Vagrantfile.json
#

#
# Loading Varrgrant
#
require 'ipaddr'
require 'rubygems'
require 'json'

# Default to local version of Varrgrant.json
if File.exists? "Varrgrant.local.json"
	boxfile = "Varrgrant.local.json"

# If not try Varrgrant.json
elsif File.exists? "Varrgrant.json"
	boxfile = "Varrgrant.json"

# Create a Varrgrant based off the example.
elsif File.exists? "Varrgrant-example.json"
	FileUtils.copy_file("Varrgrant-example.json", "Varrgrant.json")
	puts "[success] Copied Varrgrant-sample.json to Varrgrant.json."
	puts "[info] Configure your vagrant environment by adding Varrgrant definitions to Varrgrant.json."
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

      # Get the hostname
      hostname = (node["hostname"].kind_of? String) ? node["hostname"] : node["hostname"].first

      # get the hostname part 2
      host = hostname.split('.').first;

      # get the tld
      tld = hostname.split('.').last;

      # get the box to use for this node
      box = node["box"] ? node["box"] : "ubuntu/trusty64"

      # get the IP address or setup a sequential one
      node_count = index + 1
      ip_address = node["ip"] ? node["ip"] : "%d.%d.%d.%d" % [10, 10, 10, node_count.to_s.ljust(3, '0')]

      # RESULTS
      puts "hostname: #{hostname}"
      puts "host: #{host}"
      puts "tld: #{tld}"
      puts "box: #{box}"
      puts "ip_address: #{ip_address}"

      #
      # VM CONFIGURATION
      #
	  config.vm.define "#{hostname}" do |configure_node|

	    # Set the box to use for this node
	    configure_node.vm.box = box

        # Set the guest IP address
	    config.vm.network "private_network", ip: ip_address, :netmask => "255.255.255.0"

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


	  end # config.vm.define

  	end # boxes.each
end # Vagrant.configure