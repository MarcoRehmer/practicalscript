---
title: "Nginx Reverse Proxy"
date: 2024-06-19T09:44:17Z
# aliases: ["/first"]
tags: ["post"]
author: "Marco Rehmer"
draft: false
#description: "Desc Text."
#canonicalURL: "https://canonical.url/to/page"
cover:
  image: "/cover.png" # image path/url
  alt: "NGINX Logo" # alt text
  #relative: false # when using page bundles set this to true
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
