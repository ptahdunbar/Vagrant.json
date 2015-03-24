# Vagrant.json

> Vagrantfile can get pretty messy with bigger projects. Vagrant.json simplifies VM configuration/setup for individuals and team projects.

## Requirements
* [VirtualBox](https://www.virtualbox.org/) or [AWS account](http://aws.amazon.com/) or [DigitalOcean account](http://digitalocean.com/).
* Latest version [Vagrant](http://www.vagrantup.com/).

# Installation

```
git clone git://github.com/ptahdunbar/Vagrant.json.git new-project && cd new-project
vagrant up
```

# Getting started
* Customize `Vagrant.json` with your VM settings (see the example files for ideas).
* `vagrant up`

## What is Vagrant.json?
`Vagrant.json` provides a json key/value structure to configure your boxes without having to mess with `Vagrantfile`.


## Vagrantfile configuration

- Settings should be self-explanatory and should mirror the official vagrant API.
- This is an example `Vagrant.json` file with all available settings.
- Please note that you probably don't need all these configured. This is just an example to copy/paste from.

```
[{
	"hostname": "vagrant.dev",
	"box": "ubuntu/trusty64",
	"scripts": [
		"ops/provisioning/base.sh"
	],
	"github_ssh_keys": [
		"github-username",
		"another-user"
	],
	"forwarded_ports": [
		{ "host": "3000", "guest": "3000" },
		{ "host": "8000", "guest": "80" }
	],
	"synced_folders": [
		{
            "host" : ".",
            "guest" : "/srv",
            "group": "www-data",
            "mount_options": [
                "dmode=775",
                "fmode=667"
            ]
        }
	],
	"settings": [
		"ssh_username": "root",
		"ssh_host": "10.10.10.100",
		"ssh_port": 22,
		"private_key_path": "~/.ssh/id_rsa",
		"insert_key": true,
		"disable_default_synced_folder": false,
		"cpus": 1,
		"memory": 512,
		"gui": false
	],
	"aws": {
		"access_key_id" : "xxxxxxxxxxxx",
		"secret_access_key" : "xxxxxxxxxxxx",
		"keypair_name" : "xxxxxxxxxxxx",
		"subnet_id" : "xxxxxxxx",
		"private_key_path" : "~/.ssh/id_rsa",
		"username" : "ubuntu",
		"security_groups" : [ "default" ],
		"ami" : "ami-9a562df2",
		"region" : "us-east-1",
		"instance_type" : "m3.medium",
		"elastic_ip" : false
	},
	"digital_ocean": {
		"token": "xxxxxxxxxxxxxxxxxxxxxxxx",
		"private_key_path": "~/.ssh/id_rsa",
		"username": "root",
		"image": "ubuntu-14-04-x64",
		"region": "nyc2",
		"size": "512mb",
		"ipv6": false,
		"private_networking": false,
		"backups_enabled": false,
		"setup": true
	}
}]
```

See [Vagrantfile](https://github.com/ptahdunbar/Vagrant.jsonfile/blob/master/Vagrantfile) for all configuration settings.

### Example Vagrant.json file for AWS
Create a new EC2 instance on the AWS platform.

```
vagrant up --provider=aws
```

Required definitions:

- username
- keypair_name
- private_key_path

#### Example definition

```
[
    {
        "hostname": "vagrant",
        "aws": {
            "access_key_id": "xxxxxxxxxx",
            "secret_access_key": "xxxxxxxxxx",
            "username": "ubuntu",
            "keypair_name": "my-aws-keypair",
            "private_key_path": "~/.ssh/my-aws-keypair.pem"
        }
    }
]
```

### Recommendations

- Read up on [all available configuration settings](https://github.com/mitchellh/vagrant-aws) for vagrant-aws.
- Consider setting `elastic_ip` to true for more longer lived instances.
- Use your `subnet_id` to boot up smaller instances outside of your VPC.
- Consider adding your AWS credentials to your `.bashrc` or `.zshrc` file and remove it from `Vagrant.json`.
- [Check out the AWS documentation](http://aws.amazon.com/ec2/instance-types/) on various instance types available.


### Example Vagrant.json file for Digital Ocean

```
vagrant up --provider=digital_ocean
```
Required definitions:

- token
- private_key_path

#### Example definition

```
[
    {
        "hostname": "vagrant",
        "digital_ocean": {
            "token": "xxxxxxxxxx",
            "private_key_path": "~/.ssh/my-do-keypair.pem"
        }
    }
]
```

### Recommendations:

- Read up on [all available configuration settings](https://github.com/smdahlen/vagrant-digitalocean) for vagrant-digitalocean.

### Defining multiple VMs

Here's an example Vagrant.json config for 3 VMs:

```
[
    {
        "hostname": "vagrant",
        "github_ssh_keys": "githubusername",
        "synced_folders": [
            {
                "host" : ".",
                "guest" : "/srv",
                "group": "www-data",
                "mount_options": [
                    "dmode=775",
                    "fmode=667"
                ]
            }
        ],
        "scripts": [
            "provision-nginx.sh",
            "provision-php.sh"
        ]
    },
    {
        "hostname": "db",
        "settings": {
            "disable_default_synced_folder": true
        },
        "forwarded_ports": [
            { "host": "3306", "guest": "3306" }
        ],
        "scripts": "provision-db.sh"
    },
    {
        "hostname": "nodeapp",
        "forwarded_ports": [
            { "host": "3000", "guest": "3000" }
        ],
        "synced_folders": [
            {
                "host" : "node-app",
                "guest" : "/var/www"
            }
        ],
        "scripts": "provision-node.sh"
    }
]
```

## Vagrant.local.json

Optionally create a `Vagrant.local.json` (which is private and .gitignore) for testing/debugging or when working in a team.

## Vagrant plugins
Vagrant.json To make your development workflow easier comes bundled with the following [vagrant plugins](https://github.com/mitchellh/vagrant/wiki/Available-Vagrant-Plugins):

* [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier) - Speeds up apt-get.
* [vagrant-exec](https://github.com/p0deje/vagrant-exec) - Run one-liner commands into a machine from the host
* [vagrant-pristine](https://github.com/fgrehm/vagrant-pristine) - Adds pristine command to vagrant.
* [vagrant-dns](https://github.com/BerlinVagrant/vagrant-dns) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-awsinfo](https://github.com/johntdyer/vagrant-awsinfo) - Retrieve AWS data about your instance.
* [vagrant-aws](https://github.com/mitchellh/vagrant-aws) - Test/deploy to your amazon AWS account.
* [vagrant-digitalocean](https://github.com/smdahlen/vagrant-digitalocean) - Test/deploy to your Digital Ocean account.
* [vagrant-managed-servers](https://github.com/tknerr/vagrant-managed-servers) - Test/deploy to any VPS server via SSH.

## Troubleshooting

```
VPCResourceNotSpecified => The specified instance type can only be used in a VPC. A subnet ID or network interface ID is required to carry out the request.
```
   - Navigate to https://console.aws.amazon.com/vpc/
   - Click on the left navigation menu "Subnets"
   - Use any of the "Subnet ID" identifiers in the table list.

```
No host IP was given to the Vagrant core NFS helper. This is an internal error that should be reported as a bug.
```
   - Remove any NFS flags from your shared_folders definition in order to use AWS

## Getting Help

[Submit an issue](https://github.com/ptahdunbar/PressVarrs/issues/new) and I can provide further assistance.

> Yarrty yarr matey -_O
>
> Pirate Dunbar
