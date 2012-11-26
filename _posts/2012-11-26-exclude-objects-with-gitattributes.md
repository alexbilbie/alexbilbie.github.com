---
layout: post
title: Exclude objects with .gitattributes
permalink: /2012/11/exclude-objects-with-gitattributes/
---

Not many people know about the `.gitattributes` file. It essentially is file which can be used to tell git how to handle certains files or paths.

One useful usecase is when you need to distribute zip/tarballs of your repository but you don't want to share certains files or folders such as unit tests or build configuration scripts.

In the repository route add a new `.gitattributes` file and for each file or folder you want to exclude add `export-ignore` after it:

	tests/ export-ignore
	build.xml export-ignore
	phpunit.xml export-ignore

