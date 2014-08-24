---
layout: post
title: "Setting up a Vagrant machine for Grails development"
date: 2014-02-12 23:21:27 +0100
comments: true
description: "How to setup a Vagrant machine for Grails development"
categories: vagrant grails
---

Recently I've tried several times to integrate [Vagrant](http://www.vagrantup.com/) in my development flow, without success. So, now that I have to start
Grails development I decided to setup a development enviroment first.

I used Vagrant with [Virtualbox](https://www.virtualbox.org/) and [Puppet](http://puppetlabs.com/) for provisioning.

For the impatient, you can [clone this repo](https://github.com/JustB/vagrant-grails) and go with `vagrant up` to get a fully functional grails
environment. If you want to know what's inside, keep on reading.

## Initial setup

First install Vagrant and Virtualbox. Then create a folder and initialize the Vagrant machine:

```
$ mkdir vagrant_grails && cd vagrant_grails
$ vagrant box add base http://files.vagrantup.com/precise32.box
$ vagrant init base
```

Then open the newly created `Vagrantfile` and add these lines (or uncomment the relevant ones):

```
config.vm.provision :puppet do |puppet|
  puppet.manifests_path = "manifests"
  puppet.manifest_file  = "default.pp"
  puppet.module_path = "modules"
end
```

Vagrant will look for a `default.pp` file under the `manifests` folder and for any missing modules
under the `modules` folder. So let's create them:

```
$ mkdir modules
$ mkdir manifests
$ touch manifests/default.pp
```

## Installing required Puppet modules

We'll need some modules from the Puppet forge: specifically [apt](https://forge.puppetlabs.com/puppetlabs/apt) and [stdlib](https://forge.puppetlabs.com/puppetlabs/stdlib). You can clone them
under the `modules` folder, or you can download the archives and extract them. It's up to you.

## Installing Java

We're ready to start. We need Java for Grails to work, so let's get this settled. Unfortunately
since Java was acquired from Oracle it isn't available as a package in Ubuntu repositories. So we'll
need to add a ppa that has a package that will let us download the Java installer. The problem is,
this package require us to accept a license agreement. So we'll need to automatically accept it.
Write this in the `default.pp`:

```
class grails {
  include apt
  apt::ppa { "ppa:webupd8team/java": }

  exec { 'apt-get update':
    command => '/usr/bin/apt-get update',
    before => Apt::Ppa["ppa:webupd8team/java"],
  }

  exec { 'apt-get update 2':
    command => '/usr/bin/apt-get update',
    require => [ Apt::Ppa["ppa:webupd8team/java"], Package["git-core"] ],
  }

  package { ["vim",
             "curl",
             "git-core",
             "bash"]:
    ensure => present,
    require => Exec["apt-get update"],
    before => Apt::Ppa["ppa:webupd8team/java"],
  }

  package { ["oracle-java7-installer"]:
    ensure => present,
    require => Exec["apt-get update 2"],
  }

  exec {
    "accept_license":
    command => "echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections && echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections",
    cwd => "/home/vagrant",
    user => "vagrant",
    path    => "/usr/bin/:/bin/",
    before => Package["oracle-java7-installer"],
    logoutput => true,
  }
}

include grails
```
The task are very simple: we update the repositories, add the webupd8team ppa, then we update the
repositories again. Then we install the Java installer and set the flag to accept the license,
without it asking us. In this way we'll get a fully automated installation.

## Installing Grails

Now we need to install Grails. The steps are very similar: we'll add a PPA (as it is stated in the
[Grails docs](http://grails.org/download/ubuntu)) and install the package. We'll also need to set the `JAVA_HOME` environment variable
that is needed by Grails. Here is the full Puppet manifest:


```
class grails {
  include apt
  apt::ppa { "ppa:webupd8team/java": }
  apt::ppa { "ppa:groovy-dev/grails": }

  exec { 'apt-get update':
    command => '/usr/bin/apt-get update',
    before => Apt::Ppa["ppa:webupd8team/java"],
  }

  exec { 'apt-get update 2':
    command => '/usr/bin/apt-get update',
    require => [ Apt::Ppa["ppa:webupd8team/java"], Apt::Ppa["ppa:groovy-dev/grails"], Package["git-core"] ],
  }

  package { ["vim",
             "curl",
             "git-core",
             "bash"]:
    ensure => present,
    require => Exec["apt-get update"],
    before => Apt::Ppa["ppa:webupd8team/java"],
  }

  package { ["oracle-java7-installer"]:
    ensure => present,
    require => Exec["apt-get update 2"],
  }

  exec {
    "accept_license":
    command => "echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections && echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections",
    cwd => "/home/vagrant",
    user => "vagrant",
    path    => "/usr/bin/:/bin/",
    before => Package["oracle-java7-installer"],
    logoutput => true,
  }

  package { ["grails-ppa"]:
    ensure => present,
    require => Package["oracle-java7-installer"],
  }

  exec { "add_java_home":
    command => '/bin/echo "export JAVA_HOME=/usr/lib/jvm/java-7-oracle" >> /home/vagrant/.bashrc',
  }
}

include grails
```

## Starting the machine

Now you only need to start your machine.

```bash
$ vagrant up
$ vagrant ssh
```
