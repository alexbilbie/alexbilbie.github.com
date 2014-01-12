---
title: "Using Vagrant and Ansible for distributing educational course virtual machines"
date: "2013-12-19 11:50:00"
layout: post
published: true
---


[Tony Hirst](http://blog.ouseful.info/2013/12/02/packaging-software-for-distance-learners-vms-101/) wrote an article on his blog recently that I'd somehow missed in my RSS reader but in any case I'd like to quickly respond to it.

In ["Cursory Thoughts on Virtual Machines in Distance Education Courses"](http://blog.ouseful.info/2013/12/02/packaging-software-for-distance-learners-vms-101/) Tony discuses using virtual machines in courses to help distribute software and operating systems to students who increasingly more and more are bringing in very different devices.

This is a common problem is development teams as well, in my current workplace two of us develop on OSX, another uses Ubuntu and our designer uses Windows; therefore it is important for us to have a development environment that works for all of us regardless of the operating system we use.

At the very end of the article Tony mentions using [Vagrant](http://vagrantup.com/) which is a small piece of software to help "create and configure lightweight, reproducible, and portable development environments". Essentially you write a small document in Ruby called a _Vagrantfile_ to describe a virtual machine and then Vagrant will download, configure and spin-up that virtual machine in Virtualbox or VMWare or other Virtualisation software.

A Vagrantfile looks like this:

```ruby
Vagrant.configure("2") do |config|

    config.vm.box = "lucid32"
    config.vm.box_url = "http://files.vagrantup.com/lucid32.box"
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "private_network", ip: "192.168.100.10"

    config.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--memory", "512"]
    end

end
```

Inside the folder that has the Vagrantfile you just type `vagrant up` and Vagrant will do the following (based on the above config):

1. Download a Vagrant box from <http://files.vagrantup.com/lucid32.box> (a Vagrant box is essentially a gzipped .img file with it's own Vagrantfile describing the box).
2. Save the downloaded box with the name "lucid32" - if in another Vagrantfile you reference "lucid32" it will use the box that has already been downloaded.
3. Configure the virtual machine to have 512mb of memory, port forward the guest port 80 to the host's port 8080 and then give it a private IP address of 192.168.100.10.
4. Boot the virtual machine.

Tony also discusses pre-configuring the virtual machines before distributing them because some students won't be as au fait with installing software on Linux machines. This is certainly one option but I think in the long run academics would find it tedious trying to create different virtual machines with slight changes and having to store different images for each course.

A better option would be to use another [devops](http://en.wikipedia.org/wiki/DevOps) tool called [Ansible](http://www.ansibleworks.com/) which "is a powerful automation engine that makes systems and apps simple to deploy".

Ansible uses [YAML](http://en.wikipedia.org/wiki/YAML) files to describe a list of tasks that should be executed on a machine. For example if you wanted to do the following:

1. Update apt
2. Install PHP + Apache
3. Open port 80 in the firewall
4. Start Apache

You could express it like this:

```yaml
---

- hosts: webserver
  user: vagrant
  sudo: True

- name: update apt
  command: /usr/bin/apt-get update

- name: install web server packages
  action: apt pkg=$item state=installed
  with_items:
  	- httpd
  	- php

- name: allow port 80
  command: /usr/sbin/ufw allow http

- name: flush firewall
  command: /usr/sbin/ufw reload

```

This YAML file (called a "playbook" in Ansible terminology) can be [automatically executed by Vagrant](http://docs.vagrantup.com/v2/provisioning/ansible.html) to ["provision"](http://en.wikipedia.org/wiki/Provisioning#Server_provisioning) the virtual machine.

Ansible playbooks can include other playbooks so an academic can have a playbook of common tasks and then have individual playbooks with slight variations for different courses.

Together the playbooks and the Vagrantfile can be distributed to the students and assuming they've installed Vagrant and Virtualbox for their OS they can be up and running with identical environments in just a few minutes.

At my workplace we use the same Ansible playbooks for both local development and provisioning our staging and production servers - together with similarly configured Vagrant boxes and VMWare vCloud (our cloud environment) images we've almost entirely removed the old problem of "but it works on my machine".

As well as teaching students a version control system and how to use the command line I think it is vital that all computer science students finish their courses with familiarity of devops tools like Virtualbox, Vagrant, Ansible, and even tools like [Veewee](https://github.com/jedi4ever/veewee) which help build custom Vagrant boxes.
