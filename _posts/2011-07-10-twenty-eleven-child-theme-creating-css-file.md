---
title: 'Twenty Eleven Child Theme: Creating CSS File'
date: 2011-07-10T12:37:42+00:00
author: Randy Zwitch
layout: post
guid: http://randyzwitch.com/?p=65
permalink: /twenty-eleven-child-theme-creating-css-file/
tweetbackscheck:
  - 1472779383
shorturls:
  - 'a:3:{s:9:"permalink";s:67:"http://randyzwitch.com/twenty-eleven-child-theme-creating-css-file/";s:7:"tinyurl";s:26:"http://tinyurl.com/7t2kwfp";s:4:"isgd";s:19:"http://is.gd/Q9RoWt";}'
twittercomments:
  - 'a:0:{}'
tweetcount:
  - 0
category:
  - WordPress Tutorials
tags:
  - Child Theme
  - CSS
---
With the Twenty Eleven child theme directory <a title="Picking a WordPress Theme:  Fancy or Basic?" href="http://randyzwitch.com/2011/07/wordpress-theme-twentyeleven-athualpa/" target="_blank">created</a> as "twentyeleven-child" in the '/wp-content/themes' directory, we're now ready to create the first file as part of the child theme: the CSS file.

While we _could_ just copy the existing CSS file from the Twenty Eleven theme (_style.css_ in the /wp-content/themes/twentyeleven directory) and modify that file directly, if we do that then we will lose track of the changes that were made relative to the original Twenty Eleven styling.  So what we're going to do is create a new CSS file to hold our changes, with a reference back to the original CSS file.



## Step 1:  Creating a blank CSS file

To start the process of creating the blank CSS file, open up your favorite plain text editor such as TextPad if you are using a PC or TextEdit if you are using a Mac.  It's important to use a plain text editor and not a word processor such as Microsoft Word, as Word can add strange characters into your file.

So with a new, blank text document open, save this file with the name 'style.css'.  Your file MUST be named style.css in order to work correctly.

## Step 2:  Creating template/theme header

In the newly created style.css file, add the following code (including the /\*  and \*/ characters):

<pre>/* Theme Name: randyzwitch.com
Description: Child theme for the twentyeleven theme
Author: Randy Zwitch
Template: twentyeleven */</pre>

For your theme, you can choose to put whatever you want in the "Theme Name", "Description" and "Author" files.  The text you list here is what will be visible in the Appearance > Themes menu in the WordPress admin panel.

If you are creating your child theme based on Twenty Eleven, then you are done, as no additional changes need to be made.  Otherwise, place the **directory** name (with no leading or trailing slashes) of the theme are using on the "Template" line.





**Step 3:  Include "CSS import" of Twenty Eleven CSS file

**

The final step in creating the CSS file for our new child theme is to import the CSS properties from the original Twenty Eleven theme.  Note that above, I discussed that we _could_ just copy the entire CSS file into our new file, but then we wouldn't be able to see our changes as a stand-alone.  What we can do instead is add the following code to our new file, directly under the header we created in Step 2:

<pre>@import url("../twentyeleven/style.css");</pre>

It's important to note that there can be no CSS code above this "import" line (other than the header file info).

And that's it!  Save your newly created CSS file, and be sure that it is located in the directory '/wp-content/themes/twentyeleven-child'

## Final comments

If you've done this correctly, the contents of your new CSS file should have the following format (with your information, obviously):



Technically, this is all you need to have a fully-functional "child theme" in WordPress.  To make sure that everything is working correctly, go to the WordPress admin panel and select your new theme under Appearance > Themes and hit 'Activate' your new theme!

&nbsp;

_For more information about creating CSS files for child themes in WordPress, go to the <a title="Wordpress Codex: Child Theme" href="http://codex.wordpress.org/Child_Themes#The_required_style.css_file" target="_blank">CSS section</a> of the WordPress Codex on this topic._