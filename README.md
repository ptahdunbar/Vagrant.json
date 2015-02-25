# DevOps

> Bootstrap your application stack and maintain development/production parity. Inspired by [the 12factor app](http://12factor.net/) and the sheer pain of doing sysadmin work, ugh.

## Requirements
* [VirtualBox](https://www.virtualbox.org/) or [AWS account](http://aws.amazon.com/) or [DigitalOcean account](http://digitalocean.com/).
* Latest version [Vagrant](http://www.vagrantup.com/).

# Installation

```
git clone git://github.com/ptahdunbar/DevOps.git new-project
cd new-project
vagrant up
```

# Getting started
* Start by running the `vagrant` command or manually create a `devops.json` file.
* Next, fill out the `devops.json` file describing your environment (see the example files for ideas).
* `vagrant up`

## What is devops.json?
`devops.json` describes your network of nodes without having to mess with `Vagrantfile`.

### devops.json format
`devops.json` is a json file containing an array of machines for your environment.

See `Vagrantfile` for all configuration settings.

## Vagrant plugins
To make your development workflow easier, devops comes bundled with the following [vagrant plugins](https://github.com/mitchellh/vagrant/wiki/Available-Vagrant-Plugins):

* [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier) - Speeds up apt-get.
* [vagrant-exec](https://github.com/p0deje/vagrant-exec) - Run one-liner commands into a machine, without ssh'ing into it.
* [vagrant-pristine](https://github.com/fgrehm/vagrant-pristine) - Adds pristine command to vagrant.
* [vagrant-dns](https://github.com/BerlinVagrant/vagrant-dns) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater) - Updates your `/etc/hosts` file with machine IP addresses.
* [vagrant-awsinfo](https://github.com/johntdyer/vagrant-awsinfo) - Retrieve AWS data about your instance.
* [vagrant-aws](https://github.com/mitchellh/vagrant-aws) - Test/deploy to your amazon AWS account.
* [vagrant-digitalocean](https://github.com/smdahlen/vagrant-digitalocean) - Test/deploy to your Digital Ocean account.
* [vagrant-managed-servers](https://github.com/tknerr/vagrant-managed-servers) - Test/deploy to any VPS server via SSH.

> Yarrty yarr yarr matey -_O
>
> Pirate Dunbar
