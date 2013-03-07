---
layout: default
published: true
permalink: /2013/02/twitter-official-clients-have-their-oauth-keys-leak/
title: "Twitter's official clients have their OAuth keys leak"
date: "2013-03-07 15:00:00"
---

Someone [has posted a Github Gist](https://gist.github.com/re4k/3878505) with all of the client identifiers and secret keys for the official Twitter clients on various platforms.

This just highlights why it is imperitive that you [don't screw up like Facebook did](http://alexbilbie.com/2013/02/facebooks-oauth-problem/) by allowing wildcard client redirect URIs.

The client redirect URIs **must** be absolute and stored on the authorisation server.

## So what can Twitter do about this?

They could release new binarys for all of their clients with new keys and invalidate the old keys, but clients that haven't been updated will suddenly stop working and leave users confused and frustrated. Also it will be trivial for the new keys to be leaked too.

Alternatively they could come up with some sort of mechanism to allow the official clients to download new keys on the fly but then these could be captured with a man-in-the-middle attack.

## But is it actually a problem?

First, it is important to recognise that this is really only a problem for desktop/mobile app clients (assuming that is that web server based clients themselves keep their keys secure - the differnece being that someone would have to break into a web server to get to them).

Therefore as long as Twitter aren't giving their own applications additional functionality that 3rd party clients can't replicate then there isn't too much of a problem because with this equal playing field scenario it could be Tweetbot or MetroTwit we're talking about.

Having said that, the official iOS mobile client at least does allow you to register a new account, so these leaked keys could be used to create spam accounts.

With 3rd party Twitter clients being limited to 100,000 access tokens this could tempt some to make use of these keys however if they were caught then they risk being named and shamed by the media and that in turn could also result in developer accounts being banned by Apple and Google from their app stores.

## Is OAuth to blame?

No, the OAuth protocol is behaving as it designed to here. That is, if a 3rd party client presented a correctly formed request with these valid keys and the correct redirect URI then, assuming Twitter doesn't have some extra security in place to validate requests using their keys (such as validating user-agent strings for example), the authorisation server will assume the request is genuine.