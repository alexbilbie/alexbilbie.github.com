---
title: "Version 4 of league/oauth2-server is out!"
layout: post
date: "2014-11-08 18:06:00"
tags: [oauth, php]
---

After almost a year in development I've just tagged version 4.0.0 of [league/oauth2-server](https://github.com/thephpleague/oauth2-server).

Back in the summer [I wrote about some of the problems I've faced](/2014/08/open-source-guilt/) during its development, but I've finally got there.

So what's new about v4?

**First of all it's a complete rewrite from scratch**. Theres some old saying about never rewriting software, but this is a component that to be used in a larger system, and good software practise involves [sacrificing and upgrading modular components](http://martinfowler.com/bliki/SacrificialArchitecture.html) to improve the overall stack.

At the code level I've updated everything to pass around objects which are much easier to test and validate than arbitrary key value arrays. This will also mean that code documentation generated with phpDocumentor or ApiGen will be much more useful.

Because almost all of the underlying objects have interfaces you can swap in your own key generator and create your own tokens as well as creating your own grant types much more easily than before.

In addition I've made use of Symfony's Request class from `symfony/http-foundation` (which just about every framework uses) to parse and validate server requests which saves developers having to manually pass through request variables which was required in older versions.

[The server now emits events](http://oauth2.thephpleague.com/authorization-server/events/) which can be used to further integrate the library into your own application. For example the `session.owner` event is fired when a session has been allocated an owner - you might want to listen to this event to automatically add extra scopes (permissions) if the owner is an administrator. All of the events stuff is powered by the lovely `league/events` by Frank de Jonge.

All the code has been tested on the latest versions of PHP and HHVM too.

Finally, and probably most importantly there is some extensive documentation which can be found here - [http://oauth2.thephpleague.com](http://oauth2.thephpleague.com).

I'd like to thank everyone who has contributed to v4's development - especially [Luca Degasperi](https://twitter.com/lucadegasperi) and [Jason Lewis](https://twitter.com/jasonclewis) and all the guys at the [PHP League of Extraordinary Packages](http://thephpleague.com) for their support, nagging and bug fixes.
