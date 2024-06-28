---
title: "NGINX Web Server"
date: 2024-06-19T09:44:17Z
draft: true
---

NGINX is an open source web server, reverse proxy, load balancer, and HTTP cache in one. It is known for its high performance, stability, easy configuration, and low resource consumption. As a result, it is a popular tool and is used in many enterprise environments.

## Docker Image

NGINX is available as a Docker image on Docker Hub and can thus be used in many virtualized environments.

```bash
docker pull nginx
```

For use with your own Docker image, a suitable configuration file must be copied into the container:

```docker
FROM nginx
COPY nginx.conf
```

### Docker Compose

Here is an example implementation of NGINX in Docker Compose:

```yml
web:
  image: nginx
  volumes:
    - ./content:/var/www/html
  ports:
    - "8080:80"
  environment:
    - NGINX_HOST=foobar.com
    - NGINX_PORT=80
```

## Backlinks

- [Setup Nginx Reverse Proxy + SSL certificate](/posts/nginx-reverse-proxy)

## References

- [Official website](https://nginx.org/)
