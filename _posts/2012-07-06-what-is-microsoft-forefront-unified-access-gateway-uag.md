---
layout: post
title: What is Microsoft Forefront Unified Access Gateway (UAG)?
permalink: /2012/06/what-is-microsoft-forefront-unified-access-gateway-uag/
---

# What is Microsoft Forefront Unified Access Gateway (UAG)

[Microsoft Forefront Unified Access Gateway](http://www.microsoft.com/en-us/server-cloud/forefront/unified-access-gateway.aspx) (UAG) is a piece of server side software which provides secure access to corporate networks, systems and applications. It incorporates a number of different access technologies including VPN, HTTP reverse proxies and the Microsoft-developed technologies DirectAccess and Remote Desktop Services. Remote clients can access these corporate resources through  a special web site that is hosted on an [IIS](http://www.iis.net/) server which is bound to the UAG software.

UAG includes built integrations for [Microsoft Exchange Server](http://www.microsoft.com/exchange/en-us/default.aspx) (2003, 2007 and 2010), [SharePoint Server](http://sharepoint.microsoft.com/en-au/Pages/default.aspx) (2003, 2007 and 2010), Remote Desktop Services and [Citrix Presentational Services](http://www.citrix.com/English/ps2/products/product.asp?contentID=186). It also includes a technology called SSL-VPN which allows for authentication integration with most 3rd party and custom software.

UAG can use a number of different authentication sources including Active Directory, LDAP, RADIUS and SecurID. Finally it can also "speak" SAML and ADFS.

Microsoft identifies a number of benefits of using UAG:

> Forefront Unified Access Gateway (UAG) is designed to provide secure remote access in a way that extends application intelligence, security and control, and ease of use. Key benefits include:

> __Anywhere Access__

> Forefront UAG makes it easier to deliver secure remote access to your applications and resources, and improve employee and partner productivity, by combining an intelligent access policy engine with a variety of connectivity options including SSL VPN and Direct Access.  Forefront UAG:

> Empowers employees, partners, and vendors to be productive from virtually any device or location through integrated SSL VPN capabilities.
Delivers simple and secure access optimised for applications such as SharePoint, Exchange, and Dynamics CRM.
Extends networking connectivity with Windows Direct Access to existing infrastructure and legacy applications.

> __Integrated Security__

> Forefront UAG improves the security in remote access scenarios by enforcing granular access controls and policies that are tailored to the applications being published, the identity of the user, and the health status of the device being used. Forefront UAG further improves security by enabling strong authentication to applications and mitigating the risks of downloaded data from unmanaged devices. Forefront UAG:

> Protects IT assets through fine-grained and built-in policies that provide access to sensitive data based on identity and endpoint health.
Easily integrates with Active Directory and enables a variety of strong authentication methods.
Limits exposure and prevent data leakage to unmanaged endpoints.

> __Simplified Management__

> Forefront UAG offers a single platform through which to deliver and manage remote access. With built in policies and configurations for common applications and devices, you can gain more control, more efficient management, greater visibility, and lower total cost of ownership. Forefront UAG:

> Consolidates remote access infrastructure and management.
Simplifies deployment and ongoing tasks through wizards and built-in policies.
Reduces support costs by delivering a simplified connectivity experience for users.

> Source: _[http://www.microsoft.com/en-us/server-cloud/forefront/unified-access-gateway.aspx](http://www.microsoft.com/en-us/server-cloud/forefront/unified-access-gateway.aspx)_

## How UAG can help us

At the university we have a number of existing web based applications which will benefit from having UAG integration.

We currently have SharePoint and Exchange 2003 installations  (which will soon be upgraded to 2010) which UAG can natively integrate with.

We use [Blackboard Learn](http://www.blackboard.com/platforms/learn/overview.aspx) as our LMS and [Zendesk](http://zendesk.com/) as our support desk software, both of which have can use SAML for single sign on.

We are also looking into potentially using UAG as the access point for Windows 7 thin client access.

## How can UAG work with OAuth?

As UAG can use SAML to communicate with services it means that we can use the [OAuth 2.0 assertions specification](http://tools.ietf.org/wg/oauth/draft-ietf-oauth-assertions/) to create a translation framework between SAML assertions and OAuth tokens. This will extend some of the work that we've originally done on our OAuth server and the updated code will be published.

One of the outcomes of this project will be a case study (with open source code examples) on how UAG and an OAuth server can work together (though because we don't know how this may work at the moment it could be that it turns out they can't work together...) and on this blog we will document our experience.