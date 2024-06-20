---
title: "Nginx Reverse Proxy"
date: 2024-06-19T09:44:17Z
aliases: ["/nginx-reverse-proxy"]
tags: ["reverse proxy", "web server", "security"]
author: "Marco Rehmer"
draft: false
description: "Desc Text."
cover:
  image: "../images/cover.png" # image path/url
  alt: "NGINX Logo" # alt text
  relative: true
---

# Header 1

this is some content

## Header 2

### Header 3

Here comes the blog post in english

```nginx
server {

  server_name example.com www.example.com;

  location / {
    proxy_pass http://localhost:8080;
  }
}
```
