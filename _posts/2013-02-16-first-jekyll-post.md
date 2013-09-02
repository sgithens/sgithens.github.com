---
layout: post
title: "First Jekyll Post"
description: ""
category: 
tags: []
---
{% include JB/setup %}

After a few years of not writing blogs, I'm putting up a new technical
blog, using Jekyll instead of Wordpress or other things.  For a while
I always thought static websites would never do for me because of the
file extensions, and then I stumbled upon the clever way all these
static publishing engines do it.

Essentially, for anything you want to have a nice clean file
extensionless URL for, you make a directory, and then place an
index.html file inside there.  Web browsers are trained to
automatically load index files for directories, so your directory name
becomes the clean Url. ie.

    .
    |-- projects
        |-- GPII
            |-- index.html
            
becomes the clean url, http://yoursite/projects/GPII . Cool stuff!
And having static pages is great because you can use emacs or
something other editor to maintain your site, and git to naturally
track the changes.
