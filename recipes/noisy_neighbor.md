{
  "title": "How to detect noisy neighbors",
  "shortTitle": "Detecting noisy neighbors",
  "date": "2016-07-04"
}
---
In shared virtual environments like a cloud, noisy neighbors can negatively impact the performance of your applications. A neighbor is a virtual machine sharing the same resources as you, owned by someone else (another tenant). "Noise" can be in the form of stolen cpu cycles and saturated disk I/O.

Disk I/O can be saturated by an individual tenant forcing other tenants to wait for their turn and stealing of cpu cycles. If your application depends on reading or writing to disk, this I/O wait can essentially stall it.

One possible reason for cpu being stolen is if the IaaS provider oversold the physical server and the VMs on that server are competing for the cpu. If your application needs to be fast and is cpu bound, stolen cpu cycles will impact your performance.

If our processes would have to wait to access the disk, it shows up as cpu **wa** time when using the `top` or `vmstat` command. Steal time shows up as **st**. We can easily monitor these values with a recurring cron job to alert us of neighbors being noisy. The general idea is to have the cron job compare the average cpu **wa** and **st** usage over some time to maximum values. The cron job then kicks a watchdog timer if the usage is below the maximums. Now if the usage is above those maximums, WDT.io won't get kicked and sends out an alert.

For this recipe we use **vmstat**. Used with `vmstat 1 2` it runs twice, and with a delay of one second. The output will be something like the following:

```bash
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0  99024  10196 306184    0    0     4   249    5    1  1  2 97  0  0  
 0  0      0  99024  10196 306184    0    0     0     0 2154 4245  0  2 98  0  0  
```

The second to last line is the average since boot, and the last line is the average during the one second we're letting `vmstat` run. We can use `tail -1` to capture that last line, and `awk` with `$(NF-1)` and `$(NF)` to get the second to last field (wa) and the last field (st). Furthermore, we can compare the values of these fields to their maximums and have `awk` return a successful exit status (0) if both values are below their maximums. All together we have:

```bash
vmstat 1 2 | tail -1 | awk '{exit ($(NF-1) <= WA_MAX && $(NF) <= ST_MAX) ? 0 : 1}'
```

Let's decide that we want to monitor every 2 minutes, that the maximum I/O wait is 50%, and the maximum cpu steal is 20%.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "noisy-neighbor" with a schedule of every 2 minutes +30 seconds tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/2 * * * * vmstat 1 2 | tail -1 | awk '{exit ($(NF-1) <= 50 && $(NF) <= 20) ? 0 : 1}' && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the neighbors are too noisy (or the cronjob fails) and can contact support or move our application to another VM. If the noise is so bad that our cron job fails to start on time, we'll get alerted as well which shows the advantage of passive inbound monitoring over active monitoring.

### More info

- [noisy neighbors on Wikipedia](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)
- [man page for vmstat](http://linux.die.net/man/8/vmstat)
- [man page for tail](http://linux.die.net/man/1/tail)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
