---
title: "Facebook’s OAuth problem"
permalink: /2013/02/facebooks-oauth-problem/
date: "2013-02-25 11:00:00"
published: true
layout: post
---

Last week self-proclaimed web security evangelist Egor Homakov published a blog post entitled [How we hacked Facebook with OAuth2 and Chrome bugs](http://homakov.blogspot.co.uk/2013/02/hacking-facebook-with-oauth2-and-chrome.html). Naturally this led to a new round of “OAuth 2.0 is unsecure” comments on news articles that linked to the post.

If you actually read through the post however, the part of the attack that involves OAuth is this bit (emphasis from original text):

> 2) OAuth2 is... quite unsafe auth framework. Gigantic attack surface, all parameters are passed in URL. I will write a separate post about OAuth1 vs OAuth2 in a few weeks. Threat Model is bigger than in official docs.
In August 2012 I wrote a lot about common vulnerabilities-by-design and even proposed to fix it: OAuth2.a.
>
> We used 2 bugs: **dynamic redirect_uri and dynamic response_type parameter**.
response_type=code is the most secure authentication flow, because end user never sees his access_token. But response_type is basically **a parameter in authorize URL**. By replacing response_type=code to response_type=token,signed_request we receive both token and code on our redirect_uri.
>
> redirect_uri can be not only app's domain, **but facebook.com domain is also allowed**.
In our exploit we used response_type=token,signed_request&redirect_uri=FB_PATH where FB_PATH was a specially crafted URL to disclose these values..

If an OAuth 2.0 authorisation server is correctly configured then it should, in addition to numerous other checks, ensure that there is only one value in the _response_type_ parameter (e.g. “code” - if using the authorization code grant) and the server should store whitelisted redirect URIs for each client on the server so that attacks can’t be launched with dynamic URIs.

I suspect partly why this has happened to Facebook is because when they launched their Graph APIs back in 2010 they secured them with draft 5 of the OAuth 2.0 specification. As the specification changed over it’s 27 drafts they already had clients hardcoded with specific parameters that couldn’t be updated easily and so therefore they had to make a choice to allow the above parameters that were used in the attack to accept dynamic and non-canon values.

[My OAuth 2.0 server library](http://alexbilbie.com/2013/02/php-meet-oauth-2/) is not vulnerable to the aforementioned attack as it strictly validates and verifies every parameter that is sent to it and redirect URIs are whitelisted on the server.