# Varrgrant

> Bootstrap an OS stack with first-class support for vagrant and chef.

## Requirements
* Latest version vagrant; [http://vagrantup.com](http://www.vagrantup.com/)

Run these commands in the project's working directory.

```
vagrant plugin install bindler
vagrant plugin bundle
```

# Usage

* Configure your nodes.
    * `cp Varrgrant-sample.json to Varrgrant.json`
* Set the base vagrant box to use
    * `vim +24 Vagrantfile`
* Run vagrant
    * `vagrant up`

## Chef

* Varrgrant depends on [Berkshelf](http://berkshelf.com/) to support the management of chef cookbook dependencies.
* Be sure to register new cookbooks into the Berksfile.

### Bundled Cookbooks
* [MySQL](https://github.com/opscode-cookbooks/mysql)
* [database](https://github.com/opscode-cookbooks/database)
* [apache](https://github.com/opscode-cookbooks/apache)
* [nginx](https://github.com/opscode-cookbooks/nginx)
* [postfix](https://github.com/opscode-cookbooks/postfix)
* [xml](https://github.com/opscode-cookbooks/xml)
* [git](https://github.com/opscode-cookbooks/git)
* [subversion](https://github.com/opscode-cookbooks/subversion)
* [vim](https://github.com/opscode-cookbooks/vim)
* [nodejs](https://github.com/mdxp/nodejs-cookbook)
* [redisio](https://github.com/brianbianco/redisio)
* [base](https://github.com/ptahdunbar/base-cookbook)
* [mariadb](https://github.com/ptahdunbar/mariadb-cookbook)
* [php](https://github.com/ptahdunbar/php-cookbook)


## Vagrant plugins
Varrgrant comes bundled with these vagrant plugins

* [vagrant-exec](https://github.com/p0deje/vagrant-exec)
* [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier)
* [vagrant-omnibus](https://github.com/schisamo/vagrant-omnibus)
* [vagrant-berkshelf](https://github.com/riotgames/vagrant-berkshelf)
* [vagrant-pristine](https://github.com/fgrehm/vagrant-pristine)
* [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater)

## What is Varrgrant.json?
Varrgrant.json is vagrant sugar to help make setting up multi-node environments super clear and simple.

### Boxes Format

```
[
	{
		"hostname": [
			"varrgrant.dev"
		],
		"ips": ["10.10.10.100"],
		"chef" : {
			"recipes" : [
				//"recipes[cookbook]"
			],
			"json" : {

			}
		}
	}
]
```

```
[
	{
		"hostname": [
			"varrgrant.vm"
		],
		"ips": ["10.10.10.12"],

		"customize": {
			"memory": 1024,
			"cpu": 2,
			"gui": true
		},
		
		"hosts": [
			"vagrant.dev",
			"vagrant.lan"
		],
		"forward_port" : [
			{
				"guest": 80,
				"host": 8080
			},
			{
				"guest": 9000,
				"host": 3000
			}
		],
		"synced_folders": [
			{
				"host": "relative/on/host",
				"guest": "/abs/path/on/guest",
				"owner": "vagrant",
				"group": "www-data",
				"mount_options": [
					"fmode=777",
					"dmode=775"
				]
			}
		],
		
		"chef": {
			"recipes": [
				"recipesOne::default",
				"recipesTwo::default",
			],
			"json" : {
				"apache" : {
					"default_site_enabled": false
				}
			}
		}
	},
	{
		"name": "vagrant2",
		"ips": "10.10.10.11",
	}
]
```

### Varrgrant Format for AWS

`vagrant up --provider=aws`

```
[
	{
		"hostname": [
			"varrgrant.vm"
		],
		"synced_folders": [
			{
				"host": ".",
				"guest": "/vagrant",
				"id": "vagrant-root",
				"disabled": true
			}
		],
		"ami" : {
			"id" : "ami-a73264ce",
			"instance_type" : "t1.micro",
			"security_groups" : ["default"]
		},
		"chef" : {
			"recipes" : [
				//"recipes[cookbook]"
			],
			"json" : {

			}
		}
	}
]
```


# Varrgrant for a webserver
```
[[
 	{
 		"hostname": [
			"varrgrant.vm"
		],
 		"ips": ["10.10.10.100"],
 		"hosts" : [
 			"example.vm"
 		],
 		"synced_folders" : [
 			{
 				"host" : ".",
 				"guest" : "/srv/www/example.vm"
 			}
 		],
 		"chef": {
 			"recipes": [
 				"base",
 				"web_server"
 			],
 			"json": {
 				"web_server": {
 					"platform": "apache2",
 					"vhosts": [
 						{
 							"name": "example.vm"
 						}
 					]
 				}
 			}
 		}
 	}
 ]
```

Nginx
```
[
	{
		"hostname": [
			"varrgrant.vm"
		],
		"ips": ["10.10.10.200"],
		"hosts" : [
			"example.vm"
		],
		"synced_folders" : [
			{
				"host" : ".",
				"guest" : "/srv/www/example.vm"
			}
		],
		"chef": {
			"recipes": [
				"base",
				"web_server"
			],
			"json": {
				"web_server": {
					"platform": "nginx",
					"vhosts": [
						{
							"name": "example.vm"
						}
					]
				}
			}
		}
	}
]
```