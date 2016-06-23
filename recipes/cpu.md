{
  "title": "How to monitor cpu utilization",
  "shortTitle": "Monitoring cpu utilization",
  "date": "2016-06-15"
}
---
Even though WDT.io is just a simple watchdog timer that only understands HTTP, it can be used to monitor available system resources. Here we'll focus on monitoring the processor (cpu).

When a server utilizes it processor's cores to the maximum, it doesn't have enough spare cycles to process new jobs and will feel slow. When the cpu usage stays at 100%, the machine is essentially unresponsive. A rule of thumb is to stay below 80% but that limit depends on many factors such as duration and intensity of peak usage. Whatever maximum cpu usage you're comfortable with, you should monitor this maximum so you can upgrade your hardware or optimize your software when that maximum is reached.

The general idea is to create a cron job that compares the average cpu usage over some time to a maximum value. The cron job then kicks a watchdog timer if the usage is below the maximum. Now if the usage is above that maximum, WDT.io won't get kicked and sends out an alert.

**vmstat** is a standard unix command that among other things displays cpu activity. Used with `vmstat 1 2` it runs twice, and with a delay of one second. The output will be something like the following:

```bash
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0  99024  10196 306184    0    0     4   249    5    1  1  2 97  0  0  
 0  0      0  99024  10196 306184    0    0     0     0 2154 4245  0  2 98  0  0  
```

The second to last line is the average since boot, and the last line is the average during the one second we're letting `vmstat` run. We can use `tail -1` to capture that last line.

The values we're most interested in are **us** (cpu time spent in user space) and **sy** (cpu time spent in kernel space). We can add them up with `awk '{print int($13+$14)}'`. All together we have

```bash
vmstat 1 2 | tail -1 | awk '{print int($13+$14)}'
```

Let's decide that we want to monitor every 2 minutes and that the maximum cpu usage is 80%. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "cpu" with a schedule of every 2 minutes +30 seconds tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/2 * * * * ((`vmstat 1 2 | tail -1 | awk '{print int($13+$14)}'` < 80)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the cpu is used too much (or the cronjob fails) and can deal with whatever caused the problem.

### More info

- [man page for vmstat](http://linux.die.net/man/8/vmstat)
- [man page for tail](http://linux.die.net/man/1/tail)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
