---
layout: post
title: Dude, where are my headers? flask-jwt, apache and WSGI
excerpt: "How to be able to serve an app using flask-jwt with apache and WSGI."
tags: [web, flask, jwt, apache, wsgi, troubleshooting]
modified: 2017-04-03
comments: true
image:
    feature: old-days-of-technology.jpg
---

I had a beautiful time today trying to figure out why JWT authentication was not working in my server.
Surprisingly I found little information about it in the Internet.

I was using Flask locally and the application worked wonderfully. However, when I tried to test the application in a server, I got the following error:

{% highlight javascript %}
{
  "description": "Request does not contain an access token",
  "error": "Authorization Required",
  "status_code": 401
}
{% endhighlight %}


The error message in the server was clear: the access token was not present in the request.

{% highlight plaintext %}
ERROR:flask_jwt:Authorization Required. Request does not contain an access token
{% endhighlight %}


How was it possible? My script to test the API endpoints was sending the Authorization header correctly:

{% highlight python %}

def get_access_token(host):
    url = host + '/auth'
    data = '{"username":"usernameexample","password":"passwordexample"}'
    headers = {'Content-type': 'application/json'}
    resp = requests.post(url=url, data=data, headers=headers)
    resp_json = resp.json()
    access_token = resp_json['access_token']
    return access_token

def test_protected_access(host, access_token):
    headers = {
        'Authorization': 'JWT {}'.format(access_token),
    }
    resp = requests.get(host + '/protected', headers=headers)

access_token = get_access_token(host)
test_protected_access(host, access_token)
{% endhighlight %}

And the headers were being received in the local server:

{% highlight conf %}
Authorization: JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6MSwiaWF0IjoxNDkxMjE1MDk0LCJleHAiOjE0OTEyMTUzOTQsIm5iZiI6MTQ5MTIxNTA5NH0.F4pbN71cJd1JPj2oO6O-YoatcF6_1My7yRsUhokvwCE
Host: localhost:5000
Content-Length:
User-Agent: python-requests/2.13.0
Accept: */*
Connection: keep-alive
Accept-Encoding: gzip, deflate
Content-Type:
{% endhighlight %}


<br/>
<center>
<a href="/images/dude_where_are_my_headers.jpg">
  <img class="aligncenter size-medium wp-image-814" src="/images/dude_where_are_my_headers.jpg" alt="dude where are my headers (dude where's my car meme)"/>
</a>
</center>
<br/>

Unluckily, this time finding the problem was not as easy as googling the error that I received either on the server or on the client side.
After reading some documentation I found out that [the authorisation header is not passed through to WSGI applications by default](http://code.google.com/p/modwsgi/wiki/AccessControlMechanisms) for security reasons.
But the fix is very simple, you just have to use the WSGIPassAuthorization directive:

{% highlight apache %}
WSGIPassAuthorization on
{% endhighlight %}

This is an example of apache configuration file using this directive:

{% highlight apache %}
<VirtualHost *:80>
		ServerName example.com
		ServerAdmin hithere@example.com
		WSGIScriptAlias / /var/www/example/example.wsgi
		WSGIPassAuthorization on
		<Directory /var/www/example/example/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
{% endhighlight %}


I hope this helps anyone googling these errors or at least helps me not forget about this next time :)
