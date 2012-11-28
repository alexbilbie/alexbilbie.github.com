---
layout: post
permalink: /2012/12/hawk-a-new-http-authentication-scheme
title: Hawk, a new HTTP authentication scheme
---

[Eran Hammer](http://hueniverse.com/) (formerly editor of the OAuth specifications) has introduced a new HTTP authentication scheme called [Hawk](https://github.com/hueniverse/hawk).

Hawk is described as a HTTP authentication scheme using a message authentication code (MAC) algorithm to provide partial HTTP request cryptographic verification. It is related to his other new project [Oz](https://github.com/hueniverse/oz) which is a web authorisation protocol promoted as an alternative to OAuth. Eran has made it clear that he doesn’t want to write a new specification after the leaving the OAuth working group and so he is intending to describe Hawk (and Oz) through code and leave it up to someone else to write a formal specification.

### What is Hawk?

An overview of Hawk is provided in the [README file in the repository](https://github.com/hueniverse/hawk/blob/master/README.md#introduction):

> Hawk is an HTTP authentication scheme providing a method for making authenticated HTTP requests with partial cryptographic verification of the request, covering the HTTP method, request URI, and host.
>
> Similar to the HTTP Basic access authentication scheme, the Hawk scheme utilizes a set of client credentials which include an identifier and key. However, in contrast with the Basic scheme, the key is never included in authenticated requests but is used to calculate a request MAC value which is included instead.
>
> The Hawk scheme requires the establishment of a shared symmetric key between the client and the server, which is beyond the scope of this module. Typically, the shared credentials are established via an initial TLS-protected phase or derived from some other shared confidential information available to both the client and the server.
>
> The primary design goals of this mechanism are to:
>
> * simplify and improve HTTP authentication for services that are unwilling or unable to employ TLS for every request,
> * secure the shared credentials against leakage when sent over a secure channel to the wrong server (e.g., when the client uses some form of dynamic configuration to determine where to send an authenticated request), and
> * mitigate the exposure of credentials sent to a malicious server over an unauthenticated secure channel due to client failure to validate the server's identity as part of its TLS handshake.
>
> Unlike the HTTP Digest authentication scheme, Hawk provides limited protection against replay attacks which does not require prior interaction with the server. Instead, the client provides a timestamp which the server can use to prevent replay attacks outside a narrow time window. Also unlike Digest, this mechanism is not intended to protect the key itself (user's password in Digest) because the client and server both have access to the key material in the clear.

To clarify, MAC (Message Authentication Code) is a cryptographic string that is sent alongside a message (such as an HTTP request) to detect tampering and forgery. Both the sender and the receiver will use the same shared secret to generate and verify the MAC. The OAuth 1.0 specification used MAC signatures but these were removed in OAuth 2.0.

### Protocol Example

To demonstrate how Hawk authentication works there is a [protocol example](https://github.com/hueniverse/hawk/blob/master/README.md#protocol-example):

The client attempts to access a protected resource without authentication, sending the following HTTP request to
the resource server:

	GET /resource/1?b=1&a=2 HTTP/1.1
	Host: 127.0.0.1:8000

The resource server returns the following authentication challenge:

	HTTP/1.1 401 Unauthorized
	WWW-Authenticate: Hawk

The client has previously obtained a set of **Hawk** credentials for accessing resources on the "http://example.com/"
server. The **Hawk** credentials issued to the client include the following attributes:

* Key identifier:  dh37fgj492je
* Key:  werxhqb98rpaxn39848xrunpaw3489ruxnpa98w4rxn
* Algorithm:  hmac-sha-256

The client generates the authentication header by calculating a timestamp (e.g. the number of seconds since January 1,
1970 00:00:00 GMT) and constructs the normalized request string (newline separated values):

	1353832234
	GET
	/resource/1?b=1&a=2
	127.0.0.1
	8000
	some-app-data

The request MAC is calculated using the specified algorithm "hmac-sha-256" and the key over the normalized request string.
The result is base64-encoded to produce the request MAC:

	/uYWR6W5vTbY3WKUAN6fa+7p1t+1Yl6hFxKeMLfR6kk=

The client includes the **Hawk** key identifier, timestamp, and request MAC with the request using the HTTP "Authorization"
request header field:

	GET /resource/1?b=1&a=2 HTTP/1.1
	Host: 127.0.0.1:8000
	Authorization: Hawk id="dh37fgj492je", ts="1353832234", ext="some-app-data", mac="/uYWR6W5vTbY3WKUAN6fa+7p1t+1Yl6hFxKeMLfR6kk="

The server validates the request by calculating the request MAC again based on the request received and verifies the validity and scope of the **Hawk** credentials. If valid, the server responds with the requested resource.

### Security Considerations

There are a number of security considerations to consider which can be seen here [https://github.com/hueniverse/hawk#security-considerations](https://github.com/hueniverse/hawk#security-considerations).

### Hawk vs. OAuth

The Hawk documentation says that is can be used for client to server authentication but I think it would also make an easier to implement (than OAuth) authentication protocol if you are implementing machine to machine authentication.

For delegating access to protected resources then the documentation recommends [Oz](https://github.com/hueniverse/oz) as alternative to OAuth. I will review Oz in another post.

## PHP Implementation

I’ve had a go at implementing Hawk in PHP - [https://github.com/alexbilbie/PHP-Hawk](https://github.com/alexbilbie/PHP-Hawk).

### Client Usage

Assume you're hitting up the following endpoint:

`https://api.example.com/user/123?foo=bar`

And the API server has given you the following credentials:

* Key - `ghU3QVGgXM`
* Secret - `5jNP12yT17Hx5Md3DCZ5pGI5sui82efX`

To generate the header run the following:

{% highlight php %}
<?php
$key = 'ghU3QVGgXM';
$secret = '5jNP12yT17Hx5Md3DCZ5pGI5sui82efX';
$hawk = Hawk::generateHeader($key, $secret, array(
				'host'	=>	'api.example.com', // you must set this
				`port`	=>	443, // you must set this
				'path'	=>	'/user/123',
				'method'	=>	'GET' // could be POST/DELETE/etc
			));
{% end highlight %}

You can also pass in additional application specific data with an `ext` key in the array.

Once you've got the Hawk string include it in your HTTP request as an `Authorization` header.

### Server Usage

On your API endpoint if the incoming request is missing an authorization header then return the following two headers:

`HTTP/1.1 401 Unauthorized`
`WWW-Authenticate: Hawk`

If the request does contain a Hawk authorization header then process it like so:

{% highlight php %}
<?php
$hawk = ''; // the authorisation header

// First parse the header to get the parts from the string
$hawk_parts = Hawk::parseHeader($hawk);

// Then with your own function, get the secret for the key from the database
$secret = getSecret($hark_parts['id']);

// Now validate the request
$valid = Hawk::verifyHeader($hawk, array(
		'host'	=>	'api.example.com',
		'port'	=>	443,
		'path'	=>	'/user/123',
		'method'	=>	'GET'
	), $secret); // return true if the request is valid, otherwise false
{% endhighlight %}
