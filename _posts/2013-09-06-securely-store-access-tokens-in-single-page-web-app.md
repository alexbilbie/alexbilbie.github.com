---
title: "How to securely store OAuth access tokens in single page JavaScript web apps"
date: "2013-09-06 11:04:00"
layout: post
published: true
tags: [oauth]
---

---

### Please see my new post which explains my thoughts here in greater detail - [http://alexbilbie.com/2014/11/oauth-and-javascript/](http://alexbilbie.com/2014/11/oauth-and-javascript/)

---

A question I'm asked frequently is how developers should securely store OAuth access tokens and client credentials in a single page JavaScript webapp.

Here's the answer: you can't.

There is currently no way of securely storing something in front end JavaScript code and decrypting it without leaking your encryption key too<sup>*</sup>.

I can only think of one way to potentially help secure the API requests and that is by giving the browser a short-lived X.509 certificate (perhaps with the same TTL as an access token) which must be presented to the API along with the access token. The certificate's "Subject Unique Identifier" parameter would contain the user's ID.

The user's browser will automatically present the certificate when requested by the API server during an API call and the server would then check the "Subject Unique Identifier" matched the ID of the user who owns the access token. An attacker will have to sniff both the access token (easily done) and the certificate (hard because it's not accessible via JavaScript) in order to make a fraudulent request.

Having said that I really don't think it's worth the pain of development, testing, debugging and maintaining. Instead my recommendation is to have a very thin backend script through which all ajax requests are proxied. This backend script can securely store the user's access token in an encrypted cookie/session/whatever and it securely keeps client's credentials away from the front end.

Shameless plug: If you want to really easily implement a standards compliant OAuth 2.0 authorization server or secure your API with OAuth 2.0 then check out my PHP library - [https://github.com/php-loep/oauth2-server](https://github.com/php-loep/oauth2-server).

<sup>*</sup> _I don't know if any of the upcoming HTML5 cryptography specifications will remedy this._
