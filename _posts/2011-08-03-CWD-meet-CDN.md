---
layout: default
published: true
title: "CWD, meet CDN"
date: "2011-08-03 09:00:00"
---

This morning there was a massive outage at the university’s Brayford campus, apparently caused by some of the builders on campus accidentally cutting an important power cable. As a result some of our servers died including the server hosting the Common Web Design (CWD); meaning tens of sites were left without CSS, JavaScript and images. I made the decision that now was an appropriate day to finally move the CWD onto Rackspace Cloud Files CDN.

Recently we’ve started making heavy use of Git (on Github) to manage the source code for our sites and services (which has been great because Nick and I can no longer overwrite each other’s code…as well as all the other advantages Git gives us). The CWD has now got it’s own (private) Github repository too and I’ve written a quick crontab script which is executed few minutes to `git pull` the latest CWD commits, then using the Rackspace Cloud Files PHP API sync any changes with the CDN container (container = a folder with it’s own unique domain name).

It works great however there are a few things to consider if you build a similar tool. We’ve got the the CDN container with a TTL of 168 hours which means that content served to browsers has an expiry of 7 days, and the CDN edges (edges = the individual servers around the world that make up the CDN) will check for new content every 7 days. The downside of this is that any changes to CWD won’t be reflected for upto 7 days.

Fortunately Rackspace Cloud Files now supports “edge purge” which is basically a command that tells all the edges to go and look for new content immediately. Every time we update or delete a file from the container this edge purge command is issued and within half an hour (the CDN has thousands and thousands of servers to update…!) the new/updated content will be served.

In terms of speed increases CDN served CWD is noticeably faster on mobile devices, and on desktop browsers in Firebug the content onload is ready within 400ms on my home connection (vs 800ms for non CDN served CWD). To test this for yourself visit http://cwd.online.lincoln.ac.uk/3.0/ for the non CDN version, and http://cwd.online.lincoln.ac.uk/3.0/cdn.html for the CDN served version.
To top it off we’re looking at about £2.00/month in hosting costs too!