---
title: "Easy Docker Volume Backup"
date: 2024-06-22T12:00:00Z
aliases: ["/easy-docker-volume-backup"]
tags: ["Docker", "Backup"]
author: "Marco Rehmer"
draft: false
summary: Create simple backups of existing Docker volumes + automatically scheduled backups
cover:
  image: "images/docker-logo.png"
  alt: "Docker Logo"
---

You ever struggle to backup your Docker Volumes from your remote server? No problem, I show you how to archieve that.

In this post we use a tiny little tool named "vackup" to run our backup process, write a script to backup all volumes at once and schedule them for automation. Okay, let's get started!

> 💡 At the end of this post you can find this guide on YouTube

## Install "Vackup"

On GitHub you can find the Script named "vackup" from Bret Fisher ([link](https://github.com/BretFisher/docker-vackup)) which makes it very easy to backup one of our volumes. Just visit the repository and follow the installation instructions (or use the command below). You can just grab the curl-command and paste it to your machine where your volumes are located at.

```bash
sudo curl -sSL https://raw.githubusercontent.com/BretFisher/docker-vackup/main/vackup -o /usr/local/bin/vackup && sudo chmod +x /usr/local/bin/vackup
```

> 💡 Take a closer look to the first sentence in the repository description: this script was used to create the official backup extension for Docker Desktop. So you can be sure the script works how it should.

After you install the script, give it a little test to check if everything works fine. For that, enter `vackup` in your terminal, and if you see a usage description, everything is ready to go.

## Backup all Volumes

Now we can write a little script to backup all volumes at once. Our backup script will grab the current date and attach it to our exported backup-files. And in addition, we create a folder named by the current date to give our backups a little structure.

Whereever you are on your remote machine, just create a new shell file with a fancy name, for example `backup_script.sh` and inside paste the following code:

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

Backup all volumes with timestamp

### Test the Script

To check if our script runs correctly, we give it a little check. Change the permission for execution with `sudo chmod +x backup_script.sh` and run your script with `sudo ./backup_script.sh`

The vackup utility will do the following steps:

- download the busybox image if not available
- spin up a new container with busybox image and the provided volume attached
- zip all files inside the volume and generate a tarballed archive

And that's it! Our volumes are now backed-up!

## Download our backups

A backup file on the server itself is nice, but most of the time it's better to store them externally, for example on your local hard drive. Because it's better to have a backup – of your backup, right?

With one simple SCP command (wich stands for "secure copy") we download our files:

```bash
scp -r remote-user@<remote-ip-address>:/path/to/backup-dir/* /path/to/local/destination/
```

Don't forget the little asterisk at the end of the first path to grab **all** files and folders at the target path. 💡

## Schedule automatic backups

We are lazy dudes and so we don't want to execute our script manually everytime. For that, we use cron to run our self-written script automatically. Go to your crontab editor with `sudo crontab -e` and paste the following line:

```bash
0 3 * * 1 /home/username/backup_script.sh
```

Now, our server will create backups **every Week on Monday** in date-named directories. Easy as 1-2-3.

## Bonus: use rsync

If you don't wanna download all files at once every time, you can use **rsync** to download only the newest changes. Instead of using the SCP command just use the following rsync command:

```bash
rsync -va remote-user@<remote-ip-address>:/path/to/backup-dir/* /path/to/local/destination/
```

## Watch this Guide on YouTube

{{< youtube PGp3Sb77-cg >}}
