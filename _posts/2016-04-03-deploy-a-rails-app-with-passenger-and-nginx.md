---
layout: post
title: Deploy a Rails App with Passenger and Nginx
tags: [Passenger, Nginx]
categories: Development
---

<img src="{{ site.url }}/public/images/nginx-passenger-for-rails.jpg"/>

If you are a Ruby on Rails developer, you probably need a web server to host your web apps. Passenger is easy to install, configure, and maintain and it can be used with Nginx or Apache.


Let's start with it.

### Install passenger gem

{% highlight liquid %}
gem install passenger --no-ri --no-rdoc
{% endhighlight %}

### Install nginx passenger module

The correct way of running the `Phusion Passenger installer for nginx`, when using RVM, is to use `rvmsudo` as in:

{% highlight liquid %}
rvmsudo passenger-install-nginx-module
{% endhighlight %}

Installer will guide if any dependecy is missing on your machine.
Like - Install below package

{% highlight liquid %}
sudo apt-get install libcurl4-openssl-dev
{% endhighlight %}

### Location of nGinx installed: (to get passenger root path)

{% highlight liquid %}
passenger-config --root
{% endhighlight %}

### Start/Stop nGinx service

{% highlight liquid %}
sudo /opt/nginx/sbin/nginx

sudo /opt/nginx/sbin/nginx -s stop
{% endhighlight %}

Before start nGinx server check for port number 80 whether it is free or apache2 is already been using,
{% highlight liquid %}
netstat -tulpn | grep :80
{% endhighlight %}

Stop the service of apache2 by,
{% highlight liquid %}
sudo service apache2 stop
{% endhighlight %}

In `/etc/apache2/ports.conf`, change the port as
{% highlight liquid %}
From:
Listen 80

To:
Listen 81
{% endhighlight %}

Then go to `/etc/apache2/sites-enabled/000-default.conf`

And change the first line as
{% highlight liquid %}
<VirtualHost *: 81>
{% endhighlight %}

Now restart
{% highlight liquid %}
sudo service apache2 restart
{% endhighlight %}

Now start nGinx server and confirm it's properly working by browsing it's index page.

`sudo /opt/nginx/sbin/nginx`

Once nginx started correctly, verify it on browser with following url,

http://localhost/

It should show nGinx default page see below,

<img src="{{ site.url }}/public/images/nGinx-default-page.png"/>

Configure to connect Nginx to Your `Rails Project`

Open up the nginx config file,

{% highlight liquid %}
sudo nano /opt/nginx/conf/nginx.conf
{% endhighlight %}

Set the `root` to `public directory` of your new rails project.

Your config should then look something like this:
{% highlight liquid %}
server {
listen 80;
server_name localhost;
passenger_enabled on;
root /home/rahul/workspace/sports/public;
}
{% endhighlight %}

Restart nginx server and check your rails application working with nGinx.

After running your rails application with nginx, if you still seeing `Nginx: Welcome to nginx! page.` Then try with following option to resolve the issue.

In your config file,
{% highlight liquid %}
sudo nano /opt/nginx/conf/nginx.conf
{% endhighlight %}
Comment out following code.
{% highlight liquid %}
    # location / {
     #    root   html;
     #     index  index.html index.htm;
     # }
{% endhighlight %}

Restart nginx service, your rails application is now running with nginx server.