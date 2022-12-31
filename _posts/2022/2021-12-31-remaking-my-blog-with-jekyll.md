---
layout: post
title: "Remaking my blog with Jekyll"
date: "2022-12-31"
categories: 
  - "blog"
---

I started blogging in May 2011 - more than 10 years ago!
Back then, I had no experience with blogging, but I was interested in writing and creating content.
So I gave it a try, bought a yearly subscription for some webspace (at [https://www.netcup.de](https://www.netcup.de/)) and began to build my blog.

Right from the beginning, I made an obvious decision: 
I installed [Wordpress](https://wordpress.com/) together with a couple of plugins and themes to get started.
And to get things clear, Wordpress did a great job for more than a decade for me!

## Why I want to migrate away from Wordpress

However, since a couple of years, I have the feeling that Wordpress is growing over my head.
I must take care of installing updates, moderating comments, optimize the performance and manage a bunch of plugins which I think I need.
And in the end, it comes all down to this:

> Wordpress is far too powerful for the simple tasks I want to achieve.

## What's the alternative?

As obvious as Wordpress was back in 2011 for me, so is [GitHub pages](https://pages.github.com/) right now.
I'm a software developer and working with [Git](https://git-scm.com/) and [Markdown](https://en.wikipedia.org/wiki/Markdown) is easy for me.
The concept of generating static HTML based on templates ([Jekyll](https://jekyllrb.com/)) feels like a light-weight alternative for a full-fledged CMS like Wordpress.
Exactly what I want!

## Migrating from Wordpress to Jekyll

Migrating from Wordpress to Jekyll was a mix of automated tasks and manual work.
Here's a rough outline of what I did:

1. Exporting all of my posts from Wordpress as a huge XML-file (Tools > Export > Download Export File).
After 10 years of blogging I got about 3,3 MB of pure XML.
2. Converting the XML-file to Markdown by using [wordpress-export-to-markdown](https://github.com/lonekorean/wordpress-export-to-markdown).
This will also download all images. I ended up with about 30 MB of Markdown and images.
3. Choosing a pre-made [Jekyll theme](https://jekyllthemes.io/theme/reverie) that looks clean and fork it.
4. Creating the folder structure for posts and images in the new theme.
I made a folder per year (2022, 2021...) and inside a folder per month (01, 02...).
5. Copying the generated Markdown files to the new folder structure and fix their naming.
The Wordpress export will name every file `index.md`, but the name schema should be `yyyy-mm-dd-my-post-name.md`.
6. Manually checking each file to see if the export/conversion has broken something.
I also fixed typos and rewrote some misleading sentences.
This part took the longest.
7. Commit and push everything to [https://github.com/tuhrig/tuhrig.github.io](https://github.com/tuhrig/tuhrig.github.io).

![](/images/2022/12/blog-project-structure.png)

At this point, my blog was [live on GitHub Pages](https://tuhrig.github.io).
However, I wanted to use my existing domain [tuhrig.de](https://tuhrig.de).
So I decided to deploy the Jekyll build via FTP to my own webspace.
The alternative was to use a [308 Permanent Redirect](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/308) which I don't prefer.

Here's what I did:

1. I created a [GitHub action](https://github.com/features/actions) to run the Jekyll-build. See [here](https://stackoverflow.com/questions/64395360/how-can-i-deploy-jekyll-site-from-github-repo-to-my-ftp).
2. After the Jekyll-build, a simple FTP upload too my own webspace is executed. See [here](https://github.com/marketplace/actions/ftp-deploy).

You can find the current build script right [here](https://github.com/tuhrig/tuhrig.github.io/blob/master/.github/workflows/main.yml).

![](/images/2022/12/deploy.png)

You can find a very good tutorial about migrating from Wordpress to Jekyll right here:

> [https://haralduebele.github.io/2021/02/10/Moving-my-Blog-from-Wordpress-to-Github-Pages](https://haralduebele.github.io/2021/02/10/Moving-my-Blog-from-Wordpress-to-Github-Pages/)

## No more comments

I decided to remove all comments from my blog. 
While I received some useful comments in the past, there were not many.
The amount of spam was always very high and I had to moderate the comments regularly.
Often comments contained questions about old posts which I was unable to answer since everything was outdated for years.
So in the end, the value of the comments was very low to me.

> Instead, I encourage everyone to get in touch with me via [email](mailto:mail@tuhrig.de) or any of my social media profiles.

## Final thoughts

Blogging with Jekyll instead of Wordpress feels much more understandable for me.
No more plugins, pingbacks, WYSIWYG-editors, admin sections and setting pages.
Everything seems to be more under control. 

The actual migration on the other side was a bunch of work. 
My old blog has been growing over the years and it accumulated a lot of "technical debt".
Even before the migration, there have been broken posts, dead links and missing images.
I'm happy that it's done, but it wasn't all fun.

## More

- [Tutorial on how to migrate from Wordpres to Jekyll ](https://haralduebele.github.io/2021/02/10/Moving-my-Blog-from-Wordpress-to-Github-Pages)
- [GiHub FTP upload action](https://github.com/marketplace/actions/ftp-deploy)
- [Run Jekyll-build in an own GitHub action](https://stackoverflow.com/questions/64395360/how-can-i-deploy-jekyll-site-from-github-repo-to-my-ftp) 

**Best regards,** Thomas.
