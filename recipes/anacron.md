{
  "title": "How to monitor scheduled anacron jobs",
  "shortTitle": "Monitoring anacron",
  "date": "2016-03-14"
}
---
Anacron is very similar to cron, wherein it is used to perform tasks on a scheduled basis on a unix-like system.  Anacron will also ensure that tasks are eventually run even when the host is turned off part of the time.  This is ideal for laptops and other machines that can be in various power states when a scheduled task is meant to run.
We can use WDT.io to be notified when a scheduled anacron job fails to run.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time anacron runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails or does not run (perhaps the server has been off for longer than expected), WDT.io won't get the kick and will send an alert.


### Example

We have the following /etc/anacrontab on our development laptop that backs up local assets on the laptop:

```bash
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=10
START_HOURS_RANGE=12-13

7       10      backup             /home/lester/backup.sh
```

Every 7 days, starting 10 minutes past the START_HOUR_RANGE with a RANDOM_DELAY of up to 10 minutes, the job will launch.  It takes roughly 10 minutes to run.

So we name our new inbound timer **backupdb**, set the schedule to **every 7 day** and the precision to **30 minutes**, which is RANDOM_DELAY + DELAY + time to run. The URL for this new timer will look something like **k.wdt.io/123abc/backupdb**. With that, we edit /etc/anacrontab and change it to:

```bash
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=45
START_HOURS_RANGE=3-22

7       25      backup             /home/lester/backup.sh && curl -sm 30 k.wdt.io/123abc/backupdb
```

Now we'll be notified when the job fails or fails to run and can fix whatever caused the problem (maybe our laptop has been turned off for a long period of time or is not connected to the network).

### More info

- [man page for anacrontab](http://linux.die.net/man/5/anacrontab)
- [man page for anacron](http://linux.die.net/man/8/anacron)
