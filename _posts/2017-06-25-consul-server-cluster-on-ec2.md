---
title: "How to setup a Consul server cluster on EC2 in four easy steps"
layout: post
tags: [devops]
---

Over the past 18 months or so I've tried to deploy a [Consul](https://consul.io) server cluster on EC2 several times. Ironically though for a service discovery service I've always had difficulty with Consul nodes failing to discover each other reliably.

The two most common solutions to node discovery have been to use known private IP addresses (by assigning pre-created Elatic Network Interfaces in an instance's user-data script) or putting the Consul autoscaling group behind an internal Elastic Load Balancer. In both cases this adds unnecessary complexity and costs with the load balancer.

Today I discovered that in Consul 0.7.1 new configuration options were added to allow bootstrapping by automatically discovering AWS instances with a given tag key/value at startup. This is game changing because the hard work is done for you - all you need to do is ensure all of the Consul server instances share a tag and are able to communicate with one another.

To run your own Consul cluster you just need to follow these steps:

0. Create an IAM role with the following policy:
    ```json
    {
    "Version": "2012-10-17",
    "Statement": [
        {
        "Sid": "",
        "Effect": "Allow",
        "Action": "ec2:DescribeInstances",
        "Resource": "*"
        }
    ]
    }
    ```
0. Create an EC2 security group that the Consul server instances and any other instances that need to communicate with the Consul servers can reside in.
0. Create another EC2 security group for just the Consul server instances with the following ingress rules (set the source to be the other security group you created):
    * TCP 8300 (Server RPC)
    * TCP 8301 (Serf LAN)
    * UDP 8301 (Serf LAN)
    * TCP 8302 (Serf WAN)
    * UDP 8302 (Serf WAN)
    * TCP 8400 (CLI RPC)
    * TCP 8500 (HTTP API)
    * TCP 8600 (DNS)
    * UDP 8600 (DNS)  
0. Finally I used the following launch configuration to download and install the Consul binary, then create and load an Upstart script to run Consul in server mode and auto-discover other instances based on the name of the autoscaling group (you could also use this script to bake an AMI):
    ```bash
    #!/bin/sh
    curl -O https://releases.hashicorp.com/consul/0.8.4/consul_0.8.4_linux_amd64.zip
    unzip consul_0.8.4_linux_amd64.zip
    rm -f consul_0.8.4_linux_amd64.zip
    mv consul /usr/local/bin

    cat <<EOF > /etc/init/consul.conf
    description "Consul"
    author      "Alex Bilbie"

    start on filesystem or runlevel [2345]
    stop on shutdown

    script

        /usr/local/bin/consul agent \
            -server \
            -data-dir=/tmp/consul \
            -client=0.0.0.0 \
            -datacenter=AWS_REGION \
            -bootstrap-expect=3 \
            -ui \
            -retry-join-ec2-tag-key=aws:autoscaling:groupName \
            -retry-join-ec2-tag-value=NAME_OF_AUTOSCALING_GROUP

    end script
    EOF

    initctl reload-configuration
    initctl start consul
    ```

The autoscaling group that runs the launch configuration needs a min, max and desired count of 3 to maintain the quorum.

If you SSH into any of the Consul servers and run `/usr/local/bin/consul members` you should see the three instaces listed out like so:

```
Node           Address          Status  Type    Build  Protocol
ip-10-0-1-122  10.0.1.122:8301  alive   client  0.8.4  3
ip-10-0-2-12   10.0.2.12:8301   alive   server  0.8.4  3
ip-10-0-2-45   10.0.2.45:8301   alive   server  0.8.4  3
```

If you expose port 8500 to your IP address in the security group and visit [http://server-public-ip:8500/ui](http://server-public-ip:8500/ui) you'll be able to interact with the build in web interface.

I hope you'll agree that it really is very simple now to get a Consul cluster up and running on EC2 without the need to mess around with network interfaces or load balancers. The configuration described above is built with high availability in mind (assuming your auto-scaling group is launching across availability zones) and is self-healing should any of your instances fail.