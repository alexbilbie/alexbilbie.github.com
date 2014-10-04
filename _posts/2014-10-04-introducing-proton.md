---
title: Introducing Proton
date: "2014-10-04 17:30:00"
layout: post
---

The last few big projects I've worked on (mostly backend RESTful APIs) have been built with a combination of different packages loaded with Composer instead of building on top of a framework such as Laravel or Symfony.

My go to packages to build the core of each project for a while now have been the following:

* [Orno\Route](https://github.com/orno/route) for routing (it's built on Nikita Popov's [FastRoute](https://github.com/nikic/FastRoute)).
* [Orno\Di](https://github.com/orno/di) for dependency injection.
* [League\Event](https://github.com/thephpleague/event) for dispatching events.

For each app I would spend time bootstraping the packages together and then build on that foundation. The bootstrap code isn't particularly excessive or laborious to put together, but it has been repetitive.

So as I started on another project this week I spend some time finally building a micro "framework" called Proton - [https://github.com/alexbilbie/proton](https://github.com/alexbilbie/proton) which binds and exposes the packages above with one reusable component

Here is a simple example of how to use Proton with anonymous functions:

<script src="https://gist.github.com/alexbilbie/2ab3b3c23b025dd70032.js"></script>

And another with controllers:

<script src="https://gist.github.com/alexbilbie/234efa329cd0c8348dc0.js"></script>

Why did I build yet another micro framework? Why not just use Slim or Silex or some other micro framework?

Principally because I really like the particular packages above that I've already mentioned; I know their codebases very well and they're blazing fast. Also those other frameworks all offer features that I don't need; I like my code lean and mean and I don't want unnecessary overhead.

Proton contains __just enough__ code to bootstrap those package components together (similarly to how I have done on previous projects), and leaves me to make the fundamental decisions about architecture myself; I'm not constrained in any way. Also the underlying packages are all exposed so I can work with the router, container and event dispatcher individually (as apposed to having to talk to them through a facade on the `Proton\Application` object).

Aside from this blog post I don't plan on publicising Proton particularly; I've written it to help myself out and if others find it useful then that's great too.

This week I also discovered [StackPHP](http://stackphp.com) and it's completely changed the way I'm thinking about app development (and helped inform the design of Proton).

The principle of StackPHP is that you build up layers of classes that implement [HttpKernelInterface](https://github.com/symfony/symfony/blob/master/src/Symfony/Component/HttpKernel/HttpKernelInterface.php). Then each implementing class decorates another class so that you get several layers that pass around Request and Response objects in a common way.

For example, before your application runs you might want to:

1. Parse any cookies sent in the request
2. Validate a session cookie that may be present
3. Respond with a 403 if the session cookie is invalid and exit before the main application logic runs.

This is nicely demonstrated below by an onion image on the StackPHP website:

![http://stackphp.com/img/onion.png](http://stackphp.com/img/onion.png)

As long as each middleware (and the application itself) implements HttpKernelInterface you can literally stack these layers on top of each other and everything that follows will inherit and benefits from the preceding layers.

These middlewares can easily be with Proton too:

<script src="https://gist.github.com/alexbilbie/dda1ece33134adbf4bf6.js"></script>

The StackPHP website has a number of useful [middlewares built by the community](http://stackphp.com/middlewares/), as well as [some others](http://stackphp.com/toolbox/) that help with the decoration of the layers ([StackPHP/Builder](https://github.com/stackphp/builder)) and implement sessions in requests ([StackPHP/Session](https://github.com/stackphp/session)). There are also [four work-in-progress conventions and protocols](http://stackphp.com/specs/) that ensure interoperability between disparate middlewares.

I've already started on building a middleware [league/oauth2-server](https://github.com/thephpleague/oauth2-server) and I will include this in the very nearly complete version 4 release.
