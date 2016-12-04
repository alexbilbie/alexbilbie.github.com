---
title: "Using AWS CodePipeline and CodeBuild to update a Jekyll website"
layout: post
tags: [aws,devops]
---

This week at the AWS Re:Invent 2016 event in Las Vegas a new [CodeBuild](https://aws.amazon.com/codebuild/) service was introduced. CodeBuild is essentially a build service which given an input (generally code), will process it in some way and then output a build artifact.

My [alexbilbie.com](https://alexbilbie.com/) blog is a static website created with [Jekyll](https://jekyllrb.com/) and hosted with Github Pages. I've been wanting for sometime to move away from Github Pages and store the site in an S3 bucket however this would require some sort of mechanism to update the S3 hosted version whenever I publish a new post (which Github Pages does automatically). There are a few methods to this using CircleCI and TravisCI out there but I wanted to see if CodeBuild could be used instead.

My first step was to setup a new S3 bucket (using the very speedy new S3 console):

<figure>
  <img src="/images/codebuild-jekyll/0create-bucket.png" />
</figure>

The bucket needs to be configured for static website hosting which is trivial to enable in just a few clicks:

<figure>
  <img src="/images/codebuild-jekyll/1bucket-static-website.png" />
</figure>

Finally the bucket policy needs updating to allow anonymous reads:

<figure>
  <img src="/images/codebuild-jekyll/2bucket-permissions.png" />
</figure>

```json
{
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::alexbilbiecom/*"
        }
    ]
}
```

Then I navigated to the CodeBuild console and created a new project using the following settings:

<figure>
  <img src="/images/codebuild-jekyll/3codebuild-create-project.png" />
</figure>

* Project name: `alexbilbie_com`
* Source provider: `github`
* Repository: `alexbilbie.github.com`
* Environment image: `Use an image maanged by AWS CodeBuild`
* Operating System: `Ubuntu` (currently the only available option)
* Runtime: `Ruby`
* Version: `ruby:2.3.1`
* Build specification: `Use the buildspec.yml in the source code root directory`
* Artifact type: `No artifact`
* Service role: `Create a service role in your account`

Through some experimentation I discovered that if you let CodeBuild automatically upload the Jekyll build output then you can't have the site in the root of the bucket (it will be under `<artifact name>/_site/` prefix). The CodeBuild Ruby image comes with the AWS CLI pre-installed so I used a build command to upload the result directly to the bucket root using `aws s3 sync` command.

Next up I edited the service role that the CodeBuild wizard created to allow write access to the website S3 bucket. Normally I would create the service role manually however at the time of writing you can't create a CodeBuild service role (at least through the console).

I added the following to the generated policy:

<figure>
  <img src="/images/codebuild-jekyll/5iam-policy-permission.png" />
</figure>

```
{
    "Effect": "Allow",
    "Resource": [
        "arn:aws:s3:::alexbilbiecom",
        "arn:aws:s3:::alexbilbiecom/*"
    ],
    "Action": [
        "s3:PutObject",
        "s3:Get*",
        "s3:List*"
    ]
}
```

The final step was to add a `buildspec.yml` file to the root of the Github repository:

```
version: 0.1
   
phases:
  install:
    commands:
      - gem install jekyll jekyll-paginate jekyll-sitemap jekyll-gist
  build:
    commands:
      - echo "******** Building Jekyll site ********"
      - jekyll build
      - echo "******** Uploading to S3 ********"
      - aws s3 sync _site/ s3://alexbilbiecom
```

Inside the `buildspec.yml` you can hook into the `install`, `pre_build`, `build` and `post_build` lifecycle events as well as specify artifacts to upload to S3. In this case I just needed to hook into `install` and `build` events.

From here you can start a new build, I used the default settings and clicked `Start Build`.

<figure>
  <img src="/images/codebuild-jekyll/6new-build.png" />
</figure>

A few minutes later...success!

<figure>
  <img src="/images/codebuild-jekyll/7build-success.png" />
</figure>

At this point I could navigate to the bucket static site URL to verify the generated site was working correctly.

CodeBuild is set up but it currently requires manual triggering of build. Fortunately last year's new service, CodePipeline can help out here.

In the CodePipeline console I created a new pipeline linked to the Github repository:

<figure>
  <img src="/images/codebuild-jekyll/8new-pipeline.png" />
</figure>

Under the build setting I selected `AWS CodeBuild` as the build provider and selected the CodeBuild project I'd created.

**Note:** Currently CodePipeline requires the build provider to produce an output which the CodeBuild project doesn't currently have. I went back to the CodeBuild settings and set them to this:

<figure>
  <img src="/images/codebuild-jekyll/codebuild-artifact.png" />
</figure>

I skipped the `beta` section of the wizard as a deployment isn't required.

<figure>
  <img src="/images/codebuild-jekyll/9pipeline-build.png" />
</figure>

Finally I let the console create a CodePipeline service IAM role for me and completed the wizard.

<figure>
  <img src="/images/codebuild-jekyll/10pipeline-beta.png" />
</figure>

<figure>
  <img src="/images/codebuild-jekyll/11pipeline-service-role.png" />
</figure>

Now whenever I make a change in the Github repository CodePipeline will automatically trigger CodeBuild and my website will be updated.

<figure>
  <img src="/images/codebuild-jekyll/12pipeline-view.png" />
</figure>

From my brief look at CodeBuild it looks like the start of a really useful service. Usually I  set clients up with a Jenkins build server however from my brief play here and some reading of the documentation I'm keen to explore the service more to see to what extent it could potentially replace Jenkins in the future.

