---
title: "Performance von AWS S3 Buckets"
date: 2024-06-30T17:21:32+02:00
aliases: ["/aws-s3-performance"]
tags: ["AWS", "Performance"]
author: "Marco Rehmer"
draft: false
summary: "Lese- und Schreib-Performance von Amazons S3 Buckets unter der Lupe"
cover:
  image: "images/aws-s3-logo.png"
  alt: "AWS S3 Bucket logo"
---

S3 Buckets sind ideal um Daten abzulegen, die sich selten ändern aber häufig abgefragt werden. Um zum Beispiel große Datenmengen abzulegen, kann ein S3 Bucket eine geeignete Wahl sein, denn die Performance zum lesen und schreiben skaliert mit den Anforderungen.

## Standard Performance

Greift man auf den Regulären Wege auf den S3 Bucket bzw. auf Objekte innerhalb des Buckets zu, so kann der S3 Bucket folgende Performance-Werte an den Tag legen:

- **3.500 Requests/Sekunde** bei Funktionen wie PUT/COPY/POST/DELETE
- **5.500 Requests/Sekunde** bei den Funktionen GET/HEAD

Das ist an sich schon eine ordentliche Performance, kann aber schnell auch zu einem Bottleneck werden, wenn tausende Clients sehr schnell Daten lesen wollen, oder sehr viel geschrieben wird (z.B. Metriken von Millionen von Clients).

## Partitionierung mit Prefixes

Abhilfe schaffen hier Objekt-Prefixes: Dadurch können die Objekte im S3 Bucket partitioniert und die Zugriffs-Performance praktisch unendlich gesteigert werden. Denn **jede Partition** bringt die oben genannte Performance mit sich. Werden die Objekte also in 10 Partitionen geteilt, erreicht man eine Performance von **35.000 / 55.000 Requests pro Sekunde**. Das ist wahnsinnig schnell!

Die Anzahl an Prefixes innerhalb des Buckets ist unbegrenzt, mit der richtigen Daten-Organisation kann also die gewünschte Performance erreicht werden und es liegt allein bei der Anwendungs-Architektur diese zu erreichen.

### Prefixes einsetzen

Die Verwendung ist sehr simpel: ein Prefix ist lediglich der Anfang eines Objekt-Namens. Da es möglich ist Objekte mit verschiedenen Sonderzeichen abzulegen, können auch Trennzeichen wie `/`, `-` oder `.` eingesetzt werden.

Möchte man also Daten von allen Städten der Welt in seinem S3 Bucket ablegen, kann das Prefix folgendermaßen aussehen:

- Europe/France/Nouvelle-Aquitaine/Bordeaux
- North America/Canada/Quebec/Montreal
- North America/USA/Washington/Bellevue
- North America/USA/Washington/Seattle

Beim Zugriff kann nun mit mit `Delimiter` und `Prefix` der Zugriff auf die gewünschte Partition eingeschränkt werden.

Zum ausgeben der Daten von Nord Amerika gibt man `Delimiter='/'` und `Prefix='North America/USA/'` für die Partition an und schon schon erreicht man die entsprechenden Daten.
