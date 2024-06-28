---
title: "Setup Nginx Reverse Proxy + SSL certificate"
date: 2024-06-19T09:44:17Z
aliases: ["/nginx-reverse-proxy"]
tags: ["reverse proxy", "web server", "security"]
author: "Marco Rehmer"
draft: false
summary: "Use of the NGINX web server as a reverse proxy and integrate of Let's Encrypt for SSL certificates"
cover:
  image: "images/nginx-logo.png"
  alt: "NGINX Logo"
---

The following steps describe how [NGINX](/glossary/nginx) can be operated as a [reverse proxy](/glossary/reverse-proxy) under Linux (e.g. Ubuntu). In addition, the activation of SSL/TLS is described and a certificate from Lets Encrypt is requested.

## NGINX installation

Under Linux, [NGINX](/glossary/nginx) can simply be installed with the following command:

```sh
sudo apt install nginx
```

[NGINX](/glossary/nginx) is available in every common package manager.

## Create config for virtual hosts

A virtual host must now be created, which forwards the request from the outside to the internal service. If, for example, `www.example.com` is requested, the configuration must forward this to the internal service such as `http://localhost:8080`.

Here is an example configuration which is created in the `etc/nginx/sites-enabled` directory:

```nginx
server {

  server_name example.com www.example.com;

  location / {
    proxy_pass http://localhost:8080;
  }
}
```

## Request a certificate from Lets Encrypt

To request a certificate from Lets Encrypt, make sure that ports 80 and 443 in the firewall are open for communication.

If not already done, the `certbot` and the appropriate [NGINX](/glossary/nginx) plugin must be installed:

```sh
sudo apt install certbot python3-certbot-nginx`
```

Now the certificate can be requested with the following command:

```sh
certbot --nginx -d example.com -d www.exmaple.com
```

[NGINX](/glossary/nginx)
`-d` specifies the domain to be requested. If the certificate has been successfully installed, the config file is automatically adapted and [NGINX](/glossary/nginx) is prompted to read in the config again.
