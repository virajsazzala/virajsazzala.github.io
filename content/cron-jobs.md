+++
title = "Cron Jobs: A gift for the lazy!"
date = 2023-01-11

[taxonomies]
tags = ["automate", "linux", "guide"]
+++


Cron jobs allow you to schedule tasks to run automatically at a specified time and date. They are commonly used for running periodic jobs like backups, sending emails, data aggregation, etc. 

<!-- more -->

## How Cron Jobs Work

Cron jobs work by running cron (the scheduler) as a daemon process that executes commands at the specified schedule. 

To schedule a cron job, you edit the crontab file (a config file that tells cron which jobs to run) and specify:

- The command to execute
- The schedule for the command

For example:

```
# Run backup at 2am every day
0 3 * * * ./backup.sh
```

This runs `backup.sh` at 3 AM every day.

You can specify a wide variety of schedules using the 5 time/date fields:

```
.---------------- minute (0 - 59) 
|  .------------- hour (0 - 23)
|  |  .---------- day of month (1 - 31)  
|  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
|  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7)
|  |  |  |  |
*  *  *  *  * command to be executed
```

## Cron Job Examples

Some examples of cron schedules:

- `0 12 * * *` - run at 12 PM every day 
- `*/5 * * * *` - run every 5 minutes
- `0 0 1 * *` - run at 00:00 on 1st of every month

## Managing Cron Jobs

There are a few key commands for managing cron jobs:

- `crontab -e` - Edit the crontab file 
- `crontab -l` - List cron jobs
- `service cron reload` - Reload cron daemon after editing crontab

And that's the basics of how to use cron jobs! They provide a handy way to run periodic tasks.