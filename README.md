# mysql-database-backups-script
Simple shell script to take regular MySQL database backups

---
title: "Simple script to take regular MySQL database backups"
date: 2021-03-05T13:29:03+05:30
subtitle: "Shell script to take regular MySQL database backups"
lastmod:
draft: false
description: "Shell script to take regular MySQL database backups"
images:
tags:
featuredImage: 
featuredImagePreview: 
---

This script can be implemented to take database backups regularly (hourly/daily/weekly/monthly) using cron jobs. This script is useful for database administrators, Linux administrators, web hosting providers to automate their database backup task.

# The Script

````
#!/bin/sh
#Author: Akash Mehta
#Database backup script

#Script Start

#Initialises now variable with current date and time.
now="$(date +'%d_%m_%Y_%H_%M_%S')"

#Initializes the backup file name and makes a gunzip.
filename="db_backup_$now".gz

#Path to make the backup file (that is the filename).
backupfolder="/var/www/backups"
fullpathbackupfile="$backupfolder/$filename"

#To create a logfile in backup folder with log file name containing creation date, month and year.
logfile="$backupfolder/"backup_log_"$(date +'%Y_%m')".txt

#Logs mysqldump started in the log file.
echo "mysqldump started at $(date +'%d-%m-%Y %H:%M:%S')" >> "$logfile"

#Shell command for creating backup/dump.
#Replace mydbuser, mypass, mydatabase values accordingly.
#For single database:
mysqldump --user=mydbuser --password=mypass --default-character-set=utf8 mydatabase | gzip > "$fullpathbackupfile"

# #For backing up all databases recursively uncomment this code and comment above code.
# #newcode-start
# for DB in $(mysql -e 'show databases' -s --skip-column-names); do
# mysqldump $DB | gzip > "$fullpathbackupfile";
# #mysqldump $DB > "/var/www/database-backups/$(date +%d-%h-%Y)-$DB-dump.sql";
# done
# #newcode-end

#Logs mysqldump finished in the log file.
echo "mysqldump finished at $(date +'%d-%m-%Y %H:%M:%S')" >> "$logfile"

#Changes ownership permissions of backupfile and logfile to myuser. Replace myuser value accordingly.
chown myuser "$fullpathbackupfile"
chown myuser "$logfile"
echo "file permission changed" >> "$logfile"

#Deletes old backups. Keeps latest 8 backups.
find "$backupfolder" -name db_backup_* -mtime +8 -exec rm {} \;
echo "old files deleted" >> "$logfile"

#Logs operation finished with date and time to the logfile.
echo "operation finished at $(date +'%d-%m-%Y %H:%M:%S')" >> "$logfile"
echo "**********" >> "$logfile"
exit 0
#Script End
````
