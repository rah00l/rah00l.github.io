---
layout: post
title: Self Signed SSL certificate for Nginx in Ubuntu
tags: [Passenger, Nginx, SSL]
categories: Deployment
---

### Basics about SSL

SSL (Secure Sockets Layer) and its successor, TLS (Transport Layer Security), are protocols for establishing authenticated and encrypted links between networked computers. Although the SSL protocol was deprecated with the release of TLS 1.0 in 1999, it is still common to refer to these related technologies as “SSL” or “SSL/TLS.”

<img src="{{ site.url }}/public/images/self-signed-SSL-certificate for-Nginx-in-Ubuntu.jpg"/>


### Keys, Certificates, and Handshakes

SSL/TLS works by binding the identities of entities such as websites and companies to cryptographic key pairs via digital documents known as X.509 certificates. Each key pair consists of a private key and a public key. The private key is kept secure, and the public key can be widely distributed via a certificate.

The special mathematical relationship between the private and public keys in a pair mean that it is possible to use the public key to encrypt a message that can only be decrypted with the private key. Furthermore, the holder of the private key can use it to sign other digital documents (such as web pages), and anyone with the public key can verify this signature.

### How HTTPS works?

 For HTTPS connection, public key and signed certificates are required for the server. 
 When using an https connection, the server responds to the initial connection by offering a list of encryption methods it supports. In response, the client selects a connection method, and the client and server exchange certificates to authenticate their identities. After this is done, both parties exchange the encrypted information after ensuring that both are using the same key, and the connection is closed. In order to host https connections, a server must have a public key certificate, which embeds key information with a verification of the key owner's identity. Most certificates are verified by a third party so that clients are assured that the key is secure.
In other words, we can say, HTTPS works similar to HTTP but SSL adds some spice in it.

Let's start with it.

We can create a self-signed key and certificate pair with OpenSSL in a single command:

{% highlight liquid %}
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
{% endhighlight %}

The entirety of the prompts will look something like this:

{% highlight liquid %}
deploy123@ip-172-11-22-555:~/example$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
Generating a RSA private key
..........+++++
................................................................................................................................+++++
writing new private key to '/etc/ssl/private/nginx-selfsigned.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:AU
State or Province Name (full name) [Some-State]:Some-State
Locality Name (eg, city) []:City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Internet Widgits Pty Ltd                                                    
Organizational Unit Name (eg, section) []:Unit Name  
Common Name (e.g. server FQDN or YOUR name) []:server FQDN                 
Email Address []:example@email.com
{% endhighlight %}


### Configuring Nginx to Use SSL

Your new ssl config changes look something like this:
{% highlight liquid %}
server {
  listen 443 ssl;
  ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
  ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
  server_name  www.moreaboutsports.com;
  passenger_enabled on;
  root /home/rahul/workspace/sports/public;
}
{% endhighlight %}

## Initial configurations were 

{% highlight liquid %}
server {
	listen 80;
	server_name localhost;
	passenger_enabled on;
	root /home/rahul/workspace/sports/public;
}
{% endhighlight %}

### Start/Stop nGinx service

{% highlight liquid %}
sudo /opt/nginx/sbin/nginx

sudo /opt/nginx/sbin/nginx -s stop
{% endhighlight %}

Restart nginx service, your rails application is now running with SSL eabled nginx server.	

## Difference between HTTP and HTTPS: HTTPS = HTTP + SSL

1. URL begins with “http://" in case of HTTP while the URL begins with “https://” in case of HTTPS.
2. HTTP is unsecured while HTTPS is secured.
3. HTTP uses port 80 for communication while HTTPS uses port 443 for communication.
4. HTTP operates at Application Layer while HTTPS operates at Transport Layer.
5. No encryption is there in HTTP while HTTPS uses encryption.
6. No certificates required in HTTP while certificates required in HTTPS.
