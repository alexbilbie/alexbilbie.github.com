---
title: "OAuth Open Redirector Attack"
date: "2015-04-16 10:00:00"
layout: post
---

I'm a little late to writing about this but as reported by Antonio Sanso on [his blog](http://intothesymmetry.blogspot.ch/2015/04/open-redirect-in-rfc6749-aka-oauth-20.html) he found yet another flaw in well known identity providers' OAuth 2.0 implementations.

The specifics of the attack are the same as the last flaw that was found with Facebook's implementation that [I wrote about a while ago](/2013/02/facebooks-oauth-problem/); namely that vendors aren't being strict about whitelisting redirect URIs for the authorization (and likely implicit) grant routes.

Antonio discovered that if you registered a client with one redirect URI but crafted a OAuth authorizw URL with a different `redirect_uri` parameter then vendors were sending the user to the invalid (and non-whitelisted) redirect URI.

In these examples `www.attacker.com` is the non-whitelisted redirect URI:

* Facebook: `https://graph.facebook.com/oauth/authorize?response_type=code&client_id=1621835668046481&redirect_uri=http://www.attacker.com/&scope=WRONG_SCOPE`
* Github: `https://github.com/login/oauth/authorize?response_type=code&redirect_uri=http://attacker.com2&client_id=e2ddb90328315c367b11`
* Microsoft: `https://login.live.com/oauth20_authorize.srf?response_type=code&redirect_uri=http://attacker.com&client_id=000000004C12822C`
* Moves: `https://api.moves-app.com/oauth/v1/authorize?response_type=code&client_id=bc88FitX1298KPj2WS259BBMa9_KCfL3&redirect_uri=data%3Atext%2Fhtml%2Ca&state=<script>alert('hi')</script>`

In his testing he discovered that Google's implementation returned a `HTTP 400` instead of redirect the user because it is strictly validating the redirect URI against the client.

The `league/oauth2-server` PHP library I wrote is not vulnerable to this attack because very early on in the request I validate the `redirect_uri` along with the client credentials - [https://github.com/thephpleague/oauth2-server/blob/master/src/Grant/AuthCodeGrant.php#L82](https://github.com/thephpleague/oauth2-server/blob/master/src/Grant/AuthCodeGrant.php#L82).
