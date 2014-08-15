---
title: Open Source Guilt
date: "2014-08-15 13:30:00"
layout: post
---

For the past few years I've "maintained" two very popular PHP libraries; an [OAuth 2 Server](https://github.com/thephpleague/oauth2-server) and an [OAuth 2 Client](https://github.com/thephpleague/oauth2-client).

I've willingly and happily poured hours of my life into both projects. This was easily done when I worked for the University of Lincoln as both libraries were outputs of a project that I worked on. After leaving the university I moved to London and my life _"got flipped-turned upside down"_ (as Will Smith once put it) which naturally resulted in a reduction in the number of commits that went into the projects.

This didn't concern me too much though; as far as I was concerned at the time the code worked, it was documented and I could point to a number of blog posts with solutions to common problems. I received numerous emails from other developers thanking me for my work on the projects and a number of developers in the PHP community that I respected promoted the library, invited me to talk on podcasts and offered me freelance opportunities to implement the libraries into their codebases. I was happy.

The second half of 2013 I reworked some of the server code which was quickly becoming the more popular of the two libraries and I released version 3 which improved the library's compliance with the OAuth 2.0 specification and the code itself was much cleaner. I tagged version 3, and I was again happy.

But I was lazy. I didn't update the documentation and I didn't update my blog posts which had example code implementations. Github issues started to appear asking for help, but some very kind developers who were interested in the project helped me answer them. The emails changed from thanks and congratulations to calls for help; and at one point I was receiving upwards of 30 emails a week.

Late 2013 I started working on version 4 to correct some pet-peeves I had with the code. I had become a benevolent dictator of the project and rejected numerous improvements to the existing codebase. I did my best with the emails piling up in my inbox but I also ignored many. My focus was only on the new version, because to me I knew it was better and I was able to easily implement it and I became frustrated by people who asked for help suggesting they just read the code and work it out.

During this time (from about November to March this year) I feel that on reflection I was fundamentally depressed and fed up. I felt terribly burnt out and couldn't even comprehend looking at a computer screen once I got home from work, I was unhappy with where I was living and I felt immense pressure because so many other developers were depending on me for help working with my code. Some people started to call me out publicly - and rightly so, others were rude and hurtful towards to me and I'm ever grateful to Phil Sturgeon for shooting them down for being arseholes. I'd hit a rut and I needed to get out of it.

Change started earlier this year when I moved house, and at the end of May I left my old job. I took a break between starting a new job, and I started exercising. I improved my diet, made some new friends and found enjoyment in coding again.

However I still had the issue of my projects. Others had worked on the client library and improved it, but my server library had got to about 35 open issues (most of which concerned the lack of documentation) and I'd not commented on them in weeks. I'd missed my promises to others of getting some examples out and actually documenting the current stable version. [Another OAuth 2 server project](https://github.com/bshaffer/oauth2-server-php) also overtook mine in popularity (and rightly so).

About a fortnight ago I received an email from a developer which immensely upset me; he wasn't rude or angry but expressed his immense disappointment in me. I knew at that point that I had to get my act into gear and stop avoiding this elephant in the room.

And I've really tried. I've polished off version 4, merged it into the `develop` branch and finally written some goddamn [documentation](http://oauth2.thephpleague.com) for it. As soon as that is finished (including an upgrade guide from version 3) I'm going to merge everything into master. Then I'm going to sort out the client library and bring that up to spec.

> _"With great power comes great responsibility"_

Releasing open source projects is a great feeling however there are a number of considerations one should bear in mind:

1. **Actions have consequences** - consciously making the decision to put something you've made on the web with an open source license with the expectation that others may find it useful has a side effect; other developers may come to you with questions, problems, and security concerns. You have a responsibility to do your best to help them out if you care about the integrity, security and reputation of your project.
2. **People want to help** - others may come to you with suggestions to improve or change the codebase. Sometimes these changes can be great and you'll learn from them (I certainly have). Other times the change might be a good one but it may not fit in with your coding style, your future plans of the project or the code layout, and you should help the person making the change adapt it as necessary. Finally some suggestions may not be appropriate and you should politely explain why and offer suggestions as to how they can make more useful improvement.
3. **Your personal reputation is on the line** - your name and email address will generally be embedded somewhere in the codebase, in git commit logs and in pretty graph visualisations on Github. This includes when you interact with others in bug trackers, and emailing others directly (nothing can stop them taking a screenshot and posting it on Twitter). If you act like an arsehole people will find it and call you out.
4. **Popular open source projects work well when the authors are using the project regularly themselves** - one of the reasons why I have continued to work on the OAuth Server project more than the others is because I'm regularly implementing it in my personal projects or as part of my day to day job.

Based on my experiences in recent months I'm going to try the following to make my life easier and happier:

1. I'm going to set out clear roadmaps for the project and more openly invite others to contribute to features.
2. Document answers to the most common questions and problems so that I can point others to them in the future.
3. Encourage people to ask questions - whilst this might seem counterproductive to my bulging inbox problem, I'm going to ask people to contact [The PHP League's mailing list](https://groups.google.com/forum/#!forum/thephpleague) rather than contact me directly; this way multiple people can contribute answers.
4. Be more honest about _my_ availability to work on new features.