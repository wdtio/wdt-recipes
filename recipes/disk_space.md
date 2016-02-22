{
  "title": "How to monitor disk space",
  "shortTitle": "Monitoring disk space",
  "date": "2016-02-22"
}
---
Even though WDT.io is just a simple watchdog timer that only understands HTTP, it can be used to monitor available system resources. Here we'll focus on monitoring disk space.

When a server runs out of available disk space, processes that need to persist data will either freeze or crash. We definitely want to find out about this worst case scenario ahead of time so that we can track down why space is being used up and reclaim it before it completely runs out.

The general idea is to create a cron job that compares the used disk space to a maximum value, and that then kicks a watchdog timer unless the available space is higher than the maximum. Now if the used disk space is above that maximum, the kick won't be sent and WDT.io will send out an alert.

df is a standard unix command that displays free disk space. Used with the options `-l` and `--total` it will be limited to the local file system and output a line with the totals. The second last value in every line of the output is the percentage used. So we can use grep to get the line with the totals, then we use awk to get the second last value and turning it into a number.

```bash
df -l --total | grep total | awk '{ print int($(NF-1)) }'
```

Alternatively, if we want to monitor the disk space of our root volume, we just grep for that.

```bash
df -l | grep /$ | awk '{ print int($(NF-1)) }'
```

Let's decide that we want to monitor every 5 minutes and that the maximum used disk space we'll allow is 90%. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer "diskspace" with a schedule of every 5 minutes +1 minute tolerance.
3. Copy the URL of this new timer.
4. Edit the crontab and add this line:

```bash
*/5 * * * * ((`df -l --total | grep total | awk '{ print int($(NF-1)) }'` > 90)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the disk is too full (or the cronjob fails) and can fix whatever caused the problem.

### More info

- [man page for df](http://linux.die.net/man/1/df)
- [man page for grep](http://linux.die.net/man/1/grep)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
