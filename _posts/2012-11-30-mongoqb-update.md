---
layout: post
title: "MongoQB updated for Mongo PECL 1.3"
permalink: /2012/11/mongoqb-update/
---

I have updated [MongoQB](https://github.com/alexbilbie/MongoQB) to support the new MongoDB PECL update ([v1.3](http://pecl.php.net/package/mongo/1.3.0)) which introduces some big changes to the base classes as described [in this blog post on the MongoDB blog](http://blog.mongodb.org/post/36666163412/introducing-mongoclient).

The only big change for developers using the library is you don't need to pass the `persist` and `persist_key` keys into the connection array.