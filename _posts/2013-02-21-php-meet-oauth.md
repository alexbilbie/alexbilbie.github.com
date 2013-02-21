---
layout: default
title: "PHP, meet OAuth 2.0"
permalink: /2013/02/php-meet-oauth-2/
---

Over the last few months I’ve been developing a PHP library that helps you work with OAuth 2.0 in a number of different ways:

* Develop an authentication server which can be used as part of a web “single sign on” solution.
* Secure your APIs with access tokens and scopes.
* Easily sign users in to many different OAuth 2.0 identity providers.

The code for the authentication and resource server can be found on Github here [https://github.com/lncd/OAuth2](https://github.com/lncd/OAuth2).

The server library code requires PHP 5.3+, is hooked into [Packagist](https://packagist.org/packages/lncd/Oauth2) (a bit like Ruby Gems but for PHP) and has 100% unit test code coverage. It has built in support for the following grants:

* authentication code ([section 4.1](http://tools.ietf.org/html/rfc6749#section-4.1))
* refresh token ([section 6](http://tools.ietf.org/html/rfc6749#section-6))
* client credentials ([section 2.3.1](http://tools.ietf.org/html/rfc6749#section-2.3.1))
* password (user credentials) ([section 4.3](http://tools.ietf.org/html/rfc6749#section-4.3))
* implicit grant ([section 4.2](http://tools.ietf.org/html/rfc6749#section-4.2)) - though I don't recommend you use this grant unless you really understand how to safely use it

You can easily create you own grants by extending `\OAuth2\Grant\GrantInterface`. I'm going to be creating plugins which support [JSON web tokens](http://tools.ietf.org/wg/oauth/draft-ietf-oauth-json-web-token/) and the [SAML assertions](http://tools.ietf.org/wg/oauth/draft-ietf-oauth-saml2-bearer/).

The code for the client library can be found here [https://github.com/lncd/OAuth2-Client](https://github.com/lncd/OAuth2-Client) - at the time of writing it isn’t quite finished, I’ll blog when it is.

Over the next few blog posts I'll document how to use the libraries.