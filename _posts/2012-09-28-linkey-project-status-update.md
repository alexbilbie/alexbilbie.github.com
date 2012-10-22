---
layout: post
permalink: /2012/09/linkey-project-status-update
title: Linkey Project Status Update 1
---

We're now a quarter of the way through the Linkey project calendar so I'm going to try and summarise where we are at and where the project is heading.

I've spent a considerable amount of time working away on the [OAuth PHP library](https://github.com/lncd/oauth2) which features code to build an authorisation platform, a resource server, and an abstract client class so that you can easily interact with 3rd party OAuth endpoints.

With this code almost finished and tested I've started designing the next generation OAuth endpoint for use here at the university. Coupled with the work we're doing on the side for our next generation API and open data platform, as well as ICT's own enterprise service bus and [UAG](http://linkey.blogs.lincoln.ac.uk/tag/uag/) development all together we're developing a beautiful data driven environment that will allow us to go forth and build rich, personalised and secure services and applications.

My colleagues here in ICT have been testing the UAG on our development network over the past few months and they now feel comfortable to start building a production platform which we can start integrating services into. This will hopefully be ready in the next few weeks and I will be able to go into much more detail about how OAuth and UAG can work together.

The main clients for Linkey are the university library and they have already started reaping some of the benefits of a single sign on environment thanks to an installation of [EZProxy](http://www.oclc.org/ezproxy/) which offers reverse proxy authentication to a large number of 3rd party journal and database services. Currently EZProxy is piggybacking off of our Blackboard installation for authentication which benefits students - we know from our analytics that most students access library services through Blackboard - because there are now 35 (at the time of writing) resources which they can access with one click and they won't have to authenticate a second (or in some cases a third) time.

With a production UAG we should be able to hook up a large percentage of sites and services with relative ease. As a refresher here is the current authentication situation:

![https://raw.github.com/lncd/AIM-project/master/SSOCurrentSituation.png](https://raw.github.com/lncd/AIM-project/master/SSOCurrentSituation.png)

and here is the ideal situation which we should be able to achieve:

![https://raw.github.com/lncd/AIM-project/master/SSOIdealSituation.png](https://raw.github.com/lncd/AIM-project/master/SSOIdealSituation.png)

Over the next 9 months there are a few other areas of access and identity which I want to cover.

At EduServ's [Federated Access Management](http://www.eduserv.org.uk/newsandevents/events/fam11) conference last year there was a talk by Jonathon Richardson, assistant CIS director at the University of East Anglia where he discussed how students and staff can access some of his institutions' services with their own 3rd party Internet services accounts (e.g. Facebook/Twitter/Google). I would like to look into what are the benefits and risks associated with allowing staff and students to access university resources with their own accounts instead of the brand new account which they are given by us when they start at the university.

Hopefully the OAuth 2.0 specification will also be completed very soon and I want to further investigate OAuth's potential use cases. I have submitted a [conference talk proposal](http://confoo.ca/en/call-for-papers/speaker/alex-bilbie) to ConFoo in Montreal, Canada to discuss my findings.

We have also committed to publishing a case study and organising a workshop about OAuth (and access and identity in general) which we will work with JISC to organise and promote.