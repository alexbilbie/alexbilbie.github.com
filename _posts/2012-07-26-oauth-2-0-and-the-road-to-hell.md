---
layout: post
title: OAuth 2.0 and the road to Hell
permalink: /2012/07/oauth-2-0-and-the-road-to-hell/
tags: [oauth]
---

Eran Hammer who, until a few days ago, was the main editor of OAuth 2.0 has written this very damning blog post about the protocol

[http://hueniverse.com/2012/07/oauth-2-0-and-the-road-to-hell/](http://hueniverse.com/2012/07/oauth-2-0-and-the-road-to-hell/)

I’ve been following the working group for about a year now and there has been an awful lot of bickering and mindless discussion.

As I’ve stated already, I’ve spent the last few weeks working on a brand new library that implements the current spec (draft 30) and I do feel myself agreeing with some of his points:

* Yes the specification mess, I’ve highlighted and sticky noted the printed spec document to death and the flow between different sections is really bad
* Some new features such as refresh tokens are overly complicated and don’t benefit the protocol, and it means clients now have to maintain access token state added to complexity
* Just reading the spec you can tell how much it has been influenced enterprise – many features are very open ended so that you bolt OAuth onto something else (or something else onto OAuth)
* Bearer tokens over SSL/TLS by themselves are bad and I think signatures need to come back
	* Basically if I steal someone’s access token I can use that wily nilly, however with signatures the entire request is signed with the client’s secret key so unless the secret key is leaked you can’t just use access tokens by themselves
* I disagree however that the protocol (in it’s current state) is complicated, they’ve done great work at making it a 3 (or 4) legged protocol to just 2 legs and everyone agrees that bit of the protocol has been done well.

In terms of his suggestion that alternatives to OAuth that are outside the reach of the IETF might crop up I’m quite interested in this and if something crops up I’ll stick my nose in, and if one doesn’t then I’d be interested in having a go in writing one myself as an output of Linkey.

In terms of the extension documents (which include the assertions (SAML) extension, i don’t know enough about SAML to make any sort of informed opinion about this, however I’ve also still yet to see any public implementation of it.