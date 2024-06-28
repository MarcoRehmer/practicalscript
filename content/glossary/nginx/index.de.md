---
title: "NGINX Web Server"
date: 2024-06-19T09:44:17Z
draft: false
---

NGINX ist ein Open Source Webserver, Reverse-Proxy, Load Balancer und HTTP-Cache in einem. Es ist bekannt für seine hohe Leistung, Stabilität, einfache Konfiguration und geringem Ressourcenverbrauch. Dadurch ist es ein beliebtes Tool und wird in vielen Enterprise Umgebungen eingesetzt.

## Docker Image

NGINX ist als [Docker Image im Docker Hub](https://hub.docker.com/_/nginx) verfügbar und kann so in vielen virtualisierten Umgebungen eingesetzt werden.

```bash
docker pull nginx
```

Für die Verwendung einem eigenen Docker Image muss eine passende Konfigurationsdatei in den Container kopiert werden:

```docker
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
```

### Docker Compose

Hier eine Beispiel-Implementation von NGINX in Docker Compose:

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

- [NGINX Reverse Proxy aufsetzen + SSL Zertifikat](/posts/nginx-reverse-proxy)

## References

- [Offizielle Webseite](https://nginx.org/)
