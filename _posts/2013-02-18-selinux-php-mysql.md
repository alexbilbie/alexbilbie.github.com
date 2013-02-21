---
layout: default
published: true
title: "Tell SELinux to let PHP connect to a remote MySQL database"
permalink: /2013/02/selinux-php-mysql/
---

I've just wasted two hours trying to work out why I couldn't connect to a remote MySQL database server from PHP, but connecting on the command line works fine on a RHEL6 box.

Turns out it was effin' SELinux again ([see previous woes here](http://alexbilbie.com/2013/02/making-apache-selinux-play-nice/)).

Setting the following values (as root) makes it all suddenly work fine:

<pre><code data-language="shell">setsebool -P httpd_can_network_connect 1
setsebool -P httpd_can_network_connect_db 1
</code></pre>