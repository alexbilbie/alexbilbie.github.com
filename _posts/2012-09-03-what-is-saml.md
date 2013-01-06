---
layout: default
title: What is SAML?
permalink: /2012/09/what-is-saml/
---

One of the outputs of the Linkey project is an open source PHP library that implements the [SAML bearer assertion extension](http://tools.ietf.org/html/draft-ietf-oauth-saml2-bearer-13) (draft 13 at time of writing) to the OAuth 2.0 specification.

**But what is SAML?**

SAML, or Security Assertion Markup Language, is an XML-based framework that allows identity and security information to be shared across security domains - for example between two websites.

Information is exchanged through the concept of assertion tokens, which are XML documents that state facts about the assertion's subject. For example, a SAML assertion about me might look like:

{% highlight html %}
<saml:Response ID="_257f9d9e9fa14962c0803903a6ccad931245264310738" IssueInstant="2012-09-03T18:45:10Z" Version="2.0">

	<saml:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">
		https://www.lincoln.ac.uk
	</saml:Issuer>

	<saml:Status>
		<saml:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
	</saml:Status>

	<saml:Assertion ID="_3c39bc0fe7b13769cab2f6f45eba801b1245264310738" IssueInstant="2012-09-03T18:45:10Z" Version="2.0">
	<saml:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">
		https://www.lincoln.ac.uk
	</saml:Issuer>

	<saml:Subject>
		<saml:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified">
			abilbie@lincoln.ac.uk
		</saml:NameID>
		<saml:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
			<saml:SubjectConfirmationData NotOnOrAfter="2012-09-03T18:50:10Z" Recipient="https://sso.lincoln.ac.uk"/>
		</saml:SubjectConfirmation>
	</saml:Subject>

	<saml:Conditions NotBefore="2012-09-03T18:45:10Z" NotOnOrAfter="2012-09-03T18:50:10Z">
		<saml:AudienceRestriction>
			<saml:Audience>https://sso.lincoln.ac.uk</saml:Audience>
		</saml:AudienceRestriction>
	</saml:Conditions>

	<saml:AuthnStatement AuthnInstant="2012-09-03T18:45:10Z">
		<saml:AuthnContext>
			<saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified</saml:AuthnContextClassRef>
		</saml:AuthnContext>
	</saml:AuthnStatement>

	<saml:AttributeStatement>

		<saml:Attribute Name="name">
			<saml:AttributeValue xsi:type="xs:anyType">Alex Bilbie</saml:AttributeValue>
		</saml:Attribute>

		<saml:Attribute Name="network_id">
			<saml:AttributeValue xsi:type="xs:anyType">abilbie</saml:AttributeValue>
		</saml:Attribute>

		<saml:Attribute Name="division">
			<saml:AttributeValue xsi:type="xs:anyType">ICT Services</saml:AttributeValue>
		</saml:Attribute>

		<saml:Attribute Name="staff_directory_url">
			<saml:AttributeValue xsi:type="xs:anyType">http://staff.lncd.org/abilbie</saml:AttributeValue>
		</saml:Attribute>

	</saml:AttributeStatement>
	</saml:Assertion>
</saml:Response>
{% endhighlight %}

This assertion here states that my name is `Alex Bilbie`, my network ID is `abilbie`, my division is `ICT Services` and that my staff directory page is `http://staff.lncd.org/abilbie`.

Generally the assertion will be provided by an "identity provider" (a.k.a an IDP) and consumed by a "service provider" (a.ka. a SDP).

SAML is often used in single sign on environments. Let's consider a student trying to sign in to the student union website:

1. The student (the user) would visit student union website (the SDP)
2. The SDP would realise the user isn't yet signed in and so redirects the user to the single sign on endpoint (the IDP)
3. The user authenticates with the IDP
4. The IDP redirects the user back to the SDP with the assertion (by using a HTTP POST)
5. The SDP consumes the assertion, validates it and signs the user in

**How does SAML compare to OAuth?**

For reference, this is how OAuth is used in a single sign on environment:

1. The student (the user) would visit student union website (the client)
2. The client would realise the user isn't yet signed in and so redirects the user to the single sign on endpoint (the authentication server)
3. The user authenticates with the authentication server
4. The user grants the client access to their private data
5. The authentication server redirects the user back to the client with an authorisation code in the URL
6. The client exchanges the authorisation code for an access token with the authentication server by itself authenticating
7. The client then sends a request to the resource server to request the user's private data. The access token represents permission granted by the user for the client to act on their behalf.

Now the OAuth flow looks longer it contains one important stage which is a requirement of the specification, specifically step 4 which is where the user is explicitly asked if they want to allow the client to access their data and also what data will be accessed. You could emulate this in the SAML flow but I think the upfront honesty so to speak in the OAuth flow is a huge advantage for users. Also most OAuth authentication servers allow users to revoke access to clients as well (i.e. their access token for that client is destroyed which immediately cuts access).

In both flows, the client/SDP itself is verified. In SAML this is done through certificate exchanges, in OAuth the client's ID is validated at step 3 and step 6.

**How does the assertion extension work?**

Looking at the OAuth sign-in flow above, at step 6 the client exchanges the authorisation code for an access token. There are a number of ways this can step can actually work and each different method is called an authorisation grant type. The grant described above (and the grant is the generally used in most OAuth implementations) is the `authorization_code` grant.

The assertion extension defines a new authorisation grant type of `urn:ietf:params:oauth:client-assertion-type:saml2-bearer`. There is also another parameter called `client_assertion` which is a base64 encoded representation of the XML assertion. The client/SDP's own credentials are in the assertion instead of being sent as `client_id` and `client_secret` parameters as in the authorisation code grant.

An example request would look like:

	POST /token.oauth2 HTTP/1.1
	Host: authz.example.net
	Content-Type: application/x-www-form-urlencoded

	grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Asaml2-
	bearer&assertion=PEFzc2VydGlvbiBJc3N1ZUluc3RhbnQ9IjIwMTEtMDU
	[...omitted for brevity...]aG5TdGF0ZW1lbnQ-PC9Bc3NlcnRpb24-

The specification includes a [number of elements](http://tools.ietf.org/html/draft-ietf-oauth-saml2-bearer-13#section-3) that must be included the assertion in order to provide the authorisation server with all the details it needs about the request.

As I understand it there is also an optional `scope` parameter that can be passed in the request any scopes that the client/SDP requires.