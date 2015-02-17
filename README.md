# DevOps

> Bootstrap your application stack and maintain development/production parity. Inspired by [the 12factor app](http://12factor.net/) and the sheer pain of doing sysadmin work, ugh.

## Requirements
* [VirtualBox](https://www.virtualbox.org/) or [AWS account](http://aws.amazon.com/) or [DigitalOcean account](http://digitalocean.com/).
* Latest version [Vagrant](http://www.vagrantup.com/).

# Installation

```
git clone git://github.com/ptahdunbar/DevOps.git new-project
cd new-project
./setup.sh
```

# Usage

* Configure the node(s) needed (See the definitions section below).
    * `vim Varrgrant.json`
* Launch the node(s):
    * `vagrant up`
* SSH into the node(s):
    * `vagrant ssh` (if its just one machine)
    * `vagrant ssh nodename` (use the "hostname" property)
* Stop the node(s):
    * `vagrant halt`
* Update the node(s) with the latest changes:
    * `vagrant provision` (you should've already ran `vagrant up`)
* Re-sync all synced folders and networking:
    * `vargrant reload`
* Destroy the node(s) and delete everything:
    * `vagrant destory`
* Launch the machine(s) again:
    * `vagrant up` (catching the flow? :)
* Destroy and reboot the machine(s) in one-go:
    * `vagrant pristine`

## What is vagrant and why do we need it?

> See [vagrantup.com](http://vagrantup.com) for documentation

**TL;DR** Vagrant is a command-line tool that enables us to have development/production environment nirvana.

Top 3 reasons why I love vagrant:

1. Like production.
    * If your production enviroment consist of multiple web servers and a separate db server, Vagrant can replicate that setup so you can simulate/debug/test code in an identical enviroment.
2. Team friendly.
    * Work with colleagues who refuses to switch to mac (or your environment that has everything installed)? Vagrant ensures that everyone works in the same environment no matter what operating system they sport.
3. Configuration management support
    * Easily use whatever provisioning tool for the project.

## Vagrant plugins
To make your development workflow easier, devops comes bundled with the following [vagrant plugins](https://github.com/mitchellh/vagrant/wiki/Available-Vagrant-Plugins):

* [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier) - Speeds up apt-get.
* [vagrant-exec](https://github.com/p0deje/vagrant-exec) - Run one-liner commands into a machine, without ssh'ing into it.
* [vagrant-pristine](https://github.com/fgrehm/vagrant-pristine) - Adds pristine command to vagrant.
* [vagrant-dns](https://github.com/BerlinVagrant/vagrant-dns) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-aws](https://github.com/mitchellh/vagrant-aws) - Test/deploy to your amazon AWS account.
* [vagrant-digitalocean](https://github.com/smdahlen/vagrant-digitalocean) - Test/deploy to your Digital Ocean account.
* [vagrant-managed-servers](https://github.com/tknerr/vagrant-managed-servers) - Test/deploy to any VPS server via SSH.

## What is Varrgrant.json?
`Varrgrant.json` is the core of Varrgrant, which is really just syntactical vagrant sugar to simplify setting up multi-machine environments and provisioning. I wanted an clear and simple format of how my machines where configured without always having to mess with `Vagrantfile` which can get messing, and repetitive.

### Varrgrant.json format

`Varrgrant.json` is a json file containing an array of machines for your enviroment.

Example `Varrgrant.json` file with one machine:

```
[
	{
		"hostname" : "foo",
	}
]
```

Example `Varrgrant.json` file with two machines:

```
[
	{
		"hostname" : "foo",
	},
	{
		"hostname" : "bar",
	}
]
```

Example `Varrgrant.json` file with three machines:

```
[
	{
		"hostname" : "foo",
	},
	{
		"hostname" : "bar",
	},
	{
		"hostname" : "bazz",
	}
]
```

You get the point...


### Varrgrant definitions in Varrgrant.json

Varrgrant definitions closely resemble vagrant's configuration directives you would typically find in a `Vagrantfile`. They've been pulled out and abstracted to improve workflow efficiency and maintainability.

#### Hostname

* `hostname` - Configure one or more hostnames for the machine. The first one is always the primary. Additional hostnames get added to your `/etc/hosts` file upon a successful `vagrant up` or `vagrant reload`. This is the only required defintion needed to spin up an new instance.

Example using one hostname:

```
[
	{
		"hostname": "varrgrant.dev"
	}
]
``` 

Example using two hostnames:

```
[
	{
		"hostname": ["varrgrant.dev", "example.dev"]
	}
]
``` 


#### IP Addresses
* `ip` - Configure one or more IP addresses for this machine. Each IP address is mapped to the above hostnames and added to your `/etc/hosts` file.

Example with one IP address:

```
[
	{
		"hostname": "varrgrant.dev",
		"ip": "10.10.10.100"
	}
]
```

Example with multiple IP addresses:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"ip": [
			"10.10.10.100",
			"10.10.10.500"
		]
	}
]
``` 

#### Synced Folders

* `synced_folders` Sync one or more folders between your host machine and vagrant machine, allowing you to use your favorite IDE or editor.

Example syncing the root directory to `/srv/www/example.vm` in the guest machine:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"synced_folders" : [
			{
				"host" : ".",
				"guest" : "/srv/www/example.vm"
			}
		]
	}
]
```

* `synced_folders` Options
	* `id` - The synced_folder identifier
	* `host` - A relative or absolute path on the host machine
	* `guest` - An absolute path on the guest machine.
	* `mount_options` - Folder Permissions
	    * `dmode` - Unix permissions (775) for directories
	    * `fmode` - Unix permissions (664) for files
	* `user` - User name on the guest machine to assign permissions too.
	* `group` - Group name on the guest machine to assign permissions too.
	* `disable` - boolean value to disable synced folders for this group. Usually combined with `id`.

Example synced_folder setup for a web project:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"synced_folders" : [
			{
				"host" : ".",
				"guest" : "/srv/www/example.vm",
				"mount_options" : [
					"dmode=775",
					"fmode=664"
				],
				"owner" : "www-data",
				"group" : "www-data"
			}
		]
	}
]
```
Example synced_folder setup disabling the default `/vagrant` that gets synced:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"synced_folders" : [
			{
				"host": ".",
				"guest": "/vagrant",
				"id": "vagrant-root",
				"disabled": true
			}
		]
	}
]
```

Example syncing multiple folders:

```
[
	{
		"hostname": "varrgrant.dev",
		"synced_folders" : [
			{
				"host" : "./public",
				"guest" : "/srv/www/example.vm"
			},
			{
				"host" : "./logs",
				"guest" : "/var/logs/webserver"
			}
		]
	}
]
```

#### Settings
* `settings` - Reconfigure default settings for the virtual machine
	* `memory` - Set the memory allocated to this machine. Defaults to `512`.
	* `cpu` - Configure the amount of processors the machine needs. Defaults to `1`.
	* `gui` - Boolean value to control enable/disable a GUI window, supported by VirtualBox and VMWare. Defaults to `false`.
	* `ssh_username` (string)
    * `ssh_host` (string)
    * `ssh_port` (integer)
    * `ssh_private_key_path` (string)
    * `forward_agent`(boolean)
    * `forward_x11` (boolean)
    * `shell` (string)

Example changing the default memory to 1GiB. Note that this increases the amount of space it takes up on your host system.

```
[
	{
		"hostname": ["varrgrant.dev"],
		"settings" : {
			"memory": 1024
		}
	}
]
```

Example changing the cpus and enabling a GUI.

```
[
	{
		"hostname": ["varrgrant.dev"],
		"settings" : {
			"cpus": 2,
			"gui": true
		}
	}
]
```

Example configuring basic ssh parameters:

```
[
	{
		"hostname" : "varrgrant.dev",

		"config" : {
			"ssh_username" : "vagrant",
			"ssh_port" : 22,
			"ssh_private_key_path" : "/path/to/private.key",
			"forward_agent" : true,
			"forward_x11" : true,
			"shell" : "bash -l"
		}
	}
]
```



#### Port Fowarding

Example port forwarding:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"port_fowarding" : [
			{
				"guest": 80,
				"host": 9000
			}
		]
	}
]
```

#### AMI

Varrgrant supports the [Amazon AWS provider](https://github.com/mitchellh/vagrant-aws). Once you fill out the relevant details, simply run:

```
vagrant up --provder=aws
```

* `aws` - Container for AWS parameters
	* `access_key_id` - The access key for accessing AWS. [Get it here](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html)
	* `ami` - The AMI id to boot, such as "ami-12345678"
	* `availability_zone` - The availability zone within the region to launch
  the instance. If nil, it will use the default set by Amazon.
  * `instance_ready_timeout` - The number of seconds to wait for the instance
  to become "ready" in AWS. Defaults to 120 seconds.
  * `instance_type` - The type of instance, such as "m1.small". The default
  value of this if not specified is "m1.small"
  * `keypair_name` - The name of the keypair to use to bootstrap AMIs
   which support it.
   * `private_ip_address` - The private IP address to assign to an instance
  within a [VPC](http://aws.amazon.com/vpc/)
  * `region` - The region to start the instance in, such as "us-east-1"
  * `secret_access_key` - The secret access key for accessing AWS. [Get it here](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html)
  * `security_groups` - An array of security groups for the instance. If this
  instance will be launched in VPC, this must be a list of security group
  IDs.
  * `iam_instance_profile_arn` - The Amazon resource name (ARN) of the IAM Instance
    Profile to associate with the instance
  
  * `iam_instance_profile_name` - The name of the IAM Instance Profile to associate
  with the instance
  * `subnet_id` - The subnet to boot the instance into, for VPC.
  * `tags` - A hash of tags to set on the machine.
  * `use_iam_profile` - If true, will use [IAM profiles](http://docs.aws.amazon.com/IAM/latest/UserGuide/instance-profiles.html)
  for credentials.

Example of a basic configuration of a ubuntu 12.04 LTS instance using the AWS provider:

```
[
	{
		"hostname": [
			"varrgrant", "varrgrant.dev"
		],
		"ami" : {
			"id" : "ami-a73264ce",
			"instance_type" : "t1.micro",
			"security_groups" : ["default"],

			"access_key_id" : "AKIAIOSFODNN7EXAMPLE",
			"secret_access_key" : "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
			"keypair_name" : "aws-keypairname",
			"private_key_path" : "~/.ssh/aws-keypairname.pem",
			"username" : "ubuntu"
		}
	}
]
```

#### Complex Varrgrant Definitions

Example machine running:
* Upgraded memory from 512MB to 1Gib.
* Run on two cpus.
* Added an additional hostname for an example app.
* Syned a host folder linked to the document root of the example app.
* Installs a database and web server with PHP5.5 installed.
* Disables the default site

```
[
	{
		"hostname": [
			"varrgrant.dev",
			"example.dev"
		],
		"ip": ["10.10.10.800"],

		"customize": {
			"memory": 1024,
			"cpu": 2
		},

		"synced_folders": [
			{
				"host": "./public",
				"guest": "/srv/www/example.dev",
				"owner": "vagrant",
				"group": "www-data",
				"mount_options": [
					"fmode=664",
					"dmode=775"
				]
			}
		],
		
		"chef": {
			"recipes": [
				"base",
				"db",
				"web",
				"php",
				"wp"
			],
			"json" : {
				"db" : {
					"databases": ["example_wp"],
					"users" : ["example_user"]
				},
				"web" : {
					"vhosts": ["example.dev"]
				},
				"php" : {
					"version" : "5.5"
				}
			}
		}
	}
]
```


## Review of Varrgrant definitions
I hope by now you'll see how easy and fun it is to build up your application's infrastructure using Varrgrant for Vagrant.
By combining these various definitions together, you can really setup a fully fledged application stack with multiple servers talking to each other, and deploying on the AWS platform.

I hope you enjoy using Varrgrant for Vagrant as much as I do. There are a lot of other secret gems hidden in all the various cookbooks that are waiting to be taking advantage of. Definitely take some time to learn chef basics, and learn how to override attributes, as well as develop your own cookbooks for your use cases.

> Yarrty yarr yarr matey -_O
>
> Pirate Dunbar
