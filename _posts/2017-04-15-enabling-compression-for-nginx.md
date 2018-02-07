---
layout: post
title: Enabling Compression for Nginx
tags: [Passenger, Nginx, Compression]
categories: Development
---


The biggest, easiest performance improvment trick to speed up your site is to implement `gzipping` HTML/JSON/JS/CSS/font file responses.

Test a webpage to see if compression is enabled.
https://varvy.com/tools/gzip/

If you are using Nginx, you can quickly and easily speed up your site by enabling gzip compression for your application. Gzip compression compresses files and assets before they are sent to the client, resulting in a nice little speed boost.

{% highlight liquid %}
server {
    listen 80;
    server_name mysite.com www.mysite.com;
    root /opt/mysite.com/current/public;
    passenger_enabled on;
    gzip on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types text/plain text/html text/css application/json application/javascript application/x-javascript text/javascript text/xml application/xml application/rss+xml application/atom+xml application/rdf+xml;}
{% endhighlight %}

Most important lines are `gzip` on and `gzip_types`

`gzip on` turns on gzip compression. Later on, you can add `gzip off` under a `server{..}` block or `location{..}` to turn off gzip compression for one or more sites/regions.

gzip_typesis list of MIME-types for which you want to turn on compression. `text/html` is implied and cannot be turned off (unless you set `gzip off`). `text/css` and `application/x-`
