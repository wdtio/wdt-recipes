{
  "title": "How to monitor memory availability",
  "shortTitle": "Monitoring available memory",
  "date": "2016-05-11"
}
---
Even though WDT.io is just a simple watchdog timer that only understands HTTP, it can be used to monitor available system resources. Here we'll focus on monitoring memory (RAM).

When a server runs out of available memory, it either slows down due to swapping, or processes are being killed. We definitely want to find out about this scenario ahead of time so that we can track down why memory is being used up. Maybe one of our processes has a memory leak, or it's justified and we need to add memory to the system.

The general idea is to create a cron job that compares the available memory to a minimum value, and that then kicks a watchdog timer if the available memory is greater than or equal to the minimum. Now if the available memory is below that minimum, WDT.io won't get kicked and sends out an alert.

free is a standard unix command that displays used and available memory. Used with the options `-m` it will output the values in megabytes. The last value in the line labeled "-/+ buffers/cache:" shows the total available memory. So we can use grep and awk to get this value and turn it into a number.

```bash
free -m | grep cache: | awk '{ print int($NF) }'
```

Let's decide that we want to monitor every 5 minutes and that the minimum available memory we allow is 50MB. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "memory" with a schedule of every 5 minutes +1 minute tolerance
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/5 * * * * ((`free -m | grep cache: | awk '{ print int($NF) }'` >= 50)) && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the memory is too full (or the cronjob fails) and can fix whatever caused the problem.

### More info

- [man page for free](http://linux.die.net/man/1/free)
- [man page for grep](http://linux.die.net/man/1/grep)
- [man page for awk](http://linux.die.net/man/1/awk)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [Linux ate my ram](http://www.linuxatemyram.com)
