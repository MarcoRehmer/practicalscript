---
title: "Nginx Reverse Proxy aufsetzen mit SSL"
date: 2024-06-19T09:44:17Z
draft: false
---

Nachfolgend werden die Schritte beschrieben, wie [[NGINX]] als [[Reverse Proxy]] unter Linux (z.B. Ubuntu) betrieben werden kann. Zusätzlich wird auch die Aktivierung von [[SSL]]/TLS beschrieben und ein Zertifikat von [[Lets Encrypt]] angefordert.

## NGINX installieren

Unter Linux kann NGINX einfach mit folgendem Befehl installiert werden:

```sh
sudo apt install nginx
```

nginx ist in jeder gängigen Paketverwaltung vorhanden.

### Config für Virtual Host anlegen

Es muss nun ein Virtual Host angelegt werden, welcher die Anfrage von aussen an den inneren Dienst weiterleitet. Wird also z.B. `www.example.com` angefragt, muss die Konfiguration diesen an den internen Service wie z.B. `http://localhost:8080` weiterleiten.

Hier eine Beispiel-Konfiguration, welche im Verzeichnis `etc/nginx/sites-enabled` angelegt wird:

```nginx
server {

  server_name example.com www.example.com;

  location / {
    proxy_pass http://localhost:8080;
  }
}
```

## Zertifikat von Lets Encrypt anfodern

Um ein Zertifikat von [[Lets Encrypt]] anzufordern muss sichergestellt werden, das die Ports 80 und 443 in der Firewall offen für Kommunikation sind.

Falls noch nicht geschehen, muss der `certbot` und das passende NGINX-Plugin installiert werden:

```sh
sudo apt install certbot python3-certbot-nginx`
```

Weitere Befehle können hier gefunden werden: [[Certbot Befehle]]

Jetzt kann das Zertifikat mit folgendem Befehl angefordert werden:

```sh
certbot --nginx -d example.com -d www.exmaple.com
```

`-d` gibt die anzufragende Domain an. Wurde das Zertifikat erfolgreich installiert, wird automatisch das Config-File angepasst und NGINX aufgefordert die Config neu einzulesen.
