{
  "title": "How to monitor Wordpress and control wp_cron externally",
  "shortTitle": "Monitoring wp_cron manually",
  "date": "2015-12-11"
}
---
Wordpress is a free and open-source web content management system based on PHP and MySQL.  Wordpress is the most popular blogging system in use on the web.
Wordpress ships with a basic functionality for running repetitive tasks on a regular basis, also know as wp_cron.
However, wp_cron is solely invoked when your website receives a visitor, some webmaster will opt to disable this function or manually control it.
With wdt.io, you can now regain control on your Wordpress scheduled tasks and receive alerts when the tasks are not completely sucessfully.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

Now every time wp_cron runs your jobs, it'll also send a kick to WDT.io upon completion. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We firstly disable the internal wp_cron function by adding the following to wp-config.php

```php
//Disable internal Wp-Cron function
define('DISABLE_WP_CRON', true);
```

Then we setup a real cron job on the server or through our host's control panel appending the WDT curl command.

```bash
*/5 * * * *    wget -O- -q -t 1 http://www.server.com/wp-cron.php?doing_wp_cron=1 > /dev/null 2>&1 
```

Once a day, after the site is visited and wp_cron.php is invoked, the backup will happen.  Unfortunately this is a very low traffic site and might not see any visits for several days.

So we create a new inbound timer **wp/wp_cron_kicker**, set the schedule to **every 5 minutes** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/wp/wp_cron_kicker**.  With that, we edit the crontab using `crontab -e` and change our entry to: 

```bash
*/5 * * * *    wget -O- -q -t 1 http://www.server.com/wp-cron.php?doing_wp_cron=1 > /dev/null 2>&1 && curl -sm 30 k.wdt.io/123abc/
```

Now we'll be notified when the wp_cron does not completely sucessfully and in a timely manner.

### More info

- [wp_cron function reference](https://codex.wordpress.org/Function_Reference/wp_cron)
