---
layout: post
published: false
title: "The importance of useful data"
permalink: /2013/02/the-importance-of-useful-data
date: "2013-02-27 12:10:00"
---

I've just been doing some tweaking of our importer scripts and I discovered an amusing code comment in some of my colleague [Nick](https://twitter.com/jacksonj04)'s code:

	// WARNING: Here be date parsing!

	/*

		  _______
		 |/      |
		 |      (_)
		 |       |
		 |      /|\
		 |      / \
		 |
		_|___

	 */

Every so often we integrate new sources of data into our data warehouse. Generally these will be sourced from APIs exposed by other systems used internally here at the university.

Following the integration of a new source of data Nick has written a blog post today entitled ["the importance of useful data"](http://orbital.blogs.lincoln.ac.uk/2013/02/27/the-importance-of-useful-data/) which explains the above code comment:

> It saddens me, therefore, that during integration with some other applications both inside and outside the University we are forced to tackle data – often purported to be “machine readable” or “ready for reuse” which has clearly not been looked at by the eye of somebody who wants to reuse it. As an example, one source of data provides a date range which is stored internally (as far as I can gather) as two distinct values; there is a “start date” and there is an “end date”. These are provided through the UI as structured inputs (a date picker) which ensures they’re entered (and presumably then stored) in an expected format which can be manipulated as necessary. The API then chooses to express this date range not as a distinct “start date” and “end date”, but instead as a single “dates”.
>
> You may think that this isn’t such a big problem – after all, how difficult can it be to parse 04/02/2013 - 07/03/2014? In that example it’s actually pretty easy once you’ve decided if you’re using UK or US style dates. The ISO date format can solve this though, giving us 2013-02-04 - 2014-03-07. Sadly, this isn’t what we get. In fact, here are the four (yes, four) distinct ways that “dates” can be represented:
>
> * `2013-02-04 to 2013-02-04` becomes 4 Feb 2013
> * `2013-02-04 to 2014-03-07` becomes 4 Feb 2013 - 7 Mar 2014
> * `2013-02-04 to 2013-03-07` becomes 4 Feb - 7 Mar 2013
> * `2013-02-04 to 2013-02-07` becomes 4 Feb 2013 - 7 Feb 2013
>
> So, the rule becomes that if the dates are the same you just show the single date, but if the dates are different then you show two dates, unless they are in the same year in which case you only show the year in the final date, unless they are in the same year and the same month, in which case you show two dates. And then you format all the dates with a locale-specific short form of the month name.

