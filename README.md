# Welcome to the code repository for Jamikal.xyz

Built with Hugo, Github Actions, and AWS.

The write-up is posted [here:](https://jamikal.xyz/posts/my-first-post/) but the basic workflow is:

1. Create a .MD file using the Hugo command ```hugo new content content/posts/[blog-title-here].md```

   Here is where I create the blog, add all the images and other content. I also use ```hugo server -D``` to build a draft of the site. If everything looks good at this step I save the changes. I also remove the draft tag      from the newly created content file.

2. Push the changes to Github.

   There is no need to build this locally since I build my Hugo site through a Github action.

3. Build the site

    Once the changes are pushed to Github, a Github action will build the site and AWS S3 Sync will push the code changes to an S3 bucket, which is enabled with static site hosting.

Overall, this allows me to focus on creating the blog posts with minimal overhead.
