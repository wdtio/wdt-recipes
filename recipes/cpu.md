{
  "title": "How to monitor CPU utilization",
  "shortTitle": "Monitoring cpu utilization",
  "date": "2016-06-15"
}
---
Even though WDT.io is just a simple watchdog timer that only understands HTTP, it can be used to monitor available system resources. Here we'll focus on monitoring the processor (CPU).

When a server utilizes it processor's cores to the maximum, it doesn't have enough spare cycles to process new jobs and will feel slow. When the CPU usage stays at 100%, the machine is essentially unresponsive. A rule of thumb is to stay below 80% but that limit depends on many factors such as duration and intensity of peak usage. Whatever maximum CPU usage you're comfortable with, you should monitor this maximum so you can upgrade your hardware or optimize your software when that maximum is reached.

The general idea is to create a cron job that compares the average CPU usage over some time to a maximum value. The cron job then kicks a watchdog timer if the usage is below the maximum. Now if the usage is above that maximum, WDT.io won't get kicked and sends out an alert.

**top** is a standard unix command that displays processes and the resources used. Used with the options `-bn 2 -d 1` it operates in batch mode (instead of being interactive), twice, and with a delay of one second. Piping the output through `grep '^%Cpu'` will capture just the two lines about total CPU utilization. The first line is the average since reboot, and the second line is the average during the one second we're letting `top` run. We can use `tail -n 1` to capture that second line, which will look something like the following.

```bash
%Cpu(s):  3.8 us,  0.6 sy,  0.0 ni, 94.2 id,  0.2 wa,  0.0 hi,  0.2 si,  1.0 st
```

The values we're most interested in are **us** (CPU time spent in user space), **sy** (CPU time spent in kernel space), and **ni** (CPU time spent on low priority processes). We can add them up and floor the result into a comparable integer with `awk '{print int($2+$4+$6)}'`. All together we have

```bash
top -bn 2 -d 1 | grep '^%Cpu' | tail -n 1 | awk '{print int($2+$4+$6)}'
```

Let's decide that we want to monitor every 2 minutes and that the maximum CPU usage is 80%. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "cpu" with a schedule of every 2 minutes +30 seconds tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/2 * * * * ((`top -bn 2 -d 1 | grep '^%Cpu' | tail -n 1 | awk '{print int($2+$4+$6)}'` < 80)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the CPU is used too much (or the cronjob fails) and can deal with whatever caused the problem.

### More info

- [man page for top](http://linux.die.net/man/1/top)
- [man page for grep](http://linux.die.net/man/1/grep)
- [man page for tail](http://linux.die.net/man/1/tail)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
