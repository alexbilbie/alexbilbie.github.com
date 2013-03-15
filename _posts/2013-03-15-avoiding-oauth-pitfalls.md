---
layout: default
published: true
title: "More OAuth pitfalls that you can easily avoid"
date: "2013-03-15 19:07:00"
permalink: /2013/03/avoiding-oauth-pitfalls/
---

`<rage>`

Egor Homakov has written [another blog post](http://homakov.blogspot.co.uk/2013/03/redirecturi-is-achilles-heel-of-oauth.html?m=1) today exploring Facebook's OAuth problem (as I dubbed it in [my previous post](http://alexbilbie.com/2013/02/facebooks-oauth-problem/)).

Basically if you are developing an OAuth authorisation server then you **must** whitelist the entire redirect URI, not just the domain. In the [OAuth threat model specification](http://tools.ietf.org/html/draft-ietf-oauth-v2-threatmodel-08#section-4.1.5) there is a [whole section (4.1.5)](http://tools.ietf.org/html/draft-ietf-oauth-v2-threatmodel-08#section-4.1.5) dedicated to discuss pitfalls with open redirectors.

> If the authorization server allows the client to register only part of the redirection URI, an attacker can use an open redirector operated by the client to construct a redirection URI that will pass the authorization server validation but will send the authorization code or access token to an endpoint under the control of the attacker.

Also if you have to use the implicit grant for whatever reason then as I said in [my guide to OAuth grants](http://alexbilbie.com/2013/02/a-guide-to-oauth-2-grants/) you **must** bear this in mind:

> If you decide to implement this grant then you must be aware that the access token should be treated as “public knowledge” (like a public RSA key) and therefore it must have a very limited permissions when interacting with the API server.

Unless you have a very very specific need and you can perhaps secure clients with client side certificates or you have a very clean environment then I strongly recommend you just avoid the implicit grant.

`</rage>`