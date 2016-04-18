---
title: "league/oauth2-server version 5.0 is out!"
date: "2016-04-18 08:00:00"
layout: post
tags: [oauth]
---

Coming just over a year since the last major release, version 5.0 is a complete code rewrite with many improvements:

* PSR-7 support
* Improved OAuth 2 specification support (public clients, implicit grant)
* JWT support by default
* Significantly simplified interfaces make getting started faster and easier
* Rewritten documentation and examples

It's simpler than ever to created an authorisation server; here is an example using the Slim Framework with support for the resource owner password credentials grant:

<script src="https://gist.github.com/alexbilbie/1f19a3cc5dbfc08fc822ad00490cd78c.js"></script>


Your API endpoints can easily be secured using the resource server PSR-7 middleware:

<script src="https://gist.github.com/alexbilbie/face0c52f60c621edd301c2be5c0f1a8.js"></script>

Check out the new documentation here - [https://oauth2.thephpleague.com](https://oauth2.thephpleague.com). If you have any questions please feel free to [open a Github issue](https://github.com/thephpleague/oauth2-server/issues/new).

I'd like to thank [everyone who has contributed](https://github.com/thephpleague/oauth2-server/graphs/contributors?from=2015-08-02&to=2016-04-18&type=c) to this release, especially [Julián Gutiérrez](https://github.com/juliangut) and [Frederik Bosch](https://github.com/frederikbosch).

I'm now an independent software developer so if you'd to hire me to help out with your OAuth or API implementation, or would like a consultation session please send me an email and we can work something out - [hello@glyndelabs.com](mailto:hello@glyndelabs.com).
