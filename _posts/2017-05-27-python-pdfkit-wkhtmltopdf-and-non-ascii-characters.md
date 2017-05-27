---
layout: post
title: Python's pdfkit, wkhtmltopdf and non-ascii characters
excerpt: "Using non-ascii characters with wkhtmltopdf is pretty easy. If you know how :)"
tags: [python, pdfkit, wkhtmltopdf, encoding]
modified: 2017-05-27
comments: true
image:
    feature: old-days-of-technology.jpg
---

I've been struggling to generate PDF files from simple HTML code that used non-ascii characters.
The tools that I was using were wkhtmltopdf (version 0.12.2 which doesn't need an X server) and [python-pdfkit](https://pypi.python.org/pypi/pdfkit).
Unfortunately, I spent quite some time working on the wrong solutions.
Namely, these are the things that I took a look at:

- Python. An usual suspect, but this time it was not a case of ["Unicode madness"](http://lucumr.pocoo.org/2014/5/12/everything-about-unicode/).
- [An open issue](https://github.com/JazzCore/python-pdfkit/issues/42) regarding a problem with certain characters in python-pdfkit.
- [Using different fonts](https://stackoverflow.com/questions/44214688/how-to-generate-a-pdf-with-non-ascii-characters-using-from-string-from-python-pd). Apparently there's another project also called pdfkit (for node) that has nothing to do with wkhtmltopdf and pdfkit for Python/Ruby.


After testing different websites (some working and some failing) I realised that I just needed to include a meta tag specifying that UTF-8 was the correct charset.
It's not surprising that some websites (and my own HTML code) did not include this attribute because this is what w3schools says about it:

<blockquote>
  If an HTML5 web page uses a different character set than UTF-8, it should be specified in the <meta> tag.
</blockquote>

Sometimes easy solutions are difficult to find!
Below you can find a working example.

{% highlight python %}
import pdfkit

html_content = """
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
</head>
<body>
    <p>&euro;</p>
    <p>áéíóúñö</p>
<body>
</html>
"""

pdfkit.from_string(html_content, 'out.pdf')
{% endhighlight %}
