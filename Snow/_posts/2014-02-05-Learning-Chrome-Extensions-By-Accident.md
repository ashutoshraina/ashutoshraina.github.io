---
layout: post
title: Learning Chrome Extensions by accident
category: accident, chrome
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

So, twitter has revamped their look to be flatter and what not...It looks like apple now. This really means I was annoyed and wanted to get rid of things in the interface I did not like. Ok so how do we do that ? Write a chrome extension and that will be the permanent end of that!!
<!--excerpt-->

Get the sample for making a chrome extension from [here](http://developer.chrome.com/extensions/getstarted.html "chrome extension"). There is more to it than what I am going to write about hopefully you will go on and explore it further. Open up the manifest.js and fix the permissions and the icon. I wanted to tweak twitter so asked for that you may want something else. There are other types of permission as well which may include working with an active tab etc..Pick and choose the relevant one. I did not need any styling in the body of the page hence I got rid of that. I ended up needing no body at all so my manifest looked like this eventually.

    {
      "manifest_version": 2,
      "name": "Twitter Fixer",
      "description": "Fixes twitter styling",
      "version": "1.0",
        "icons": {
        "16": "icon.png"
        },
    "permissions": [
      "https://twitter.com/*"
    ],
    "content_scripts": [
        {
          "matches": [
            "https://twitter.com/"
          ],
          "css": [
            "fixtwitter.css"
          ]
        }
      ]
    }

The html page was bare bones.

    <!doctype html>
    <html>
      <head>
        <title>Twitter Fixer</title>
        <script src="fixtwitter.js"></script>
      </head>
      <body>
      </body>
    </html>


So what is fixtwitter.css. It is the css which I wanted to apply on top of twitter. I tweaked it in the developer tools and settled on what I thought worked for me. It ended up looking like this. I tried fiddling with the CSS through js but I now know better.

    #timeline {
        width:115%;
    }
    #discover-stories{
        width:115%;
    }
    .content-main{
        width:115%;
    }
    .avatar{
        border-radius:'25px';
        width:65px;
        height:65px;
    }
    .dashboard{
        display:none;
    }
    .stream-item .content{
    margin:inherit;
    }
    .module{
        display:none;
    }

Now, armed with all the tools(you might want to change your icon). I opened up the extensions in settings and loaded the unpacked extension. The result was this.

![Fixed Twitter](/stylesheets/images/posts/twitter-fixed.png)

Being able to bend and break things is quite cool. Play as much as you can.
I thought about putting it up on chrome extension gallery then saw the $5 charge and dropped the idea.

P.S. Don't judge me by my CSS skills.