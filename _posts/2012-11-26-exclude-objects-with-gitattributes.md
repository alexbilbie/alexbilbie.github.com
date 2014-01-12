---
layout: post
title: Exclude objects with .gitattributes
permalink: /2012/11/exclude-objects-with-gitattributes/
---

Not many people know about the incredibly useful `.gitattributes` file which can be used to tell git how to handle certain files or folders.

One useful use case is when you need to distribute zip/tarballs of your repository but you don't want to share certain files or folders such as unit tests or build configuration scripts.

In the repository root add a new `.gitattributes` file and for each file or folder you want to exclude add `export-ignore` after it:

<pre>
tests/ export-ignore
build.xml export-ignore
phpunit.xml export-ignore
</pre>