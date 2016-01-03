---
title: "Developing an interactive county council election map (part two)"
date: "2013-05-08 09:00:00"
layout: post
published: true
tags: [hacks]
---

> This is a follow-up to my first post: [Developing an interactive county council election map (part one)](http://alexbilbie.com/2013/04/developing-an-interactive-county-council-election-map-part-one/).

Last Thursday (and Friday) proved to be an interesting day, both for myself and the people of Lincolnshire. It’s been well documented by the media how well UKIP did both nationally but in Lincolnshire it was especially interesting as we’ve been a blue county for decades and we now have a very different (and colourful) political landscape.

Here is how the map looked before and after the election:

<figure>
![http://thelincolnite.co.uk/wp-content/uploads/2013/05/comparison.jpg](http://thelincolnite.co.uk/wp-content/uploads/2013/05/comparison.jpg)
</figure>

On Thursday morning I was interviewed by journalism.co.uk as they were really interested in what The Lincolnite and I had been up to. Check out the article here [http://www.journalism.co.uk/news/hyperlocal-site-the-lincolnite-cover-elections-live-interactive-map/s2/a552859/](http://www.journalism.co.uk/news/hyperlocal-site-the-lincolnite-cover-elections-live-interactive-map/s2/a552859/).

I spent Thursday evening sat with the guys from [The Lincolnite](http://thelincolnite.co.uk) drinking cold water and redbull trying to stay awake as we sat in the county council chamber from 9:30pm until almost 5am waiting for results to come in from across the county.

I took a few photos over the course of the night:

When results came in these boards would be updated with the results

<figure>
![When results came in these boards would be updated with the results](/images/elections-1.jpg)
</figure>

The media teams (The Lincolnite team, BBC Radio Lincolnshire and BBC stringers calling the results into London)

<figure>
![The media teams](/images/elections-2.jpg)
</figure>

An example results paper that was filled out and passed around when results were phoned in from across the county

<figure>
![An example results paper that was filled out and passed around when results were phoned in from across the county](
/images/elections-3.jpg)
</figure>

Taken around 4am as we waited for the final few results to come in. By this point a few other journalists had turned up as well as successful candidates, political wonks and other interested parties

<figure>
![/images/elections-4.jpg](/images/elections-4.jpg)
</figure>

<figure>
![/images/elections-5.jpg](/images/elections-5.jpg)
</figure>

For the interactive map Daniel (The Lincolnite’s editor) and I really liked the idea of having a “bar of power” across the top of the map which showed a visual makeup of the 77 divisions and was sorted left to right by the parties with the most seats. Each little division was clickable too and zoomed into the map. It also proved a useful way of seeing how many seats were left to be called as some of the divisions such as Stamford	North are tiny to see on the map.

<figure>
![/images/elections-part-two-map.png](/images/elections-part-two-map.png)
</figure>

At 10pm (when voting stops) we switched the map to one that had no colour and just showed the outlines of the different divisions. Every 30 seconds an ajax request polled the server for the latest results and updated the map and bar of power with the winning colour and when a user zoomed into an area it would show the final results.

<figure>
![/images/elections-part-two-zoom.png](/images/elections-part-two-zoom.png)
</figure>

As results came in I sat and updated my very sexy admin interface with the number of votes each candidate received:

<figure>
![/images/elections-entry-form.png](/images/elections-entry-form.png)
</figure>

The Lincolnite team were [running a live blog](http://thelincolnite.co.uk/2013/05/lincolnshire-local-elections-2013-results/) which had the map embedded at the top so for the many readers who were keeping an eye on the page throughout the night they had a nice realtime (ish) experience.

I learnt a few new things whilst developing the updated map. The main thing was how [jQuery promises](http://api.jquery.com/promise/) worked which meant that until all 77 divisions had loaded (almost 2mb of JSON) the ajax poller wouldn’t start. I also included a nice little loading message too which appeared and disappeared as the status of the deferred object changed.

I also learnt a lot more about the [Leaflet.js](http://leafletjs.com) API and I’m definitely going to use it again if I can come up with another geospacial project to work on.

We learnt a few things from this little experiment.

First of all is that we need to work really closely with the county council to try and get all of the data required in a more usable format (such as CSV) as we wasted so much time going through 77 PDFs and manually copy and pasting names, parties and districts.

Next we need to work with the local party associations so they can update their candidates biographical information themselves without them having to speak to us every time they want someone’s photo changing.

[Tony Hirst wrote a nice roundup](http://blog.ouseful.info/2013/05/02/uk-local-elections-2013-live-data-live-maps/) of other websites who also created live maps. He also speculates if “this will be the last round of elections without a national live data feed from somewhere pushing out the results in a standardised form”. I’ve been thinking about this a lot and I’m wondering if it might be worth teaming up with [OpenlyLocal](http://openlylocal.com/) and [MySociety](http://mysociety.org) to create some sort of national service. Perhaps more on that later?

All in all I had great fun making the maps, I learnt some new stuff, The Lincolnite got some well deserved coverage and praise and I also decided that I don’t find myself waiting for Stamford North to finish counting their votes at 4:30am again.