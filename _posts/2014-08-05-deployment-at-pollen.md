---
title: "Deployment at Pollen"
layout: post
date: "2014-08-04 10:30:00"
---

During my first few weeks at [Yuza](http://yuza.com) I've been working on one of our latest projects called [Pollen](http://pollen.vc/) which advances app developers' sales income every 7 days (rather than waiting up to 60+ days for Apple and Google to pay out).

The platform is hosted on AWS and I've spent some time perfecting our initial deployment process so that we can deploy safely and securely throughout the day.

This is our current setup:

0. A developer works on a project locally, using provisioned Vagrant boxes which emulate the live environment.
0. Code is merged into the `develop` branch using pull requests on Github.
0. Our Jenkins box runs unit and behavioural tests against the `develop` branch when a change occurs.
0. If everything is sound the code is merged into the `master` branch by Jenkins.
0. Another Jenkins job watches for changes to the `master` branch. This job runs futher sanity checks, then pulls down dependencies, minifies assets and creates an archive which is pushed to an S3 bucket.
0. Jenkins then tells AWS to spin up a new instance in a auto-scaling group (specific to the application type). The auto-scaling group uses a launch configuration which has the following "user-data" script which is executed when the instance spins up:<script src="https://gist.github.com/alexbilbie/9ca5842e2284717b0ede.js"></script>The script installs [Ansible](http://ansible.com) and some other basic system utitilies, and automagically sets the hostname of the box using the AWS API. It also downloads pre-made SSH credentials which have specifc permissions to read certain Github repositories, one of which is our main devops repository. In this codebase there are shell scripts which will be executed depending on the hostname of the instance. These shell scripts will finalise the provisioning of the instance.
0. When the instance is provisioned it downloads the latest code from the archive on S3, and is finally added to the load balancer. Jenkins will one by one terminate any other instances in the auto-scaling group which will in turn spin up new instances and eventually all instances in the group are running the latest codebase and setup.

These processes allows deploys to happen in a little under 5 minutes, and we'be designed all applications to be stateless so that they can be elastically scaled up and down as required.

For now this setup works well for now however there are some things we'd like to change.

Currently each new instance has to be provisioned from scratch which can be really slow if there is lots to install and setup; my plan is to get to grips with [Packer](http://packer.io). This will allow us to bake AWS AMI images seperately with all the appropriate software installed and configured. New instances will use these AMIs when they spin up, and so all that is required is to download the latest code archive from S3. Hopefully this should reduce the deploy time to no more than a minute or two.

I'm also going to keep my eye on [Terraform](http://terraform.io/) which landed last week and is basically a way of describing a provider-agnositc cloud setup. This will then allow us to keep our code, provisioning scripts and cloud setup all in version control.

It's worth spending time examining and refining your deployment setup; it can significantly reduce onboarding time for new developers joining your team, and automating your deployment process improves productivity by reducing the number of repetative tasks required to ship your code.