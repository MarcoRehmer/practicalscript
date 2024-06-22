---
title: "Einfaches Backup von Docker Volumes"
date: 2024-06-22T12:00:00Z
aliases: ["/easy-docker-volume-backup"]
tags: ["Docker", "Backup"]
author: "Marco Rehmer"
draft: false
summary: Erstellen von einfachen Backups der vorhandenen Docker Volumes + automatisch geplante Backups
cover:
  image: "images/docker-logo.png"
  alt: "Docker Logo"
---

Hast du jemals Probleme gehabt, deine Docker-Volumes von deinem Remote-Server zu sichern? Kein Problem, ich zeige dir, wie du das bewerkstelligen kannst.

In diesem Beitrag verwenden wir ein winzig kleines Tool namens "Vackup", um unseren Backup-Prozess zu starten, ein Skript zu schreiben, um alle Volumes auf einmal zu sichern und sie für die Automatisierung zu planen. Okay, lasst uns anfangen!

> 💡 Am Ende dieses Beitrags findest du diesen Leitfaden auf YouTube

## "Vackup" installieren

Auf GitHub findest du das Skript "vackup" von Bret Fisher ([link](https://github.com/BretFisher/docker-vackup)), mit dem du ganz einfach ein Backup eines deiner Volumes erstellen kannst. Besuche einfach das Repository und folge den Installationsanweisungen (oder verwende den unten stehenden Befehl). Du kannst einfach den curl-Befehl nehmen und ihn auf deinem Rechner im Terminal einfügen, auf dem sich deine Volumes befinden.

```bash
sudo curl -sSL https://raw.githubusercontent.com/BretFisher/docker-vackup/main/vackup -o /usr/local/bin/vackup && sudo chmod +x /usr/local/bin/vackup
```

> 💡 Schau dir den ersten Satz in der Repository-Beschreibung genauer an: Dieses Skript wurde verwendet, um die offizielle Backup-Erweiterung für Docker Desktop zu erstellen. Du kannst also sicher sein, dass das Skript so funktioniert, wie es sollte.

Nachdem du das Skript installiert hast, führe einen kleinen Test durch, um zu prüfen, ob alles einwandfrei funktioniert. Gib dazu `vackup` in dein Terminal ein, und wenn du eine Benutzungsbeschreibung siehst, ist alles einsatzbereit.

## Sichere deine Volumes

Jetzt können wir ein kleines Skript schreiben, um alle Datenträger auf einmal zu sichern. Unser Backup-Skript erfasst das aktuelle Datum und hängt es an die exportierten Backup-Dateien an. Außerdem erstellen wir einen Ordner, der nach dem aktuellen Datum benannt ist, um unseren Backups eine gewisse Struktur zu geben.

Wo auch immer du dich auf deinem entfernten Rechner befindest, erstell einfach eine neue Shell-Datei mit einem ausgefallenen Namen, zum Beispiel `backup_script.sh`, und füge darin den folgenden Code ein:

```shell
    #!/bin/bash

    BACKUP_DIR="/home/username/backups"

    # create folder if it doesn't exist
    if [ ! -d "$BACKUP_DIR" ]; then
      mkdir -p $BACKUP_DIR"
    fi

    cd $BACKUP_DIR

    # grab all volume names
    VOLUMES=$(docker volume ls --format "{{.Name}}")

    # loop through volumes and backup one by one
    for VOLUME in $VOLUMES; do
      TIMESTAMP=(date +%Y_%m_%d)
      BACKUP_FILE="$VOLUME-$TIMESTAMP.tar.gz"

      # create folder for current date
      mkdir -p "$BACKUP_DIR/$TIMESTAMP"
      cd "$BACKUP_DIR/$TIMESTAMP"

      # run backup tool
      vackup export $VOLUME $BACKUP_FILE
    done
```

### Teste das Skript

Um zu prüfen, ob unser Skript korrekt läuft, führen wir eine kleine Prüfung durch. Ändere die Berechtigung zur Ausführung mit `sudo chmod +x backup_script.sh` und führe dein Skript mit `sudo ./backup_script.sh` aus.

Das Dienstprogramm "Vackup" führt die folgenden Schritte aus:

- Herunterladen des busybox-Images, falls nicht vorhanden
- Erzeugen eines neuen Containers mit dem busybox-Image und dem angehängten Volume
- alle Dateien innerhalb des Volumes zippen und ein Tarballed-Archiv erzeugen

Und das war's! Unsere Volumes sind nun gesichert!

## Herunterladen der Backups

Eine Sicherungsdatei auf dem Server selbst ist schön, aber meistens ist es besser, sie extern zu speichern, zum Beispiel auf deiner lokalen Festplatte. Denn es ist besser, ein Backup zu haben - von deinem Backup, richtig? 😎

Mit einem einfachen SCP-Befehl (was für "secure copy" steht) laden wir unsere Dateien herunter:

```bash
scp -r remote-user@<remote-ip-address>:/path/to/backup-dir/* /path/to/local/destination/
```

Vergiss nicht das kleine Sternchen am Ende des ersten Pfades, um **alle** Dateien und Ordner im Zielpfad zu erfassen. 💡

## geplante automatische Backups

Wir sind faule Devs und wollen unser Skript nicht jedes Mal manuell ausführen. Dafür benutzen wir `cron`, um unser selbstgeschriebenes Skript automatisch auszuführen. Rufe den crontab-Editor mit `sudo crontab -e` auf und füge die folgende Zeile ein:

```bash
0 3 * * 1 /home/username/backup_script.sh
```

Jetzt erstellt unser Server **jede Woche am Montag** Backups in Verzeichnissen mit Datumsangaben. So einfach wie 1-2-3.

## Bonus: rsync benutzen

Wenn du nicht jedes Mal alle Dateien auf einmal herunterladen möchtest, kannst du **rsync** verwenden, um nur die neuesten Änderungen herunterzuladen. Anstelle des SCP-Befehls verwende einfach den folgenden rsync-Befehl:

```bash
rsync -va remote-user@<remote-ip-address>:/path/to/backup-dir/* /path/to/local/destination/
```

## Sieh dir den Beitrag auf Youtube an

{{< youtube PGp3Sb77-cg >}}
