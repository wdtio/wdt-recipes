{
  "title": "How to detect noisy neighbors",
  "shortTitle": "Detecting noisy neighbors",
  "date": "2016-07-02"
}
---
In shared virtual environments like a cloud, noisy neighbors can negatively impact the performance of your applications. A neighbor is a virtual machine sharing the same resources as you, owned by someone else (another tenant). "Noise" can be in the form of stolen cpu cycles, used up memory, maxed out bandwidth, and saturated disk I/O. Modern virtualization implementations limit cpu stealing to a few percent or prevent it altogether, allocate fixed memory, and prevent maxing out the bandwidth by individual tenants. The remaining weak point is disk I/O which often can be saturated by an individual tenant forcing other tenants to wait for their turn. If your application depends on reading or writing to disk, this I/O wait can essentially stall it.

If our processes would have to wait to access the disk, it shows up as cpu **wa** time when using the `top` or `vmstat` command. We can easily monitor this value with a recurring cron job to alert us of neighbors being noisy. The general idea is to have the cron job compare the average cpu **wa** usage over some time to a maximum value. The cron job then kicks a watchdog timer if the usage is below the maximum. Now if the usage is above that maximum, WDT.io won't get kicked and sends out an alert.

For this recipe we use **vmstat**. Used with `vmstat 1 2` it runs twice, and with a delay of one second. The output will be something like the following:

```bash
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0  99024  10196 306184    0    0     4   249    5    1  1  2 97  0  0  
 0  0      0  99024  10196 306184    0    0     0     0 2154 4245  0  2 98  0  0  
```

The second to last line is the average since boot, and the last line is the average during the one second we're letting `vmstat` run. We can use `tail -1` to capture that last line, and `awk '{print $(NF-1)}'` to get the second to last field of that line. All together we have:

```bash
vmstat 1 2 | tail -1 | awk '{print $(NF-1)}'
```

Let's decide that we want to monitor every 2 minutes and that the maximum I/O wait is 80%.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "noisy-neighbor" with a schedule of every 2 minutes +30 seconds tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/2 * * * * ((`vmstat 1 2 | tail -1 | awk '{print $(NF-1)}'` < 80)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the neighbors are too noisy (or the cronjob fails) and can contact support or move our application to another VM. If the noise is so bad that our cron job fails to start on time, we'll get alerted as well which shows the advantage of passive inbound monitoring over active monitoring.

### More info

- [noisy neighbors on Wikipedia](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)
- [man page for vmstat](http://linux.die.net/man/8/vmstat)
- [man page for tail](http://linux.die.net/man/1/tail)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
