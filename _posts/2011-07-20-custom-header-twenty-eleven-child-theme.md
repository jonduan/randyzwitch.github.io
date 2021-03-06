---
title: 'Twenty Eleven Child Theme: Custom Header'
date: 2011-07-20T13:16:53+00:00
author: Randy Zwitch
layout: post
permalink: /custom-header-twenty-eleven-child-theme/
category: Design
description: When developing a child theme for WordPress, creating a custom header file will give great flexibility in customizing your theme's design.
tags:
  - Child Theme
  - CSS
  - PHP
---
In a previous [post](http://randyzwitch.com/twenty-eleven-child-theme-creating-css-file/) about creating a child theme for Twenty-Eleven, we created a custom CSS file using the <a title="Import CSS rule" href="http://www.w3.org/TR/CSS2/cascade.html#at-import" target="_blank">@import CSS rule</a>.  Technically, the new CSS file is all that is needed to create a child theme in WordPress, but _just_ changing the colors and font styles isn't that much fun.  If it's worth making your own WordPress child theme, it's worth changing _everything..._starting with creating a custom header file.

## Creating a custom header file for our child theme

Creating a custom header file in WordPress is even simpler than creating a custom style sheet, because this time we'll just copy the file directly from the base Twenty-Eleven theme directly into our child theme.

  1. Go to your `/wp-content/themes/twentyeleven` directory and locate the file `header.php`
  2. Paste the `header.php` file to your `/wp-content/themes/twentyeleven-child` directory

That's it!  Not very exciting...so let's do some additional code modification while we're in the new file.

## Changing header image and size

Of all of the great things about the WordPress Twenty-Eleven theme, the one thing I can't understand is why WordPress decided to make the header image so damn big!  There is the option to change the image you display to any number of provided images, or upload your own as part of the Appearance > Header menu.  But you can't change the size...if your upload is too short, the theme will stretch it for you, distorting the image.  If your upload is too large, you'll have to crop it. Luckily, with a minor modification we can change that.

First, go your newly created header.php file and find the following code:

What we're going to do is use the <a title="Comment out code" href="http://www.w3schools.com/tags/tag_comment.asp" target="_blank">commenting tag</a> in HTML to "comment out" the portions of our header code that we no longer want to be active (i.e. the re-sizing feature in the Twenty-Eleven theme).  We can do this by adding the following tag (a left arrow, and exclamation point, and two dashes):

You're not required to put in the "Comment out WordPress header checking" text as I did, but it is good practice to notate why changes have been made to your code.  A little documentation now goes a long way a year from now, when you can't remember why you changed something! Now that we've got the opening tag for commenting, we need to find the end of the code we want to comment out, and put the closing tag.  Once we do this, all of the code between the commenting tags will not get executed by our browser.

The closing tag above is the two dashes, then the right arrow.  Again, you don't have to write "End of RZ commenting out", but it's good for documentation.  Save your header.php file to make the completed changes.

## Hey, now I don't have ANY banner!

If you refreshed your blog at this point, you'll realize that not only did we get rid of the massive size of the banner, we got rid of the banner overall!  That's not the goal of this tutorial...so let's add the banner back, except now we'll control the sizing.

First thing you need to do is make sure you have the banner you want uploaded to your server, and that it is the size you want.  This blog is 1000 pixels wide at the body (white space), and I decided to make my banner 225 pixels tall, because that's where the picture looked good.

Once you have your banner location, we need to add the HTML link to our image, using the image tag.  For the "alt" section, you should put a description of what your banner represents, as this will be displayed if the users browser can't download the image.  In the "src" section, add the location of your banner file.

Once you hit save, that's it!  You now have control over your banner image.  Yes, modifying the code in this manner disables the Appearance > Header functionality in the theme, but that's a small price to pay to get flexibility.  If you want to change your banner in the future to show a different image, you'll need to do so in the header.php file.  But since you've documented this change in your code, you'll know where to look and what to change when that time comes...

![randyzwitchdotcom-before](/wp-content/uploads/2011/07/randyzwitchdot-before.png)

<p class="wp-caption-text">
Before - 1000px by 288px image
</p>

![randyzwitchdotcom-after](/wp-content/uploads/2011/07/randyzwitchdotcom-after-300x169.png)

<p class="wp-caption-text">
After: 1000px by 225 px
</p>
