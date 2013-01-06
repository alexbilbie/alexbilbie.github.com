---
title: What is OAuth?
layout: default
permalink: /2012/07/what-is-oauth/
---

The [OAuth website](http://oauth.org) describes OAuth as:

> An **open protocol** to allow **secure API authorisation** in a **simple** and **standard** method from desktop and web applications.

Essentially OAuth is a security protocol that enables users to grant third-party access to their web resources without sharing their passwords.

OAuth grew out of discussions between developers from Twitter and Ma.gnolia who wanted to authorise desktop applications to access their services. A working group was formed in 2007 to draft a proposal for an open standard. Developers from both Google and Yahoo also contributed to this work.

The first OAuth Core 1.0 draft was released in late 2007. In 2008 it was decided that the Internet Engineering Task Force (IETF) would adopt the specification to allow wider discussion and further standardisation work.

A minor revision ([OAuth 1.0 Revision A](http://oauth.net/core/1.0a/)) was published in June 2008 to fix a security hole. The OAuth 1.0 Protocol was published by the IETF OAuth Working Group in April 2010 as [RFC 5849](http://tools.ietf.org/html/rfc5849).

A number of Internet companies and services adopted OAuth 1.0 but it was considered too much of a pain in the arse to work with by developers because it involved complicated signatures being passed around and there were too many requests between clients and services.

In May 2010 work began on [version 2.0](http://oauth.org/2) of the OAuth protocol. Version 2.0 is not backwards compatible with OAuth 1.0a and focuses on developer simplicity. It also features more flows to allow the use of OAuth in more situations, as well as extensions to the core protocol to enable interoperability with assertion based protocols such as [SAML](http://en.wikipedia.org/wiki/Security_Assertion_Markup_Language).

OAuth has been adopted by many large Internet services and companies, here are some to name a few:

* Google (v2.0)
* Yahoo (v1.0a)
* Twitter (v1.0a and v2.0[^twitter_connect]))
* Github (v2.0)
* Microsoft (v2.0)
* Foursquare (v2.0)
* Salesforce (v2.0)
* Facebook (v2.0)

We have been using OAuth 2.0 here at the University of Lincoln since late 2011 where we investigated using it for the [Total ReCal project](http://totalrecal.blogs.lincoln.ac.uk) so that students here at the university could access their event data. Our implementation was based on [some work](https://github.com/alexbilbie/CodeIgniter-OAuth-2.0-Server) I'd already been doing in the area based in my own time.

We currently have over 30 application using OAuth to interact with our data sets including the Student Union website, the staff directory, [Orbital](http://orbital.lincoln.ac.uk), and four 3rd year student final projects.

In October 2011 I spoke at EduServ's [Federated Access Management conference](http://www.eduserv.org.uk/newsandevents/events/fam11) about how OAuth works and how we are using it. The slides for this presentation can be found at [https://speakerdeck.com/u/alexbilbie/p/introduction-to-oauth](https://speakerdeck.com/u/alexbilbie/p/introduction-to-oauth).

During the Linkey project I'm going to be redeveloping my [CodeIgniter OAuth 2.0 server](https://github.com/alexbilbie/CodeIgniter-OAuth-2.0-Server) to a be a framework-agnostic [Composer](http://getcomposer.org) package. I'm also going to be adding support for the [bearer extension](http://tools.ietf.org/wg/oauth/draft-ietf-oauth-v2-bearer/) and the [assertions extension](http://tools.ietf.org/wg/oauth/draft-ietf-oauth-assertions/).

[^twitter_connect]: Currently on Twitter Connect supports OAuth 2.0 using the XXX flow.
