---
layout: post
published: true
title: "PHP 5.3/5.4 for OS X 10.6/10.7/10.8 as binary package"
permalink: /2012/12/php-for-osx/
---

The wonderful people at [Liip](http://liip.ch/) have created [binary packages for PHP 5.3, 5.4 and 5.5 for OS X Snowy, Lion and Mountail Lion](http://php-osx.liip.ch/).

Install PHP 5.4 is as simple as running the following in your terminal:

	curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4
    
To make this new version of PHP your system default, edit your system path so that /usr/local/php5/bin is before wherever the default PHP location is (find that out with `which php`).

Remember to read the install full notes on the web page.

[➝ http://php-osx.liip.ch/](http://php-osx.liip.ch/)