---
title: "An inspired journey into microservices architecture"
date: "2015-03-15 10:11:00"
layout: post
---

Sometime this week I came across a series of blog posts by the taxi company Hailo about how their architecture has changed over time from a simple PHP/Java API to a global architecture with over 150 services powering their consumer and operational apps.

The three blog posts can be found here:

* [Part one](https://sudo.hailoapp.com/services/2015/03/09/journey-into-a-microservice-world-part-1/)
* [Part two](https://sudo.hailoapp.com/services/2015/03/09/journey-into-a-microservice-world-part-2/)
* [Part three](https://sudo.hailoapp.com/services/2015/03/09/journey-into-a-microservice-world-part-3/)

I love [this blog post](https://sudo.hailoapp.com/web/2014/12/08/webapps-as-microservices/) too.

I've been very slowly orientating my stack at work (more on that soon) to be made up of a number of discrete services, but since reading the posts above I've become a little bit obsessed with understanding at an even deeper level how to properly build and orchestrate a whole raft of services to power our mobile apps.

At this point I've come to two conclusions. First what I've made works really well so far and I don't want to disrupt that unnecessarily. Also any futher splitting out of application functions into induvidual services needs to be done at a rate which is managable for the resources we have available at the time and also carefully because we're about to launch our newest app into production.

My second conclusion is I really want and need to add another language to my toolbelt. I've worked with PHP for years now; I'm fast and effective with it but my frustration with the direction of the language _vs_ what I want from it (namely strict scalar type hints and types), it's lacklustre ability to run long running processes and poor concurrency support mean it's starting to work against my needs. I've been playing on and off with other languages and Go and has really captured my imagination so my challenge now is to find the time to learn as much as I can about Go and if I'm confident with this new approach then appropriately intoduce it into our stack.
