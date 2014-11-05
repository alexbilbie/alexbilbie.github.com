---
layout: post
date: "2014-11-05 15:33:00"
title: "Speed up Internet access in Vagrant boxes"
---

This afternoon I've been playing with the Facebook API in a Vagrant box but requests to the API were immensely slow.

The following request would take `5.5221911907196s` inside Vagrant:

```php
<?php
$t = microtime(true);
file_get_contents('https://graph.facebook.com/v2.2/me?access_token={{access token}}');
var_dump(microtime(true) - $t);
```
But on my rMBP the same request took just `0.16792297363281s`.

After a bit of Googling around I found that you can tell Virtualbox (through the `Vagrantfile`) to use the host as the DNS resolver:

```ruby
config.vm.provider "virtualbox" do |v|
  v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
end
```

After a quick `vagrant reload` the request took just `0.15842008590698s`
