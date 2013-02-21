---
layout: default
published: true
title: Making Apache and SELinux play nice
permalink: /2013/02/making-apache-selinux-play-nice/
---

We spent far too many hours trying to solve a problem on a brand new RHEL6 box today. Basically Apache would only return 403 errors no matter what file we tried to hit up in a browser. The error log wasn't much help either, even on the highest log level.

It turned out that SELinux was preventing Apache from accessing the files. The command to get around this is:

<pre><code data-language="shell">chcon -Rv -t httpd_sys_content_t /path/to/web/root</code></pre>

I don't know much about SELinux but as I understand it that command should be persistant across server reboots. Also it seems you don't need to reapply it each time you create a new file in the webroot either.

There is some useful infomation about working with SELinux on this [CentOS wiki page](http://wiki.centos.org/HowTos/SELinux).