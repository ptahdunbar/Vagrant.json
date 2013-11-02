# Varrgrant

> Bootstrap your local development environment with first-class support for vagrant and chef.

## Requirements
* Latest version vagrant; [http://vagrantup.com](http://www.vagrantup.com/)
* Berkshelf; `sudo gem install berkshelf`

Run these commands in the project's working directory.

```
vagrant plugin install bindler
vagrant plugin bundle
```

# Usage

* Configure your nodes.
    * `cp boxes-sample.json to boxes.json`
* Set the base vagrant box to use
    * `vim +24 Vagrantfile`
* Install/Update chef cookbooks
    * `berks install`
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

## What is Boxes.json?
Boxes.json is vagrant sugar to help make setting up multi-node environments super clear and simple.

### Boxes Format

```
[
	{
		"name": "vagrant",
		"ip": "10.10.10.12",

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
		"synced_folder": [
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
			"recipe": [
				"recipeOne::default",
				"recipeTwo::default",
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
		"ip": "10.10.10.11",
	}
]
```
