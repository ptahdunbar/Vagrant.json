# Vote Varrgrant for Vagrant!

> Bootstrap your application stack and maintain development/production parity. Inspired by [the 12factor app](http://12factor.net/) and the sheer pain of doing sysadmin work, ugh.

## Requirements
* [VirtualBox](https://www.virtualbox.org/) or [VMWare Fusion](http://www.vmware.com/products/fusion/) or an [AWS account](http://aws.amazon.com/).
* Latest version [Vagrant](http://www.vagrantup.com/).
* Optionally [Berkshelf](http://berkshelf.com) for chef cookbook development.

# Installation

```
git clone git://github.com/ptahdunbar/Varrgrant.git Varrgrant;
cd Varrgrant
cp Varrgrant-sample.json Varrgrant.json
vagrant plugin install bindler
vagrant bindler setup
vagrant plugin bundle
vagrant up
```

#### Wait, what just happened?!

* We downloaded the github repository and changed into it's directory.
* We then copied the sample Varrgrant-sample.json file to Varrgrant.json which will contain our stack definitions.
* Finally, we've initialized the vagrant environment, downloading all plugins and booted the machine(s).

# Welcome to Varrgrant for Vagrant
Upon running `vagrant up`, You now have a bare-bones Ubuntu LTS 12.04 installed and running!

The next step is to make the system useful by installing everything we need to run our application stack (multiple web servers, databases, cache servers, the entire stack). 

The first time takes *a while* (depening on your internet connection) as it has to perform a one-time download of the operating system and installing the vagrant-berkshelf plugin hangs, but keep trying a few times!

Once downloaded, it's saved to your computer and usable everywhere.

# Usage

* Configure the machine(s) needed (See the Varrgrant definitions section below).
    * `vim Varrgrant.json`
* To launch the machine(s), run:
    * `vagrant up`
* To ssh into the machine(s), run:
    * `vagrant ssh` (if its just one machine)
    * `vagrant ssh varrgrant.dev` (sepecify the first hostname of the machine)
* To stop the machine(s), run:
    * `vagrant halt`
* To update the machine(s) with the latest changes (for chef development), run:
    * `vagrant provision` (you should've already ran `vagrant up`)
* To re-sync all synced folders and add new ones, run:
    * `vargrant reload`
* To destroy the machine(s) and delete everything, run:
    * `vagrant destory`	
* To launch the machine(s) again, run:
    * `vagrant up` (catching the flow? :)
* To destroy and reboot the machine(s) in one-go, run:
    * `vagrant pristine`


## What is vagrant and why do we need it?

> See [vagrantup.com](http://vagrantup.com) for documentation

**TL;DR** Vagrant is a command-line tool that enables us to have development/production environment nirvana.

Here are my top 3 favorite reasons why I love vagrant:

1. Like production.
    * If your production enviroment consist of multiple web servers and a separate db server, Vagrant can replicate that setup so you can simulate/debug/test code in an identical enviroment.
2. Team friendly.
    * Work with colleagues who refuses to switch to mac (or your environment that has everything installed)? Vagrant ensures that everyone works in the same environment no matter what operating system they sport.
3. Configuration management support
    * We take advantage of [chef](https://opscode.com/chef) and [berkshelf](http://berkshelf.com/) to spin up nodes to a known state *in seconds*!


## Provisioning with Chef & Berkshelf

> See [Learnchef](https://learnchef.opscode.com/) for instructions and documentation.

**TL; DR** Chef enables us to code templates for how our machine(s) (or servers) should be configured and essentially boot up brand-new instance(s) of it's configuration with everything installed and running in seconds!

Here are my top 3 favorite reasons why I love chef:

1. Always know the state of a machine when you ssh into it.
2. Ease of maintainance and updates when working in a complex environment with multiple machine(s) running.
3. Works with *any* deployment enviroment (AWS, VirtualBox, VMWare, and Vagrant).

## Chef cookbook development with Berkshelf

> See [Berkshelf](http://berkshelf.com/) for instructions and documentation.

**TL; DR** Berkshelf makes developing chef cookbooks and reusing community cookbooks stupid easy and fun. Simply define cookbooks in the project's `Berksfile`.

### The Berkshelf Way

* [Learn what a cookbook is](https://learnchef.opscode.com/), and [the differences between various types of cookbooks](http://www.prashantrajan.com/posts/2013/06/leveling-up-chef-best-practices/) before pursuing chef development. Watch [The Berkshelf Way](http://www.youtube.com/watch?v=hYt0E84kYUI) for more info.

##### Install Berkshelf
* Install Berkshelf `sudo gem install berkshelf --no-rdoc --no-ri`

### Chef cookbook development Workflow

* To create a new cookbook, run:
	* `berks cookbook foo` (you can optionally save cookbooks into a `vendor/cookbooks` directory which is .gitignored)
* Update available cookbooks in `Berksfile`.
	* `vim Berksfile`
* To download all cookbooks specified in `Berksfile`, run:
	* `berks install`
* Update the cookbooks to their latest versions, run:
	* `berks update`


By default, Varrgrant comes bundled with the following cookbooks:

### Bundled Cookbooks
* [MySQL](https://github.com/opscode-cookbooks/mysql)
* [database](https://github.com/opscode-cookbooks/database)
* [logrotate](https://github.com/opscode-cookbooks/logrotate)
* [apache2](https://github.com/opscode-cookbooks/apache2)
* [nginx](https://github.com/opscode-cookbooks/nginx)
* [postfix](https://github.com/opscode-cookbooks/postfix)
* [xml](https://github.com/opscode-cookbooks/xml)
* [git](https://github.com/opscode-cookbooks/git)
* [subversion](https://github.com/opscode-cookbooks/subversion)
* [vim](https://github.com/opscode-cookbooks/vim)
* [sudo](https://github.com/opscode-cookbooks/sudo)
* [nodejs](https://github.com/mdxp/nodejs-cookbook)
* [redisio](https://github.com/brianbianco/redisio)
* [timezone-ii](https://github.com/L2G/timezone-ii)
* [base](https://github.com/ptahdunbar/base-cookbook)
* [php](https://github.com/ptahdunbar/php-cookbook)
* [web_server](https://github.com/ptahdunbar/web_server-cookbook)
* [database_server](https://github.com/ptahdunbar/database_server-cookbook)


## Vagrant plugins
To make your development workflow easier, Varrgrant comes bundled with the following [vagrant plugins](https://github.com/mitchellh/vagrant/wiki/Available-Vagrant-Plugins):

* [vagrant-exec](https://github.com/p0deje/vagrant-exec) - Run one-liner commands into a machine, without ssh'ing into it.
* [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier) - Speeds up apt-get.
* [vagrant-omnibus](https://github.com/schisamo/vagrant-omnibus) - Installs Chef client.
* [vagrant-berkshelf](https://github.com/riotgames/vagrant-berkshelf) - Installs Berkshelf.
* [vagrant-pristine](https://github.com/fgrehm/vagrant-pristine) - Adds pristine command to vagrant.
* [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-vmware-fusion](http://www.vagrantup.com/vmware) - If your runnning VMWare Fusion, which is much faster than virturalbox.
* [vagrant-aws](https://github.com/mitchellh/vagrant-aws) - Test/deploy to your amazon AWS account.

## What is Varrgrant.json?
`Varrgrant.json` is the core of Varrgrant, which is really only syntactical vagrant sugar to simplify setting up multi-machine environments and provisioning. I wanted an clear and simple format of how my machines where configured without always having to mess with 	`Vagrantfile` which can get messing, and repetitive.


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

#### Customize
* `customize` - Reconfigure default settings for the virtual machine
	* `memory` - Set the memory allocated to this machine. Defaults to `512`.
	* `cpu` - Configure the amount of processors the machine needs. Defaults to `1`.
	* `gui` - Boolean value to control enable/disable a GUI window, supported by VirtualBox and VMWare. Defaults to `false`.

Example changing the default memory to 1GiB. Note that this increases the amount of space it takes up on your host system.

```
[
	{
		"hostname": ["varrgrant.dev"],
		"customize" : [
			{
				"memory": 1024
			}
		]
	}
]
```

Example changing the cpus and enabling a GUI.

```
[
	{
		"hostname": ["varrgrant.dev"],
		"customize" : [
			{
				"cpus": 2,
				"gui": true
			}
		]
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

#### VMWare Fusion
Varrgrant supports VMWare Fusion off the back. You'll need the following requirements before provisioning with VMWare Fusion:

* [VMWare Fusion](http://store.vmware.com/store/vmware/en_US/home) ($59)
* [Vagrant VMWare Plugin](http://www.vagrantup.com/vmware) ($79)

After purchasing the Vagrant VMWare plugin and installing the license.lic, you'll be able to boot up your environment by specifying vmware fusion as the provider:

```
vagrant up --provder=vmware_fusion
```

Bundled in the Vagrantfile is a Ubuntu 12.04 LTS base box: `http://files.vagrantup.com/precise64_vmware.box`


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

#### Chef Recipes

Finally, you can specify chef configuration details such as recipes and custom attributes.

Example of booting up an instance with base cookbook (installs tools such as git, vim, subversion, sets the timezone and a whole lot more):

```
[
	{
		"hostname": ["varrgrant.dev"],
		"ip": ["10.10.10.100"],
		"chef" : {
			"recipes" : [
				"base"
			]
		}
	}
]
```

Example of booting up a MySQL database server:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"ip": ["10.10.10.200"],
		"chef" : {
			"recipes" : [
				"base",
				"database_server"
			]
		}
	}
]
```

Example of booting up an nginx web server:

```
[
	{
		"hostname": ["varrgrant.dev"],
		"ip": ["10.10.10.200"],
		"chef" : {
			"recipes" : [
				"base",
				"web_server"
			]
		}
	}
]
```

Example of booting up:

* a nginx web server,
* a MySQL database server
* and installing PHP 5.4 with xdebug and composer

```
[
	{
		"hostname": [ "app" ],
		"ip": ["10.10.10.100"],
		"chef" : {
			"recipes" : [
				"base",
				"web_server",
				"php"
			]
		}
	},
	{
		"hostname": [ "db" ],
		"ip": ["10.10.10.200"],
		"chef" : {
			"recipes" : [
				"base",
				"database_server"
			]
		}
	}
]
```
#### Chef JSON Attributes

Chef allows you to customize cookbook recipes by passing in cookbook attributes in a json format. Whatever cookbooks expose as attributes, they can be overriden which leads to extreme flexibility.

Example of booting up:

* an apache2 (overriding the default nginx) web server,
* a MySQL database server with a table and user,
* and installing PHP 5.4 with xdebug and composer:

```
[
	{
		"hostname": [ "app" ],
		"ip": ["10.10.10.100"],
		"chef" : {
			"recipes" : [
				"base",
				"web_server",
				"php"
			],
			"json" : {
				"web_server" : {
					"platform" : "apache2"
				}
			}
		}
	},
	{
		"hostname": [ "db" ],
		"ip": ["10.10.10.200"],
		"chef" : {
			"recipes" : [
				"base",
				"database_server"
			],
			"json" : {
				"database_server" : {
					"databases" : ["wordpress"],
					"users" : ["wordpress"]
				}
			}
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
				"database_server",
				"web_server",
				"php",
				"wp"
			],
			"json" : {
				"database_server" : {
					"databases": ["example_wp"],
					"users" : ["example_user"]
				},
				"web_server" : {
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
I hope by now you'll see how easy and fun it is to build up your application's infastructure using Varrgrant for Vagrant.
By combining these various definitions together, you can really setup a fully fledged application stack with multiple servers talking to each other, and deploying on the AWS platform.

I hope you enjoy using Varrgrant for Vagrant as much as I do. There are a lot of other secret gems hidden in all the various cookbooks that are waiting to be taking advantage of. Definitely take some time to learn chef basics, and learn how to override attributes, as well as develop your own cookbooks for your use cases.

> Have fun, and Yarr! -_O
>
> Pirate Dunbar
