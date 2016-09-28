---
title: "Starting a Jenkins multi-branch pipeline build from a Bitbucket commit"
layout: post
tags: [devops]
---

After some experimentation I've finally worked out how to start a Jenkins multi-pipeline build via a notification from Bitbucket when someone pushes a commit.

The first step is to disable CSRF protection (I know...I know...but it's necessary to allow remote access). You can do this by selecting _Manage Jenkins_, _Configure Global Security_ then unchecking the _Prevent Cross Site Request Forgery exploits_ option.

Next step, navigate into your job from the main page and select _Branch Indexing_ from the menu on the left. From this page right click on the link that says _Run Now_, copy the link address and paste it into a text editor.

Now go back to the Jenkins root menu and select _People_, then choose your user (or preferably a dedicated Jenkins user), choose _Configure_, and then reveal the API token. Copy it and head back to your text editor.

Assuming the branch indexing run now URL is `https://jenkins.example.com/job/my-project/build?delay=0`, the Jenkins user is `jenkins` and the API token is `RdFrCiEwgs9boUsJVHoi` modify the URL so it looks like this:

`https://jenkins:RdFrCiEwgs9boUsJVHoi@jenkins.example.com/job/my-project/build`

Next go into the settings of your Bitbucket and create a new webhook, paste in the modified link and save the form.

Commit something and you should see that a moment later Jenkins will run a branch index on the job and then run the build for the branch you committed to.

Multi-branch pipelines are a very new feature to Jenkins 2.0 and are still very part-baked in my opinion. As I get more proficient I will write a tutorial because whilst frustrating to get setup and going they're a huge time saver.