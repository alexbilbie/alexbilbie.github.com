---
layout: page
title: "A Guide To OAuth 2.0 Grants"
permalink: /guide-to-oauth-2-grants/
---

The [OAuth 2.0 specification](http://tools.ietf.org/html/rfc6749) is a flexibile authorization framework that describes a number of grants ("methods") for a client application to acquire an access token (which represents a user’s permission for the client to access their data) which can be used to authenticate a request to an API endpoint.

The specification describes five grants for acquiring an access token:

* Authorization code grant
* Implicit grant
* Resource owner credentials grant
* Client credentials grant
* Refresh token grant

In this post I’m going to describe each of the above grants and their appropriate use cases.

As a refresher here is a quick glossary of OAuth terms (taken from the core spec):

* **Resource owner (a.k.a. the User)** - An entity capable of granting access to a protected resource. When the resource owner is a person, it is referred to as an end-user.
* **Resource server (a.k.a. the API server)** - The server hosting the protected resources, capable of accepting and responding to protected resource requests using access tokens.
* **Client** - An application making protected resource requests on behalf of the resource owner and with its authorization. The term client does not imply any particular implementation characteristics (e.g. whether the application executes on a server, a desktop, or other devices).
* **Authorization server** - The server issuing access tokens to the client after successfully authenticating the resource owner and obtaining authorization.

---

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-format="fluid"
     data-ad-layout="in-article"
     data-ad-client="ca-pub-9081611734634232"
     data-ad-slot="6452810405"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

---

## Authorisation Code Grant ([section 4.1](http://tools.ietf.org/html/rfc6749#section-4.1))

The authorization code grant should be very familiar if you've ever signed into an application using your Facebook or Google account.

### The Flow (Part One)

The client will redirect the user to the authorization server with the following parameters in the query string:

* `response_type` with the value `code`
* `client_id` with the client identifier
* `redirect_uri` with the client redirect URI. This parameter is optional, but if not send the user will be redirected to a pre-registered redirect URI.
* `scope` a space delimited list of scopes
* `state` with a [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) token. This parameter is optional but highly recommended. You should store the value of the CSRF token in the user's session to be validated when they return.

All of these parameters will be validated by the authorization server.

The user will then be asked to login to the authorization server and approve the client.

If the user approves the client they will be redirected from the authorisation server back to the client (specifically to the redirect URI) with the following parameters in the query string:

* `code` with the authorization code
* `state` with the state parameter sent in the original request. You should compare this value with the value stored in the user's session to ensure the authorization code obtained is in response to requests made by this client rather than another client application.

### The Flow (Part Two)

The client will now send a POST request to the authorization server with the following parameters:

* `grant_type` with the value of `authorization_code`
* `client_id` with the client identifier
* `client_secret` with the client secret
* `redirect_uri` with the same redirect URI the user was redirect back to
* `code` with the authorization code from the query string

The authorization server will respond with a JSON object containing the following properties:

* `token_type` this will usually be the word "Bearer" (to indicate a bearer token)
* `expires_in` with an integer representing the <abbr title="Time To Live">TTL</abbr> of the access token (i.e. when the token will expire)
* `access_token` the access token itself
* `refresh_token` a refresh token that can be used to acquire a new access token when the original expires

---

## Implicit grant ([section 4.2](http://tools.ietf.org/html/rfc6749#section-4.2))

The implicit grant is similar to the authorization code grant with two distinct differences.

It is intended to be used for user-agent-based clients (e.g. single page web apps) that can't keep a client secret because all of the application code and storage is easily accessible.

Secondly instead of the authorization server returning an authorization code which is exchanged for an access token, the authorization server returns an access token.

### The Flow

The client will redirect the user to the authorization server with the following parameters in the query string:

* `response_type` with the value `token`
* `client_id` with the client identifier
* `redirect_uri` with the client redirect URI. This parameter is optional, but if not sent the user will be redirected to a pre-registered redirect URI.
* `scope` a space delimited list of scopes
* `state` with a [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) token. This parameter is optional but highly recommended. You should store the value of the CSRF token in the user's session to be validated when they return.

All of these parameters will be validated by the authorization server.

The user will then be asked to login to the authorization server and approve the client.

If the user approves the client they will be redirected back to the authorization server with the following parameters in the query string:

* `token_type` with the value `Bearer`
* `expires_in` with an integer representing the TTL of the access token
* `access_token` the access token itself
* `state` with the state parameter sent in the original request. You should compare this value with the value stored in the user's session to ensure the authorization code obtained is in response to requests made by this client rather than another client application.

**Note:** this grant does <u>not</u> return a refresh token because the browser has no means of keeping it private

---

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout="text-only"
     data-ad-layout-key="-gq-e+2y-3b-30"
     data-ad-client="ca-pub-9081611734634232"
     data-ad-slot="7821732399"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

---

## Resource owner credentials grant ([section 4.3](http://tools.ietf.org/html/rfc6749#section-4.3))

This grant is a great user experience for <u>trusted</u> first party clients both on the web and in native device applications.

### The Flow

The client will ask the user for their authorization credentials (ususally a username and password).

The client then sends a POST request with following body parameters to the authorization server:

* `grant_type` with the value `password`
* `client_id` with the the client's ID
* `client_secret` with the client's secret
* `scope` with a space-delimited list of requested scope permissions.
* `username` with the user's username
* `password` with the user's password

The authorization server will respond with a JSON object containing the following properties:

* `token_type` with the value `Bearer`
* `expires_in` with an integer representing the TTL of the access token
* `access_token` the access token itself
* `refresh_token` a refresh token that can be used to acquire a new access token when the original expires

---

## Client credentials grant  ([section 4.4](http://tools.ietf.org/html/rfc6749#section-4.4))

The simplest of all of the OAuth 2.0 grants, this grant is suitable for machine-to-machine authentication where a specific user's permission to access data is not required.

### The Flow

The client sends a POST request with following body parameters to the authorization server:

* `grant_type` with the value `client_credentials`
* `client_id` with the the client's ID
* `client_secret` with the client's secret
* `scope` with a space-delimited list of requested scope permissions.

The authorization server will respond with a JSON object containing the following properties:

* `token_type` with the value `Bearer`
* `expires_in` with an integer representing the TTL of the access token
* `access_token` the access token itself

---

## Refresh token grant ([section 1.5](http://tools.ietf.org/html/rfc6749#section-1.5))

Access tokens eventually expire; however some grants respond with a refresh token which enables the client to get a new access token without requiring the user to be redirected.

### The Flow

The client sends a POST request with following body parameters to the authorization server:

* `grant_type` with the value `refresh_token`
* `refresh_token` with the refresh token
* `client_id` with the the client's ID
* `client_secret` with the client's secret
* `scope` with a space-delimited list of requested scope permissions. This is optional; if not sent the original scopes will be used, otherwise you can request a reduced set of scopes.

The authorization server will respond with a JSON object containing the following properties:

* `token_type` with the value `Bearer`
* `expires_in` with an integer representing the TTL of the access token
* `access_token` the access token itself
* `refresh_token` a refresh token that can be used to acquire a new access token when the original expires

---

## Additonal Grants

There are additional grants that have been published in other specifications that I will cover in a future article.

---

## Which OAuth 2.0 grant should I use?

A grant is a method of acquiring an access token. Deciding which grants to implement depends on the type of client the end user will be using, and the experience you want for your users.

<figure>
    <img src="/images/oauth-grants.svg" style="width:100%">
</figure>

### First party or third party client?

A first party client is a client that you trust enough to handle the end user's authorization credentials. For example Spotify's iPhone app is owned and developed by Spotify so therefore they implicitly trust it.

A third party client is a client that you don't trust.

### Access Token Owner?

An access token represents a permission granted to a client to access some protected resources.

If you are authorizing a machine to access resources and you don't require the permission of a user to access said resources you should implement the client credentials grant.

If you require the permission of a user to access resources you need to determine the client type.

### Client Type?

Depending on whether or not the client is capable of keeping a secret will depend on which grant the client should use.

If the client is a web application that has a server side component then you should implement the authorization code grant.

If the client is a web application that has runs entirely on the front end (e.g. a single page web application) you should implement the password grant for a first party clients and the implicit grant for a third party clients.

If the client is a native application such as a mobile app you should implement the password grant.

Third party native applications should use the authorization code grant (via the native browser, not an embedded browser - e.g. for iOS push the user to Safari or use [SFSafariViewController](https://developer.apple.com/library/ios/documentation/SafariServices/Reference/SFSafariViewController_Ref/), <u>don't</u> use an embedded [WKWebView](https://developer.apple.com/library/ios/documentation/WebKit/Reference/WKWebView_Ref/)).