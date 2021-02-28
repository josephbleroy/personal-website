---
author:
  name: "Joseph"
date: 2017-01-08
linktitle: Implementing HTTPS and HTTP Headers in Nginx
title: Implementing HTTPS and HTTP Headers in Nginx
type:
- post
- posts
weight: 10
series:
- 
aliases:
- 
---

There's been a lot of focus on internet security and encryption over the past several years. You'll find dozens of news reports every day about companies getting breached, individuals having their bank accounts compromised, and security researchers finding vulnerabilities in web services and encryption protocols.

As someone who is passionate about security and technology, I've been driven to keep up with current standards and implement best practices so that I don't become victim to preventable vulnerabilities. With that being said, I've learned a few methods to make it more difficult for attackers to breach my servers and ways to prevent malicious content from being served from my websites, or to [prevent attackers from using MitM attacks](https://medium.com/google-developer-experts/the-importance-of-using-https-all-the-way-9c65c0224e86#.zhjctgif4) (Man in the Middle) when users connect to rogue access points (malicious public wifi hotspots).

So why should we care about HTTPS and HTTP Headers? Well, most people want to ensure the information they send between their computer and the website they're connecting to remains confidential. That means that if someone were to "sniff" their network traffic, they wouldn't be able to understand what's in the payload (raw data being exchanged), which usually contains usernames, passwords, credit card info, and other PII (Personally Identifiable Information) content.

There are several ways to check your existing site to make sure you're using proper HTTP Headers and SSL/TLS configuration settings. A few which come to mind are [Qualys SSL Labs - SSL Server Test](https://www.ssllabs.com/ssltest/) and [securityheaders.io](https://securityheaders.io/). I'm also using a new service called [Hardenize](https://www.hardenize.com), which is a slightly more comprehensive test that checks for modern security settings.

### Definitions

It's important to know what the following components are to better understand what is being worked on and why it's being modified.

* **Nginx:** HTTP Web Server, Reverse Proxy, Load Balancer, etc. Nginx can also do other things, but it's mainly used as a conduit for serving content over ports 80 (HTTP) or 443 (HTTPS).
* **SSL/TLS (HTTPS):** Protocol used to encrypt traffic between two endpoints. It's what gives you the little green lock next to the URI in your browser. Additionally, it's signed by a CA to verify domain and server ownership.
* **Content Security Policy:** Computer security standard enforced by the server to prevent the browser from rendering malicious (unauthorized or dangerous) content. Prevent cross-site scripting (XSS), clickjacking, code injection, along with other vulnerabilities.

### Setting up HTTPS and HTTP Headers in Nginx
For this example, I'll be using Nginx and Ubuntu 16.04.1 LTS. First, we'll want to set up some variables (our domain name and directory) which will be used in the Let's Encrypt certificate generation procedure. Let's Encrypt will insert a small file in a hidden directory at ```/var/www/josephbleroy.com/html``` to verify domain and server ownership.

```shell
export DOMAINS="josephbleroy.com,www.josephbleroy.com"
export DIR=/var/www/josephbleroy.com/html
sudo letsencrypt certonly -a webroot --webroot-path=$DIR -d $DOMAINS
```

You should now be able to use your new certificate / keys to encrypt traffic between the web browser and web server. We'll also want to redirect all HTTP traffic to HTTPS (301 Redirect), implement the certificate and keys we generated to establish the encrypted (TLS 1.2 over port 443) connection, and add HTTP Headers (Content-Security-Policy, X-XSS-Protection, X-Frame-Options). All certificates and keys will be stored in ```/etc/letsencrypt/live/```, for Ubuntu 16.04.1 LTS.

Let's get started by using vim or another editor to create or edit the following file:

```sudo vim /etc/nginx/sites-available/josephbleroy.com```

Implement the following code in your file and replace my domain name with your domain name. You can quickly do that in vim by using ```:%s/jospehbleroy.com/yourdomainname.com/g```.

```nginx
server {
     listen 80;
     listen 443 ssl http2;
     server_name josephbleroy.com www.josephbleroy.com;
     ssl_protocols TLSv1.2;
     ssl_ciphers EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
     ssl_prefer_server_ciphers On;
     ssl_certificate /etc/letsencrypt/live/josephbleroy.com/fullchain.pem;
     ssl_certificate_key /etc/letsencrypt/live/josephbleroy.com/privkey.pem;
     ssl_trusted_certificate /etc/letsencrypt/live/josephbleroy.com/chain.pem;
     ssl_session_cache shared:SSL:128m;
     add_header Strict-Transport-Security "max-age=31557600; includeSubDomains";
     add_header X-Frame-Options "SAMEORIGIN" always;
     add_header X-Content-Type-Options "nosniff" always;
     add_header X-Xss-Protection "1; mode=block" always;
     add_header Content-Security-Policy "script-src 'self' 'unsafe-inline' 'unsafe-eval' *.youtube.com maps.gstatic.com *.googleapis.com *.google-analytics.com assets.zendesk.com connect.facebook.net; frame-src 'self' *.youtube.com assets.zendesk.com *.facebook.com s-static.ak.facebook.com tautt.zendesk.com; object-src 'self'";
     ssl_stapling on;
     ssl_stapling_verify on;
     # Your favorite resolver may be used instead of the Google one below
     resolver 8.8.8.8;
     root /var/www/josephbleroy.com/html;
     index index.html;

     location '/.well-known/acme-challenge' {
        root        /var/www/josephbleroy.com/html;
      }

     location / {
              if ($scheme = http) {
                return 301 https://$server_name$request_uri;
              }
     }
}
```

You can add additional directives and values to **Content-Security-Policy** to match the resource providers you use on your website. For example, if you have a JavaScript file on Cloud Flare's CDN JavaScript server(s), you'll need to add a value for ```cdnjs.cloudflare.com``` so that it looks like this:

```nginx
add_header Content-Security-Policy "script-src 'self' 'unsafe-inline' 'unsafe-eval' cdnjs.cloudflare.com;
```

Now we need to make sure our Nginx configuration file is properly formatted. We'll also need to reload our server to ensure the changes we made go into effect. We can do run a check using ```sudo nginx -t && sudo nginx -s reload```. If there's any issue with our configuration, Nginx won't be reloaded and we'll have an output on where our syntax is incorrect.

We also want to make sure our certificates never expire, so we'll setup a small script and cron job to check the validity of our certificate and renew if possible:

First, we need to create the script. We can do that by ```sudo vim renewCerts.sh``` and then we'll need to insert the following script into the file:

```shell
#!/bin/sh
# This script renews all the Let's Encrypt certificates with a validity < 30 days

if ! letsencrypt renew > /var/log/letsencrypt/renew.log 2>&1 ; then
    echo Automated renewal failed:
    cat /var/log/letsencrypt/renew.log
    exit 1
fi
/usr/sbin/nginx -t && /usr/sbin/nginx -s reload
```

Make sure you ```:wq``` to save the file and then we'll need to setup the cron job using ```sudo crontab -e```, then we'll set the time we want to run the script ```@daily /etc/letsencrypt/renewCerts.sh```, and finally we'll make it executable ```chmod +x /etc/letsencrypt/renewCerts.sh```.

We're now finished our first site's certificate. Our script will run daily and check if the certificate is going to expire in less than 30 days. If it's less than 30 days until expiry, it'll be renewed for another 90 days.

![](https://storage.googleapis.com/josephbleroy-images/images/2017/image2.png)

If there's any type of error, it's probably related to the script not being able to be executed. You'll have to play around with the permissions. You can run the commands in the script individually using the ```--dry-run```, which will simulate the command without actually executing it.

### Adding Additional Certificates and Sites
I currently host several personal websites on one VPS (Virtual Private Server) using Google Cloud Platform. I have one instance of Nginx running and I have several virtual host configurations for each site. I won't go into too much detail because that's a topic within itself, but if you want to serve multiple sites on one IP address (server) you can simply follow the above steps once each host configuration is created and linked.

You can create a new virtual host file by doing the following:

```shell
sudo mkdir /var/www/yourfirstsite.you/html
sudo mkdir /var/www/yoursecondsite.you/html
sudo vim /etc/nginx/sites-available/yourfirstsite.you
sudo vim /etc/nginx/sites-available/yoursecondsite.you
sudo ln -s /etc/nginx/sites-available/yourfirstsite.you /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/yoursecondsite.you /etc/nginx/sites-enabled
```

You'll now be able to serve multiple sites after you replace all the domain and directory values, as well as the value for each certificate, in ```/etc/nginx/sites-available/yourfirstsite.you``` and ```/etc/nginx/sites-available/yoursecondsite.you```. After that, make sure you check the syntax and reload Nginx using ```sudo nginx -t && sudo nginx -s reload```.

### Future Work
There's a few things I want to implement in the near future. I'd like to build Nginx from source so that I can implement an new AEAD (Authenticated Encryption with Associated Data) cipher suite called **ChaCha20-Poly1305**. It'll be the standard and only option once [(TLSv1.3)](https://tools.ietf.org/html/rfc7539) is released. Most mobile devices do not support AES Hardware Acceleration, so this cipher suite will improve performance by roughly ~200%, which is pretty great. 

To do this we'll have to build Nginx with a custom version of OpenSSL. As always, make sure you backup your site before performing any of the following commands:

```shell
cd ~
mkdir nginx
cd nginx
wget http://nginx.org/download/nginx-1.11.8.tar.gz
tar -xzvf nginx-1.11.8.tar.gz
rm nginx-1.11.8.tar.gz
```

This will download the mainline (development) version of Nginx. We'll also need to do the same with OpenSSL, so make sure you remain in the same directory:

```shell
wget https://www.openssl.org/source/openssl-1.1.0c.tar.gz
tar -xzvf openssl-1.1.0c.tar.gz
rm openssl-1.1.0c.tar.gz
```

Now we're going to configure Nginx 1.11.8 to work with OpenSSL 1.1.0c, which supports ChaCha20-Poly1305:

```shell
cd nginx-1.11.8
./configure (existing configure arguments) --with-openssl=/home/joe/nginx/openssl-1.1.0c
```

Now we need to build Nginx so that it includes OpenSSL 1.1.0c (you might need to run them using sudo):

```shell
make
sudo make install
sudo service nginx restart
```

You can read more about this cipher suite on Scott Helme's (a very talented security researcher from the United Kingdom) [website](https://scotthelme.co.uk/doing-the-chacha-with-nginx/).

### Conclusion
I hope this guide was somewhat helpful to help you get started with using Let's Encrypt. I intend to update this post with better terminology and more clear explanations. I'll probably add some more details about some of the individual settings as well, and update them if there are any vulnerabilities with the ciphers and encryptions methods used.

If there are any errors, please send me [contact me](https://www.josephbleroy.com/page/contact) to let me know.