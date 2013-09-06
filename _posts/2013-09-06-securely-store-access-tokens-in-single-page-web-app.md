---
title: "How to securely store OAuth access tokens in single page JavaScript web apps"
date: "2013-09-06 11:04:00"
layout: default
published: false
---

A question I'm asked frequently is how developers should securely store access tokens and client credentials in a single page using a single page JavaScript web apps.

Here's the answer: you can't.

There is currently no way of securely storing something in front end JavaScript code and decrypting it without leaking your encryption key too<sup>*</sup>.

I can only think of one way to potentially help secure the API requests is giving the browser an X.509 certificate which must be presented to the API along with the access token. The user's browser will automatically present it when requested by the server. An attacker will have to sniff both the access token (easily done) and the certificate (hard because it's not accessible via JavaScript) in order to make a fraudulent request.

Having said that I really don't think it's worth the pain of development, testing, debugging and maintaining. Instead my recommendation is to have a very thin backend script through which all ajax requests are proxied. This backend script can securely store the user's access token in an encrypted cookie/session/whatever and it securely keeps client's credentials away from the front end.


---
<sup>*</sup> I don't know if any of the upcoming HTML5 cryptography specifications will remedy this.
