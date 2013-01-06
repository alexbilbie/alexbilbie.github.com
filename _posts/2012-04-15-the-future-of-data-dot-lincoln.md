---
layout: default
title: The future of data.lincoln.ac.uk
permalink: /2012/04/the-future-of-data-lincoln/
---

For the last year we have been [documenting a number of APIs](https://github.com/unilincoln-ost/Nucleus-Docs/wiki) we have developed that allow anyone inside or outside the university to interact with public and private datasets that we have compiled. These datasets include academic timetable events, building and room locations, the staff directory, building energy statistics, blog tags, and many more that we've never documented. Many of these datasets were created as result of JISC projects we've done to date.

Following a [LNCD](http://lncd.org/) away day last week we've agreed to formalise our commitment to the data.ac.uk movement by publishing [5★ linked data](http://lab.linkeddata.deri.ie/2010/star-scheme-by-example/) on [data.lincoln.ac.uk](http://data.lincoln.ac.uk/) complete with a [SPARQL](http://en.wikipedia.org/wiki/SPARQL) endpoint.

The problem we've had to date with our set up was that our open data was being hosted on the same server as our Nucleus platform which powers our authenticated APIs. Nucleus is now powering more and more services at the university and so in order to ensure quality of service we need to create a formal distinction between the two platforms.

Before we can publish any data we need to agree on an open license for it. [Joss](http://id.online.lincoln.ac.uk/sam/jwinn) will meet with the university's IP manager and consult with our friends at other universities as to an appropriate license.

So what open data sets will we be publishing? Well, here is what we can commit to to start off with:

* __Course data__ - [Jamie](http://id.online.lincoln.ac.uk/sam/jmahoney) has been working hard on collecting and categorising the university's course information as part of the [On Course project](http://coursedata.blogs.lincoln.ac.uk/). Soon we will have full XCRI-CAP 1.2 feed for all credit-bearing courses at all levels across the institution.
* __Location data__ - The university's estates department has just moved to a brand new estates management system which stores more data about campuses, buildings, floors and rooms than they have stored in one place before.
* __Energy data__ - For almost all of the buildings on our Brayford campus we have hourly energy data readings going back as far as December 2009.
* __People data__ - [Nick](http://id.online.lincoln.ac.uk/sam/nijackson) and I have worked with the marketing department on developing a new staff directory which in turn powers the staff lists on the corporate website (via an RDF-XML dump!), so we already have beautifully semantically marked up staff profiles available for all members of staff.
* __Bibliographic data__ - As part of the Jerome project, we cracked open the university library's digital catalogues and stored the data in a sane-format (i.e. not MARC). Now through the [CLOCK project](http://clock.blogs.lincoln.ac.uk/) the data will be semantically marked-up and compatible with other institutions bibliographic data.

In the future we may be able to add event data, blog data, and who knows what else...

We have also recently set up [id.lincoln.ac.uk](http://id.online.lincoln.ac.uk/) which forwards identifiers onto HTML representations e.g. [http://id.lincoln.ac.uk/sam/abilbie](http://id.online.lincoln.ac.uk/sam/abilbie) redirects to my entry on the phone directory. This is currently a virtualhost with alias endpoints however it needs extending so that it also forwards accept headers.

So, our plan of action going forward is:

1. Agree on an open data license.
2. Set up a new server which will host the data.lincoln.ac.uk platform.
3. Collect and mark-up ALL OF THE DATA.
4. Extend id.lincoln so that it forwards accept headers.
5. Document everything.

Also of note, [Chris Gutteridge](http://www.ecs.soton.ac.uk/people/cjg) has now secured the [data.ac.uk](http://data.ac.uk/) domain and we will do everything we can to support the development of the platform and the community.