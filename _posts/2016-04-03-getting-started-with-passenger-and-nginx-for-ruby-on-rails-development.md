---
title: Getting Started with Passenger and Nginx for Ruby on Rails Development
tags: [Ubuntu, Rails, Nginx]
style: border
color: danger
description: We'll explore how to set up and configure Passenger with Nginx, providing you with a straightforward and efficient solution for deploying your web apps.
---


### Introduction:

As a Ruby on Rails developer, having a reliable web server to host your applications is essential. In this blog post, we'll explore how to set up and configure Passenger with Nginx, providing you with a straightforward and efficient solution for deploying your web apps.
<br/><br/>

<img src="{{ site.url }}/public/images/nginx-passenger-for-rails-1.png"/>


#### Installing Passenger and Nginx:

To begin, follow these simple steps:

##### Step 1: Install the Passenger gem:

```console
gem install passenger --no-ri --no-rdoc
```

##### Step 2: Install the Nginx Passenger module:

When using RVM, it's important to run the Phusion Passenger installer with rvmsudo:
```console
rvmsudo passenger-install-nginx-module
```

The installer will guide you and prompt you to install any missing dependencies. 
For example, you might need to run:
```console
sudo apt-get install libcurl4-openssl-dev
```

Starting and Stopping Nginx:
To start the Nginx service, use the following command:
```console
sudo /opt/nginx/sbin/nginx
```

To stop the Nginx service, use the following command:
```console
sudo /opt/nginx/sbin/nginx -s stop
```

Before starting Nginx, ensure that port 80 is available and not being used by Apache. 
You can check this by running:

```console
netstat -tulpn | grep :80
```

If Apache is running on port 80, stop the service using:
```console
sudo service apache2 stop
```

Update the port configuration in /etc/apache2/ports.conf:
Change:
```console
Listen 80
```

To:
```console
Listen 81
```

Then, modify the first line in /etc/apache2/sites-enabled/000-default.conf to:
<VirtualHost *: 81>

Restart Apache for the changes to take effect:
```console
sudo service apache2 restart
```

Configuring Nginx to Connect with Your Rails Project:
Open the Nginx config file:
```console
sudo nano /opt/nginx/conf/nginx.conf
```

Set the root to the public directory of your Rails project. Your configuration should resemble the following:
```console
server {
  listen 80;
  server_name localhost;
  passenger_enabled on;
  root /home/rahul/workspace/sports/public;
}
```

Restart the Nginx server and verify that your Rails application is working with Nginx.

<img src="{{ site.url }}/public/images/nGinx-default-page.png"/>

##### Troubleshooting:
If you still see the "Welcome to nginx!" page instead of your Rails application, try the following steps:

Open the config file again:
```console
sudo nano /opt/nginx/conf/nginx.conf
```

Comment out the following code block:
```console
# location / {
#    root   html;
#    index  index.html index.htm;
# }
```

Restart the Nginx service. Your Rails application should now be successfully running with the Nginx server.

### Conclusion:

Setting up Passenger with Nginx for your Ruby on Rails development provides a robust and efficient solution for hosting your web applications. By following the steps outlined in this blog post, you can quickly configure Nginx and connect it to your Rails projects, ensuring smooth deployment and seamless performance. Enjoy the benefits of this powerful web server combination for your Ruby on Rails development endeavors.
