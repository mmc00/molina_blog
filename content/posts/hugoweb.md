---
title: "My Hugo website with AWS"
date: 2022-08-31T19:40:41Z
draft: false
tags: ["personal"]
categories: ["projects"]
featuredImage: "/img/awss3.png"
---

Some time ago I wanted to have a website where I could have my projects, like a portfolio website where I could share my code, my notebooks to anyone who needed it. 
I tried Hugo, because a I liked how fast it was and the ease Markdown to create html code, in that attempt, I was using Rstudio and the Academic theme (before it was converted to wowchemy),
but I was not convinced at all with result. The code, at that time, was deployed using Netlify.

Now, I decided to try with a new theme and using AWS to practice (I’m trying to be a Cloud Developer, so this would help get me hands on).
The process wasn’t as easy as before, but it’s still easy and now I have control of every event in the pipeline. So basically, the process was to use some AWS services, to get a website with Continuous Delivery, which means that I want the process to be as automated as possible,
to reduce human errors.

I followed two tutorials: the first one from [AWS](https://hosting-hugo-content.workshop.aws/05_what_is_built.html]) and the second one from
[Noah Gift](https://www.youtube.com/watch?v=i-kSm2ZqFcs) the latter was where I relied more on. The logic behind this is to create a repo on GitHub the website content, then a Cloud9 instance where you create the content and modify files.
After that, you use CodeBuild as Continuous Delivery tool, so every push you make, you build the page with the changes, using a Docker image. Finally, it stores the page in a s3 bucket, which is available to everyone. The diagram of the pipeline:

<br> 

![diagram](/img/hugo_diagram.jpg "Static website in AWS with CD")

If I have to say what was the most difficult part, I would divide it into two: setting the correct permissions to the s3 bucket,
so that it is available to everyone and can be modified by CodeBuild, and the right ports open on the EC2 instance (for Cloud9 deployment).
By following the tutorials, you can have a web site very quickly and store all your projects in an organized way, pretty cool isn't it?
