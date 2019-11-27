+++ 
date = 2014-03-24T15:56:12-05:00
title = "A better way to find ServiceNow images"
description = "**Once upon a time**, it was easy to find the image you wanted in the ServiceNow image picker."
slug = "a-better-way-to-find-servicenow-images" 
tags = ["ServiceNow"]
categories = []
externalLink = ""
series = ["Test"]
+++

**Once upon a time**, it was easy to find the image you wanted in the ServiceNow image picker. Perhaps it wasn’t pretty, but the filename was visible next to each image, making it easy to Cmd-F / Ctrl-F and locate the item you wanted. Since the Calgary release, file names are no longer included in the image picker view.

[![image_picker_images.png](https://d23f6h5jpj26xu.cloudfront.net/0kftahfxtiotvg_small.png)](http://img.svbtle.com/0kftahfxtiotvg.png)

The new styling is arguably more visually appealing at first glance, and certainly takes up less space, but the removal of file names has one serious side effect: it’s now almost impossible to find the image you want. As I'm sure you're well aware, 3 rows is just the beginning. I can’t tell you how many times I’ve repeatedly scanned the list, skimming past the item I was looking for before finally laying eyes on the right image.

# A solution
[Greasemonkey (Firefox)](https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/) / [Tampermonkey (Chrome)](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=en) to the rescue. If you’re not familiar with these plugins and what they do, they allow you to write scripts that are executed by the browser when certain pages load. For our purposes, we want a script to execute every time the image picker is loaded that will give us some fancy new options. (For more general information about Greasemonkey and user scripts, go read about them here).

To make everything about the Image Picker better, we need to add a user script to our extension of choice consisting of a few lines of JavaScript:

    // ==UserScript==
    // @name Better ServiceNow Image Picker
    // @namespace https://joshneri.us/
    // @version 0.1
    // @description A better way to find ServiceNow images
    // @match https://*.service-now.com/image_picker.do*
    // @copyright 2014+, Josh Nerius
    // @require http://ajax.googleapis.com/ajax/libs/jquery/2.1.0/jquery.min.js
    // ==/UserScript==
    $(window).load(function(){ 
        $("tr.tool_banner > td")
            .append("<input type='button' value='Show Names'>")
            .click(function() {
                var frame = $("#ibrowse"); 
                frame.contents().find("div > a").each(function() {
                    var name = $("img", this).attr("src").split("/").pop(); 
                    $(this).append(" " + name); 
                }
            ); 
        }); 
    });

This will give us a **Show Names** button when we open the image picker: 
[![show_names_button.png](https://d23f6h5jpj26xu.cloudfront.net/ziu5oeo1rtucbq_small.png)](http://img.svbtle.com/ziu5oeo1rtucbq.png)

**Click the button**, and the magic happens. Cmd/Ctrl-F away!
[![now_with_names.png](https://d23f6h5jpj26xu.cloudfront.net/nu8muaryqyuq_small.png)](http://img.svbtle.com/nu8muaryqyuq.png)

# Installing it
If you don’t have the Greasemonkey (Firefox) or Tampermonkey (Chrome) extension in your browser, install one of them first.

You can then do one of two things:

* [Go to the userscripts.org script I published here](http://userscripts.org/scripts/show/435352) and click “Install”.
* Manually create a new user script in your extension of choice and paste in the code I provided above.

# More stuff
The beauty of this solution is that it does not involve customization of any ServiceNow code, and will be available in whatever instance you log in to.

# Caveats
* If you click Show Names more than once, it will keep appending the name repeatedly. I didn’t feel it was worth spending the time to prevent this.
* jQuery is loaded as part of the user script (see the @require statement). This only occurs on the image picker page however.
* I wrote this in about 20 minutes, and didn’t really do a lot of testing, so use it at your own risk (not that there's much harm this can do).