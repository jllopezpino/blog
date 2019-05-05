---
layout: post
title: 3 simplest and most effective tricks to speed up your website
excerpt: "In less than an hour I was able to take the Google PageSpeed score of one of my websites from 41 to 80 by tracking the most severe (and basic) problems."
tags: [web, page speed]
modified: 2017-01-13
comments: true
image:
    feature: old-days-of-technology.jpg
---

I was prototyping a new web product a few days ago and I wanted to enhace customers' mobile experience.
The results from the Google PageSpeed test were pretty alarming: my prototype was very slow.
I was determined to improve the website speed, but I couldn't afford to spend too much time on it since it's in the prototyping phase.


In less than an hour I was able to take the PageSpeed score from 41 to 80 by tracking the most severe (and basic) problems.
All serious websites should have these three problems covered in a way. If you are unsure if your website has it covered, just use PageSpeed.

<br/>
<a href="/images/pagespeed.png"><img class="aligncenter size-medium wp-image-814" src="/images/pagespeed.png" alt="Google PageSpeed test results" width="600"/></a>


# Lossy compression of images

We all know that one of the main reasons why many websites load slow are the images.
I've got very good results by just using two simple command lines tools: pngquant and jpegoptim.

{% highlight bash %}

pngquant --quality 20 *.png

{% endhighlight %}


{% highlight bash %}

$ jpegoptim -m20 *.jpg

{% endhighlight %}


# Expirty date in HTTP headers

This is what Google says regarding having a expiry date in the HTTP headers:

> Setting an expiry date or a maximum age in the HTTP headers for static resources instructs the browser to load previously downloaded resources from local disk rather than over the network.

Fixing this problem can take you literally seconds if you are using apache webserver. First, you have to activate mod_expires:

{% highlight bash %}

$ sudo a2enmod expires

{% endhighlight %}

Second and last, add a rule to make all static files expire after one month.
In my case it was very easy since I have a different directory configured to server them:

{% highlight xml %}

<Directory /var/www/FlaskApp/FlaskApp/templates/static/>
  Order allow,deny
  Allow from all
  <IfModule mod_expires.c>
      ExpiresActive on
      ExpiresDefault	"access plus 1 month"
  </IfModule>
</Directory>

{% endhighlight %}

# Defer loading

Reusing examples from StackOverflow to [defer load JavaScript](http://stackoverflow.com/a/18405468) and another one to [defer load CSS](http://stackoverflow.com/a/22255132), I ended up with the following code:

{% highlight javascript %}

var dfLoadStatus = 0;
var dfLoadFiles = [
      ["static/vendor/jquery/jquery.min.js"],
      ["https://cdnjs.cloudflare.com/ajax/libs/jquery-easing/1.3/jquery.easing.min.js"],
      ["static/vendor/bootstrap/js/bootstrap.min.js"],
      ["static/vendor/scrollreveal/scrollreveal.min.js",
       "static/vendor/magnific-popup/jquery.magnific-popup.min.js"],
      ["static/js/creative.min.js"]
     ];

function loadStyleSheet(src){
   if (document.createStyleSheet) document.createStyleSheet(src);
   else {
       var stylesheet = document.createElement('link');
       stylesheet.href = src;
       stylesheet.rel = 'stylesheet';
       stylesheet.type = 'text/css';
       document.getElementsByTagName('head')[0].appendChild(stylesheet);
   }
}


function deferLoading(){
    downloadJSAtOnload();

    loadStyleSheet("static/vendor/bootstrap/css/bootstrap.min.css");
    loadStyleSheet("static/vendor/font-awesome/css/font-awesome.min.css");
    loadStyleSheet("https://fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,700italic,800italic,400,300,600,700,800");
    loadStyleSheet("https://fonts.googleapis.com/css?family=Merriweather:400,300,300italic,400italic,700,700italic,900,900italic");
    loadStyleSheet("static/vendor/magnific-popup/magnific-popup.css");
    loadStyleSheet("static/css/creative.min.css");

}

function downloadJSAtOnload() {
    if (!dfLoadFiles.length) return;

    var dfGroup = dfLoadFiles.shift();
    dfLoadStatus = 0;

    for(var i = 0; i<dfGroup.length; i++) {
        dfLoadStatus++;
        var element = document.createElement('script');
        element.src = dfGroup[i];
        element.onload = element.onreadystatechange = function() {
        if ( ! this.readyState ||
               this.readyState == 'complete') {
            dfLoadStatus--;
            if (dfLoadStatus==0) downloadJSAtOnload();
        }
    };
    document.body.appendChild(element);
  }

}

if (window.addEventListener)
    window.addEventListener("load", deferLoading, false);
else if (window.attachEvent)
    window.attachEvent("onload", deferLoading);
else window.onload = deferLoading;

{% endhighlight %}

Remember to only do refer load with those resources that are not essential and to load the resources in the right order!

## Update

Update (2019-05-04): More than two years later and Google Pagespeed still give this website a speed score of 97 / 100 (98 / 100 for desktop).
<br>
<a href="/images/2019-05-04_pagespeed_insight_tricks.png">
	<img class="aligncenter" src="/images/2019-05-04_pagespeed_insight_tricks.png" alt="Google PageSpeed tricks results" width="600"/>
</a>
