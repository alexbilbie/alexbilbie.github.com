---
layout:post
title: OAuth 2.0 has been standardised
permalink: /2012/10/oauth-2-0-has-been-standardised/
---

The IETF has approved the OAuth 2.0 Core and Bearer specifications and have now been published as [RFC 6749](http://tools.ietf.org/html/rfc6749) (Core) and [RFC 6750](http://tools.ietf.org/html/rfc6750) (Bearer).

Dick Hardt, one of the editors of the spec has [summarised three important enhancements](http://dickhardt.org/2012/10/oauth-2-0/) that OAuth 2.0 has over the old 1.0a spec:

> * Simplicity: Client developers don’t need to do any cryptography or use a library to call OAuth 2.0 protected resources. The token can be passed in the HTTP headers or as a URL parameter. While HTTP headers are preferred, a URL parameter is simpler and allows API exploration with a browser.
> * Token choice: implementers can use existing tokens that they already generate or consume. There are extension points so that the client can sign the token instead of it being a bearer token.
> * Separation of roles: if the token is self-contained, then the resource can verify the token independently of the authorization server. Resources don’t have to call back to the authorization server to verify the token on each call, enabling higher performance and separation of security contexts.