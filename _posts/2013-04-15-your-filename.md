---
layout: default
published: false
title: "Awesome Nucleus People querying"
date: "2011-07-28 11:33:00"
---

I’ve spent some time over the last few days working on the personalisation engine for our Jerome project and it’s required me to extend our Nucleus People datastore with some new data and new querying capabilities (which I’ll document on data.lincoln.ac.uk soon).
We can now express queries like:

> “List all the students who are in their 2nd year studying, and do unit ABC on course X or course Y but not course Z”

> “List all of the staff based at our Riseholme campus who have technician in their job title”

> “List all of the offices for the academics based in the MHT building on the Brayford campus”

I’m going to spin up a Sphinx database next week with every staff and students’ name in the index so that we can do lightning fast name look-ups for auto-complete user interface fields. Coupled with the above queries we can then do some even funkier queries like...

> “List all of the staff who work in ICT Services with the name Alex or Nick or have awesome jobs making sexy APIs on top of university data”

...or something like that.

BOOM.