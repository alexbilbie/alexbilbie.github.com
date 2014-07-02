---
title: "Using Ansible with a bastion SSH host"
layout: post
date: "2014-07-02 10:30:00"
---

A common network security design pattern is to prevent any connections to your application servers from outside of their private subnet, and then using a [bastion host](http://en.wikipedia.org/wiki/Bastion_host) hosted in a [DMZ](http://en.wikipedia.org/wiki/DMZ_(computing)) to selectively whitelist traffic to the servers.

We have such a setup for one of our server pools through which we only allow SSH traffic from specific IP addresses. These servers are also provisioned via [Ansible](http://www.ansible.com/) which programatically configures servers via SSH.

Due to our bastion host setup Ansible is unable to talk directly to our application servers so I had to find a way to proxy SSH connections through the bastion host.

I love using Ansible for creating simple tasks to run, for example flushing memcache to clear caches. Running with this example this is my Ansible structure:

```
devops/
    ansible/
        roles/
            memcache/
                tasks/
                    main.yml
                    restart.yml
        tasks/
            restart-memcache.yml
        vars/
            production-memcache.yml
    bin/
        restart-production-memcache.sh
    hosts.ini
    ssh.config
    ansible.cfg
```

The `tasks/restart-production-memcache.sh` script looks like this:

```
#!/bin/sh
ssh-add ${DEPLOY_KEYS_DIR}/memcache-servers.pem

ansible-playbook -i ansible/hosts.ini -u ansible ansible/tasks/restart-memcache.yml -v
```

It is called from the root `devops` folder and first adds the servers SSH key too the SSH agent and then calls the `restart-memcache.yml` playbook which in turn includes the memcache role `restart.yml` playbook (as well as other performing other tasks).

In the `ssh.config` file I have the following SSH configuration set:

```
Host bastion
    User                   ec2-user
    HostName               ###.###.###.###
    ProxyCommand           none
    IdentityFile           /path/to/ssh/key.pem
    BatchMode              yes
    PasswordAuthentication no

Host *
    ServerAliveInterval    60
    TCPKeepAlive           yes
    ProxyCommand           ssh -q -A ec2-user@###.###.###.### nc %h %p
    ControlMaster          auto
    ControlPath            ~/.ssh/mux-%r@%h:%p
    ControlPersist         8h
    User                   ansible
    IdentityFile           /path/to/ssh/key.pem
```

From the `devops` folder I can run `ssh bastion -F ssh.config` and I will be immediately connected to the bastion server.

Next Ansible needs to be told to use this custom SSH config when connecting to the application servers.

In the `ansible.cfg` file there is the following configuration:

```
[ssh_connection]
ssh_args = -o ControlPersist=15m -F ssh.config -q
scp_if_ssh = True
control_path = ~/.ssh/mux-%%r@%%h:%%p
```

When Ansible runs from the `devops` folder it will automatically pick up this `ansible.cfg` file and use the defined config when playbooks are run.

One issue with this setup is that Ansible's output as it runs is very verbose as it includes the SSH debug connection information as it passes through the bastion host to connect to the application servers; I've not yet found a way to supress this.