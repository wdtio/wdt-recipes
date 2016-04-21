{
  "title": "How to monitor Moodle cron / schedule tasks",
  "shortTitle": "Monitoring moodle cron",
  "date": "2016-04-21"
}
---
Moodle is a free and open-source learning management system based on pedagogical principles.  Moodle is widely used for distance education and other e-learning initiatives.

Moodle has a built in cron process that should be run periodically.  The script runs different internal management tasks at different intervals.
If the cron is not running regularly, reports, emails, activity completions and other internal functionalities will not be recent.

With wdt.io, you can now monitor that this cron is running and receive alerts when the tasks are not completely sucessfully.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

Now every time moodle cron runs your jobs, it'll also send a kick to WDT.io upon completion. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have deployed moodle and now want to enable the cron / scheduled tasks.
Let us start by adding the moodle cron job to run every 15 minutes.

```bash
*/15 * * * *    cd /path/to/moodle/admin/cli; /usr/bin/php cron.php >/dev/null
```

Every 15 minutes, the moodle cron will run.  But what if it runs and fails, or fails to run?  This is where wdt monitoring comes into play.  

So we create a new inbound timer **moodle-cron**, set the schedule to **every 15 minutes** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/moodle-cron**.  With that, we edit the crontab using `crontab -e` and change our entry to:

```bash
*/15 * * * *    cd /path/to/moodle/admin/cli; /usr/bin/php cron.php >/dev/null && curl -sm 30 k.wdt.io/123abc/moodle-cron
```

Now we'll be notified when the moodle cron does not complete sucessfully and in a timely manner.

### More info

- [moodle home page](https://moodle.org/)
- [moodle cron reference](https://docs.moodle.org/24/en/Cron)
