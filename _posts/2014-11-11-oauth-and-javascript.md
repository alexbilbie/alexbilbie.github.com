---
title: "OAuth and Single Page JavaScript Web-Apps"
date: "2014-11-11 11:01:00"
layout: post
permalink: /2014/11/oauth-and-javascript/
---

Earlier today I tweeted:

<blockquote class="twitter-tweet" lang="en"><p>A server dies every time someone implements OAuth in a single page is web-app. Stop the genocide! Use a server side proxy! Act now!</p>&mdash; Alex Bilbie (@alexbilbie) <a href="https://twitter.com/alexbilbie/status/532110270316171265">November 11, 2014</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

This kicked off a discussion across Twitter, Github issues and email about why I have such strong opinions about this.

It's simple, **security**. You just can't keep things that should be secret safe in client side code.

Let's assume that you've just made a shiny Angular/Ember/whatever single page web-app that gets all of it's data from an API that you've written via ajax calls. You've also elected to secure the API with OAuth and you're also  securing the API endpoint with SSL (as the OAuth spec requires).

So because this is an app that you've written and it's talking to _your_ backend you've decided that the "resource owner password credentials grant" (aka the "password grant") is the way that you're going to get an access token. The access token can then be used to authenticate API requests.

The web-app is going to make an ajax request to the API to sign the user in once you've captured their credentials (line breaks added for readability). This is how a valid OAuth 2 password grant access token request should look:

```
POST /auth HTTP/1.1
Host: api.example.com

grant_type=password
&client_id=webapp
&client_secret=52d14e22b9101
&username=alexbilbie
&password=whisky
```

The server will respond:

```
{
	"access_token": "DDSHs55zpG51Mtxnt6H8vwn5fVJ230dF",
	"refresh_token": "24QmIt2aV1ubaenB2D6G0se5pFRk4W05",
	"token_type": "Bearer",
	"expires": 1415741799
}
```

Already there are major problems with this.

First in the app's request we're sending the client ID and secret which the API uses to ensure the request is coming from a known source. As there is no backend to the web-app these will have to be stored in the front-end code and they can't be encrypted in code because you [can't do crypto in JavaScript securely](http://matasano.com/articles/javascript-cryptography/). So already we've got the problem that the only way of identifying the web-app - by using credentials - are already leaked in public code and will allow an attacker to attempt to make authenticated requests independent of the app. You can't use referrer headers to lock down requests either as they are easily faked. You can't store the credentials in an encrypted form in a cookie either because that cookie can be just grabbed by the attacker as easily as the client credentials that are baked into source code.

Moving on, in the response to the request the server has given us an access token which is used to authenticate requests to the API and a refresh token which is used to acquire a new access token when it expires.

First we've got the issue that the access token is now available to the attacker. He doesn't need anything else now to make requests to your API and go crazy grabbing all of the users' private data and performing any actions that the API allows. The server has got no way of knowing that it isn't the web-app making the requests.

Valid request from the web-app:

```
GET /resource/123 HTTP/1.1
Authorization: Bearer DDSHs55zpG51Mtxnt6H8vwn5fVJ230dF
Host: api.example.com
```

Valid request from an attacker:

```
GET /resource/123 HTTP/1.1
Authorization: Bearer DDSHs55zpG51Mtxnt6H8vwn5fVJ230dF
Host: api.example.com
```

Even if your API has short lived access tokens then the refresh token was also in the response to the browser so the attacker can use that to get a new access token when the original expires.

The simple story is here that you can't keep things safe in the front end. So don't.

Another scenario you might take when building your app and API is to implement the "implicit grant":

>   The implicit grant type is used to obtain access tokens (it does not
   support the issuance of refresh tokens) and is optimized for public
   clients known to operate a particular redirection URI.  These clients
   are typically implemented in a browser using a scripting language
   such as JavaScript.  
   _([https://tools.ietf.org/html/rfc6749#section-4.2](https://tools.ietf.org/html/rfc6749#section-4.2))_

This scenario is very similar to the flow that most people think of when they think of OAuth. That is, the user is redirected from the application they want to use to the identity provider, they sign-in, authorise the request and then are returned to the application. Then server side the application and the identity provider exchange some credentials and an authorisation code which was returned with the user and the end result is an access token.

The implicit grant is similar except instead of the user being returned to the application with an authorisation code, they arrive with an access token. Now if you're implementing a first party application which is talking to your API (so you implicitly trust it because you made it) then would be a crappy user experience because you're redirecting the user to other places when you should just ask the user for the username and password and avoid the "redirect dance". But anyway.

Because there is no token swapping server side there is now no need to worry about storing client credentials in front end code. But now you've just created a situation where (1) the API isn't authenticating the request for an access token (okay it is with a client ID and whitelisted redirect URI but I'm making a point here) and (2) you've just dished out an access token without any further verification that all parties involved are who they say they are. Congratulations!

For added pain the implicit grant doesn't support refresh tokens either so that access token is going to have a really long TTL or you're going to piss your users off by doing a redirect-dance whenever the token expires.

You could add some extra logic into your API so access tokens that were created with the implicit grant can only authenticate very specific read-only APIs but then you're reducing the usefulness of your application because it can't call all the endpoints available in the API.

---

I hope now you can see that there is a lot to consider when working with OAuth.

Please please please just avoid the implicit grant, it's a bag of hurt for everyone and it's just not worth implementing if you care even slightly about user experience and security.

**So how can you use OAuth securely in single page web-apps?**

It's simple; proxy all of your API calls via a thin server side component. This component (let's just call it a proxy from here on) will authenticate ajax requests from the user's session. The access and refresh tokens can be stored in an encrypted form in a cookie which only the proxy can decrypt. The application client credentials will also be hardcoded into the proxy so they're not publicly accessible either.

To authenticate the user in the first place the web-app will make a request to the proxy with just the user's credentials:

```
POST /ajax/auth HTTP/1.1
Host: example.com

username=alexbilbie
&password=whisky
```

The proxy will then add in the client credentials which only it knows and forward the request onto the API:

```
POST /auth HTTP/1.1
Host: api.example.com

grant_type=password
&client_id=webapp
&client_secret=52d14e22b9101
&username=alexbilbie
&password=whisky
```

The server will respond:

```
{
	"access_token": "DDSHs55zpG51Mtxnt6H8vwn5fVJ230dF",
	"refresh_token": "24QmIt2aV1ubaenB2D6G0se5pFRk4W05",
	"token_type": "Bearer",
	"expires": 1415741799
}
```

The proxy will encrypt the tokens in a cookie and return a success message to the user.

When the web-app makes a request to an API endpoint it will call the proxy instead of the API:

```
GET /ajax/resource/123 HTTP/1.1
Cookie: <encrypted cookie with tokens>
Host: example.com
```

The proxy will decrypt the cookie, add the `Authorization` header to the request and forward it on to the API:

```
GET /resource/123 HTTP/1.1
Authorization: Bearer DDSHs55zpG51Mtxnt6H8vwn5fVJ230dF
Host: api.example.com
```

The proxy will pass the response straight back to the browser.

With this setup there are no publicly visible or plain text client credentials or tokens which means that attackers won't be able to make faked requests to the API. Also because the browser is no longer communicating with the API directly you can remove it from the public Internet and lock down the firewall rules so that only requests coming from the web server directly will be allowed.

To protect an attacker just stealing the cookie you can use CSRF protection measures.

---

Making secure single page web-apps is possible, you just need to not get wrapped up in the "no backend" ideology and always remember that you can't trust the user.
