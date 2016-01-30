{
  "title": "How to monitor cron jobs",
  "shortTitle": "Monitoring cron",
  "date": "2015-06-26"
}
---
Cron is a time-based job scheduler in Unix-like systems. We can use WDT.io to be notified when a job failed.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time cron runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have the following cron tab on our database server:

```bash
0 20 * * * /home/oracle/scripts/export_dump.sh
```
Every day at 20:00 the export_dump.sh script is executed. It typically finishes in under a minute, but as long as it completes in two minutes, we're OK.

So we name our new inbound timer **db/export**, set the schedule to **every 1 day** and the precision to **2 minutes**. The URL for this new timer will look something like **k.wdt.io/123abc/db/export**. With that, we edit the crontab using `crontab -e` and change our entry to:

```bash
0 20 * * * /home/oracle/scripts/export_dump.sh && curl -s -m 30 http://k.wdt.io/123abc/db/export
```
Now we'll be notified when the job fails and can fix whatever caused the problem (maybe we ran out of diskspace).

### More info

- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
