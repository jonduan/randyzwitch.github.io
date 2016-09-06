---
title: 'Removing "Powered by WordPress" in Twenty Eleven'
date: 2011-08-20T10:14:38+00:00
author: Randy Zwitch
layout: post
guid: http://randyzwitch.com/?p=423
permalink: /removing-powered-by-wordpress-twenty-eleven/
tweetbackscheck:
  - 1472942926
  - 1472942926
shorturls:
  - 'a:3:{s:9:"permalink";s:67:"http://randyzwitch.com/removing-powered-by-wordpress-twenty-eleven/";s:7:"tinyurl";s:26:"http://tinyurl.com/6uql7fp";s:4:"isgd";s:19:"http://is.gd/Nj7ihP";}'
twittercomments:
  - 'a:0:{}'
tweetcount:
  - 0
category:
  - WordPress Tutorials
tags:
  - Child Theme
  - Footer
  - PHP
---
So far in the child theme tutorials for the WordPress Twenty Eleven theme, we've created a <a title="Twenty Eleven Child Theme: Creating CSS file" href="http://randyzwitch.com/2011/07/twenty-eleven-child-theme-creating-css-file/" target="_blank">custom CSS</a> file and <a title="Twenty Eleven Child Theme:  Custom Header" href="http://randyzwitch.com/2011/07/custom-header-twenty-eleven-child-theme/" target="_blank">custom header</a>.  Now, let's add a custom footer and remove "Powered by WordPress".



The first step to having a custom footer for your Twenty Eleven child theme is to copy the _footer.php _file from the parent Twenty Eleven theme into your directory.  You will find this file on your server here:

<p style="padding-left: 30px;">
  /wp-content/themes/twentyeleven
</p>

Once you have copied this file over to your Twenty Eleven child theme folder (for me the location on my server is /wp-content/themes/twentyeleven-child), you are done!  You have a custom footer file...but on your blog, everything will look the same.  That's because while you technically have created a new footer for your blog, no code has actually changed. We'll fix that in the next section.

## Powered by WordPress!  Woo-hoo!

I can't fault WordPress for wanting to drive traffic to their website, especially given they provide the Twenty Eleven theme for free.  But that doesn't mean that "Powered by WordPress" in the footer doesn't look goofy.  Luckily, it's not too difficult to change.

Open up the <del><em>header.php</em></del> _footer.php_ file from your child theme using your favorite text editor.  In this file, you'll find the following code:

All we need to do to remove "Powered by WordPress" is to comment out this code using HTML comment tag, like the following:

Once you hit save, you're all set.  No more "Powered by WordPress", no more text at all. Just a blank footer.



## What if I want to put my own links in the footer?

Of course, just because you don't want "Powered by WordPress" in the footer doesn't mean you don't want any links in the footer.  If you want to add your own text/links, re-open your _footer.php_ file and place your text in the same location as the code we just commented out.

For example, if I wanted to put "© Randy Zwitch - 2011" in the footer, I would place this text between the opening and closing div tags for "site generator":



It's as easy as that to have a custom footer message!  You can get even more tricky by widgets and ads and tag clouds...but there's something to be said for a clean, sleek design.  So I'll stop there 🙂
