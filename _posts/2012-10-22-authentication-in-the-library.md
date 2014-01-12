---
layout: post
permalink: /2012/10/authentication-in-the-library/
title: Authentication in the Library
---

Last week, [Paul](http://lncn.eu/me/pstainthorp) and I sat down to list the IT resources that the library offer to staff and students are and how they authenticate users. We also identified services that allow us to customise the sign in experience in order to achieve at least stage one of our strategy for consistent sign in:

> 1) To ensure a single, consistent identity for each person, all library (and ICT) applications that we operate internally must have Active Directory sign-in instead of local databases. Almost all of our applications achieve this already.
_(source [Linkey bid document](https://github.com/lncd/AIM-project/blob/master/Linkey%20JISC%2001-12%20DI%20Proposal.pdf?raw=true) - section 3.1)_

Before proceeding there is some jargon to explain:

* **SAM** - A SAM (security accounts manager) ID is a user's AD username. For staff this will generally be the first letter of their forename + their surname - e.g. for me this is _abilbie_. For students this is their student number - e.g. _01234567_.
* **Password** - This a user's AD password.
* **Employee ID** - for staff this is the ID number from our employee database. For students this is the same as their SAM ID.
* **SafeCom PIN** - SafeCom is our printing authentication service we use here at the university. Each staff and student has their own PIN number which they use with their employee ID.

This is the list we came up with:

1. **Horizon Information Portal (HIP)** - This service allows staff and students to interact with things they've borrowed or want to borrow, for example renewing and reserving books and viewing fines. We can edit [XSL](http://en.wikipedia.org/wiki/XSL) files which allow us to change the design of HIP. We can also add JavaScript files. HIP authenticates with a user's employee ID and PIN number.
2. **library.lincoln.ac.uk** - Hosted on our [WordPress](http://blogs.lincoln.ac.uk) platform. We can completely customise every aspect of the site. Users sign in with their SAM ID and password.
3. **Blackboard** - Our [VLE](http://en.wikipedia.org/wiki/Virtual_learning_environment). If a user is signed in it will provide infomation about their library account plus they can pay any fines. Digitised material stored is also stored in and access is granted based on a students' course. Users sign in with their SAM ID and password.
4. **Clio** - inter-library loans management website. Users sign in with their SAM ID and password. We can completely customise the experience.
5. **EPrints** - our research repository. [EPrints](http://www.eprints.org/) is open source so we can change anything. Users sign in with their SAM ID and password.
6. **EZProxy** - allows for e-resources access based on IP address proxying. EZProxy is LDAP capable however it can inherit Blackboard/SharePoint authentication sessions. We can completely customise the experience.
7. **Open Athens LA (Local Authentication)** - Enables users to securely access to online resources. Provides Athens-&gt;Athens, and Athens/SAML-&gt;UK Federation-&gt;SAML authentication. Users sign in with their SAM ID and password. This should be installed in the coming weeks. We _think_ we can customise the experience.
8. **Resources that have their own username + password**. A very subset of online resources the library subscribe don't work with Athens or EZProxy and so give us a username and password to use. Users are directed to these services through our SharePoint site and are authenticate with these services in two ways:
1. The username and password are automatically injected into the service's authentication screen with a JavaScript script or
2. The user is a presented with a 401 dialog and they have to manually enter the username and password.
9. **Thin Clients** - The GCW library on the Brayford campus over the summer had a number of [thin client](http://en.wikipedia.org/wiki/Thin_client) computers installed. When physically in front of the computer users' authenticate with their SAM ID and password but because these machines use virtual instances we could provide a "desktop in the browser" experience via UAG.
10. **Find it @ Lincoln** - A search engine of journals and databases that we subscribe to that is hosted remotely by Ebsco. Authentication is delegated to EZProxy.
11. **RefWorks** - Allows users to collect references. Authentication is via Athens. We can't customise the look and feel of RefWorks itself but we hope we can customise how our Open Athens LA looks.
12. **Aspire** - Reading List software provided by Talis. Authentication is via Athens. See above note about customising Athens LA.
13. **Journals A to Z / OpenURL resolver** - As above
14. **Databases** - A combination of Athens and EZProxy authentication.
15. **Printing** - The physical printers in the library require authentication using a user's employee ID and their PIN number.
16. **Self service kiosks** - These kiosks authenticate users by requiring them to scan their staff/student card (which has a [code 39 barcode ](http://en.wikipedia.org/wiki/Code_39) of their employee ID and then typing in their PIN onscreen. We can't customise this experience.
17. **Print top up kiosks** - These kiosks authenticate users by requiring them to type in their SAM ID and password. We can't customise this experience.

Visually of this looks like:

<figure>
![http://i.imgur.com/nEKo4.png](http://i.imgur.com/nEKo4.png)](http://i.imgur.com/nEKo4.png)
</figure>

When the UAG is installed we should be able to easily hook up services that require SAM ID and password over LDAP. Other services that use alternative authentication such as HIP will require us to write some middleware that will translate between SAM ID + password to employee ID + PIN.

Assuming it is as simple as that, then when these services are hooked up to the UAG then the map will look like this:

<figure>
[![http://i.imgur.com/dyV6C.png](http://i.imgur.com/dyV6C.png)](http://i.imgur.com/dyV6C.png)
</figure>

The model assumes that eventually the UAG will be like a Gateway for users to access most resources.

LDAP inject means that once a user has authenticated with the UAG their SAM ID and password will be stored in a session, and then when the user visits a service that uses LDAP authentication to the AD, UAG will inject their username and password into the sign-in form and click the submit button for them. At the end of the day single sign-in is essentially a user experience which takes some of the pain with accessing resources away from them.