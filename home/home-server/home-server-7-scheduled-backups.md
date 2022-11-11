I’ve played around with a few backup solutions for the home server and have settled on using GitHub. I’m used to the platform and Git and I think it makes the most sense to keep my versions up to date in the most lightweight way possible.

<!-- Table of Contents -->

Creating scheduled backups.
===========================

In order to create scheduled backups, we will require the following:

*   A GitHub repository to store our backup and a local git repository for pushing our code
    
*   A gitignore file to manage the files we wish to backup.
    
*   A shell script with git commit/push commands.
    
*   A cron job to run the shell script at specific intervals.
    

The GitHub repository that we’ll be using is [https://github.com/TheLaurenBarger/home-server-backup](https://github.com/TheLaurenBarger/home-server-backup).

### Creating a local git repository.

I’ve already got Git installed, so to create the repository, I simply ran the following commands:

```java
git config --global user.email "lauren.barger@outlook.com"
git config --global user.name "Badger"

git init
git remote add origin https://github.com/TheLaurenBarger/home-server-backup.git
git push --set-upstream origin master
```

### Creating a .gitignore file.

The gitignore file you create will depend on your container setup and backup preferences. I chose to exclude specific container configurations from backing up since they wouldn’t be useful in a system restore or require too much space. I created my .gitignore file in the same directory as my docker-compose.yaml file. The .gitignore file I am using currently can be found here: [https://github.com/TheLaurenBarger/home-server-backup/blob/master/.gitignore](https://github.com/TheLaurenBarger/home-server-backup/blob/master/.gitignore)

### Creating a git commit / push shell script.

I used a simple script which adds all the files in the /opt/ directory (my docker-compose environment) to the commit and creates a commit message in the format of “home-server-backup YYYY-MM-DD”. The script is stored in a file called backup.sh and reads as follows [https://github.com/TheLaurenBarger/home-server-backup/blob/master/backup.sh](https://github.com/TheLaurenBarger/home-server-backup/blob/master/backup.sh):

```java
#!/bin/bash

cd /opt/
timestamp=$(date +%Y-%m-%d)
git add .
git commit -m "home-server-backup $timestamp"
git push origin master
```

### Creating a cron job to run the backup script.

I want this backup script to run every Sunday morning around 5am. To do this my cron expression would be `0 5 * * SUN`.

This is a fantastic tool for configuring cron entries: [https://crontab.guru](https://crontab.guru)

To create the cron entry, execute the following command:

```java
sudo crontab -e
```

and add the following entry after the file comments:

```java
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
0 5 * * SUN /opt/backup.sh
```

It is actually super important to put these commands in the sudo’s crontab. That way you won’t ever run into the permissions issues that your own user will have regarding updates and backups

Conclusion.
===========

I now have a scheduled script which will backup my docker compose environment to GitHub every Sunday morning at 5am.

**←** [Home Server, Part 7: Mikrotik Port Forwarding Secure Ports](4292609.html)

[Home Server, Part 9: Scheduled Updates](17924097.html) →

References.
===========

1. [https://crontab.guru](https://crontab.guru)
2. [https://www.homeautomationguy.io/home-assistant-tips/keeping-your-home-assistant-container-up-to-date/](https://www.homeautomationguy.io/home-assistant-tips/keeping-your-home-assistant-container-up-to-date/)
3. [https://askubuntu.com/questions/173924/how-to-run-a-cron-job-using-the-sudo-command](https://askubuntu.com/questions/173924/how-to-run-a-cron-job-using-the-sudo-command)