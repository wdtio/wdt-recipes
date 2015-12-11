{
  "title": "How to monitor Wordpress wp_cron",
  "shortTitle": "wp_cron",
  "date": "2015-12-11"
}
---
Wordpress is a free and open-source web content management system based on PHP and MySQL.  Wordpress is the most popular blogging system in use on the web.
Wordpress ships with a basic functionality for running repetitive tasks on a regular basis, also know as wp_cron.
However, wp_cron is solely invoked when your website receives a visitor.  
With wdt.io, you can now ping your website on regular intervals and induce the wp_cron to run on a more regular basis.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](outbound_timer.html) a new outbound timer
3. In the URL field, paste the URL of your wordpress site, specify a frequency and alert email.

Now, WDT will send an outbound kick and dispatch an alert when the wordpress frontend is not responding.  The mere act of visiting your site on a regular interval will initiate the wp_cron actions on more regular interval.
If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert (ie: your website could be down)


### Example

We have the following wp_cron that will backup the database and ship it offsite

```bash
wp_schedule_event( time(), 'daily', 'wp_create_daily_backup' );
```

Once a day, after the site is visited and wp_cron.php is invoked, the backup will happen.  Unfortunately this is a very low traffic site and might not see any visits for several days.

So we create a new outbound timer **wp/wp_cron_kicker**, specify the wordpress frontend URL, set the schedule to **every 5 minutes** and specify an alert email.

Now we'll be notified when the site is unreachable and the kick from WDT will ensure that the wp_cron is run at regular intervals.

### More info

- [wp_cron function reference](https://codex.wordpress.org/Function_Reference/wp_cron)
