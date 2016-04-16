{
  "title": "How to monitor disk space",
  "shortTitle": "Monitoring disk space",
  "date": "2016-02-22"
}
---
Even though WDT.io is just a simple watchdog timer that only understands HTTP, it can be used to monitor available system resources. Here we'll focus on monitoring disk space.

When a server runs out of available disk space, processes that need to persist data will either freeze or crash. We definitely want to find out about this worst case scenario ahead of time so that we can track down why space is being used up and reclaim it before it completely runs out.

The general idea is to create a cron job that compares the used disk space to a maximum value, and that then kicks a watchdog timer unless the used space is higher than the maximum. Now if the used disk space is above that maximum, WDT.io won't get kicked and sends out an alert.

df is a standard unix command that displays free disk space. Used with the options `-l` and `--total` it will be limited to the local file system and output a line with the totals. The fifth value in every line of the output is the percentage used. So we can use tail to get the last line with the totals, then we use awk to get the fifth value and turn it into a number.

```bash
df -l --total | tail -1 | awk '{ print int($5) }'
```

Alternatively, we can monitor the disk space of our root volume.

```bash
df -l / | tail -1 | awk '{ print int($5) }'
```

Let's decide that we want to monitor every 5 minutes and that the maximum used disk space we allow is 90%. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "diskspace" with a schedule of every 5 minutes +1 minute tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/5 * * * * ((`df -l --total | tail -1 | awk '{ print int($5) }'` < 90)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the disk is too full (or the cronjob fails) and can fix whatever caused the problem.

### More info

- [man page for df](http://linux.die.net/man/1/df)
- [man page for tail](http://linux.die.net/man/1/tail)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
