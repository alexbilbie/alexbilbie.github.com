---
layout: post
title: OAuth 2.0 Security - Going Beyond Bearer Tokens
permalink: /2012/09/oauth-2-0-security-going-beyond-bearer-tokens
tags: [oauth]
---

The OAuth working group have released an initial security review of the OAuth 2.0 specification which looks at a number of potential threats that implementors could face and how to mitigate them.

The document can be read here [http://tools.ietf.org/html/draft-tschofenig-oauth-security-00](http://tools.ietf.org/html/draft-tschofenig-oauth-security-00).

The abstract of the document is below:

> The OAuth working group has finished work on the OAuth 2.0 core protocol as well as the Bearer Token specification. The Bearer Tokenis a TLS-based solution for ensuring that neither the interaction with the Authorization Server (when requesting a token) nor the interaction with the Resource Server (for accessing a protected resource) leads to token leakage. There has, however, always been the desire to develop a security solution that is "better" than Bearer Tokens (or at least different) where the Client needs to show
possession of some keying material when accessing a Resource Server.

> This document tries to capture the discussion and to come up with requirements to process the work on solutions.

> This document aims to discuss threats, security requirements and desired design properties of an enhanced OAuth security mechanism.