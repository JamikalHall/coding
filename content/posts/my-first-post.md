---
title: "Building a CI/CD Pipeline with Hugo"
date: 2023-01-20T10:23:27-06:00
draft: false
tags: 
  - AWS 
  - DevOps
  - Projects
---

I wanted to do part of the [Cloud Resume Challenge](https://cloudresumechallenge.dev/docs/the-challenge/aws/).

I decided that I want to be able to work on the later stages of the challenges, but not have to build an entire blog from scratch. I knew I needed to host it myself in an S3 bucket as a static site. I had also been interested in learning a programming language really well, and had several debates between learning Python and Go. I learned that both Terraform and Kubernetes are written in Go, however learning Go would not necessarily translate to being able to use either of those tools more effectively, so I ended up learning a bit of Python. In my research though, I came across Hugo, which is a framework for quickly building static sites. 

Hugo allowed me to focus on building content and not have to think too much about engineering an entire front-end. I could pick a theme, write my content, and Hugo would take care of the rest. 

For this post, we will be covering the folllowing steps:

  2. Writing the resume in HTML
  3. Adding CSS
  4. Deploying the site as an AWS S3 static website
  5. Having the website use HTTPS through Cloudfront
  6. Adding a custom domain name to the CloudFront distribution through Route 53.
  13. Adding the site code to [Github](https://github.com/JamikalHall/playground)
  14. Creating a CI/CD pipeline so that when code is pushed to Github, my site is updated automatically.
  16. Writing a blog post about the experience (what you're currently reading)

In the challenge, Hugo handles steps 2 and 3, and makes steps 4 and 16 a lot easier to me. Doing all these together means that if I stop the challenge with just these steps, I will have at least built my own custom blog hosted on AWS.

To complete steps 2 and 3, I followed the [Quickstart Guide](https://gohugo.io/getting-started/quick-start/) documentation on the Hugo site. Instead of the Ananke theme, I chose the [Paper](https://github.com/nanxiaobei/hugo-paper) theme because I liked the way it looked. After going through those steps, I had a sample site to work with. Now I needed to upload this site to Github.

The first thought I had, is that it would be quite a pain to have to constantly build the Hugo site, then upload it to Github, then upload it to S3, and then open the CLI to invalidate the cache everytime I wanted to update my site. So I figured that doing things once through the GUI is fine, but I need to find a way to automate this ASAP. Rather than build the entire app, I wanted to deploy an MVP (minimum viable product) and have it be available, especially because the other features depend on the front-end being built first.

Once I had the site running locally, I could work on getting to Github, then to my S3 bucket. I started this challenge long ago and did not document the steps to actually set up the S3 bucket for static-site hosting, but I followed these docs.
- https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html to enable an S3 bucket for hosting
- https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-https-requests-s3/ to register a site through Route 53
- https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-https-requests-s3/ to enable Cloudfront to serve HTTPS.

Hugo has a section on [deploying](https://gohugo.io/hosting-and-deployment/hugo-deploy/). There is not a specific document on publishing directly to S3, but there is a bit on hosting on [Github](https://gohugo.io/hosting-and-deployment/hosting-on-github/), specifically the 'Build Hugo with Github Action' section. 

    jobs:
        deploy:
        runs-on: ubuntu-22.04
        steps:
            - uses: actions/checkout@v3

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

This, combined with the AWS S3 Sync command, allowed me to build a pipeline, that on push to the main branch, will get access to AWS S3, build my Hugo site, then Sync the contents of the public directory with my S3 Bucket.

In the past, when pushing files to AWS S3, I would create a dedicated IAM role for communicating with Github, and this role would only have S3 permissions. However, these credentials are long-lived, and if for whatever reason someone got access to those credentials, they would have access to all my S3 resources/whatever permissions this role has. A better solution to this has been outlined [here](https://benoitboure.com/securely-access-your-aws-resources-from-github-actions). The TL;DR is instead of creating an IAM User to control pushing to S3, you create short-lived credentials to allow Github access to update S3 for a length of time that you determine (in my case I stuck with 900 seconds).

Overall, while this post is not as detailed as it could be, I wanted to at least document some of what I accomplished during this first part of the resume challenge. Having this blog setup means I can continue to learn other adjacent ideas and document those as well.

What I learned:
- I really enjoyed the process of building a pipeline. While this iteration of my pipeline is very simple, I can see how you can add complexity by first pushing to a staging branch for testing, then once tests pass, merge to the main branch and build your app. Automating things is fun.
- I realized that I do not currently have any tests in my code because there is very little interactivity(it is a static site after all). As I increase complexity and perhaps add more code and functionality, testing of all sorts will be a priority. I would then reference the above and use Git best practices to update the code.
- Sometimes building something that works (i.e. finishing some aspect of the project) and having that be available is better than trying to build the entire application at once.