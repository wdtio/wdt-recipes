{
  "title": "How to monitor WordPress and keep wp_cron running",
  "shortTitle": "Monitoring wp_cron",
  "date": "2015-12-11"
}
---
WordPress is a free and open-source web content management system based on PHP and MySQL.  WordPress is the most popular blogging system in use on the web. It ships with basic functionality for running repetitive tasks on a regular basis, also know as wp_cron.
However, wp_cron is solely invoked when your website receives a visitor.  
With WDT.io, you can now ping your website on regular intervals and induce the wp_cron to run on a more regular basis.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](outbound_timer.html) a new outbound timer
3. In the URL field, paste the URL of your WordPress site, specify a frequency and alert email.

Now, WDT.io will send an outbound kick and dispatch an alert when the WordPress frontend is not responding. It'll also ensure your wp_cron job runs as often as intended.


### Example

We have the following wp_cron that will backup the database and ship it offsite

```bash
wp_schedule_event( time(), 'hourly', 'wp_create_daily_backup' );
```

Once an hour, after the site is visited and wp_cron.php is invoked, the backup will happen.  Unfortunately this is a very low traffic site and might not see any visits for several hours.

So we create a new outbound timer **wp/wp_cron_kicker**, specify the WordPress frontend URL, set the schedule to **every 5 minutes** and specify an alert email.

Now we'll be notified when the site is unreachable and the kick from WDT.io will ensure that the wp_cron is run at regular intervals.

### More info

- [wp_cron function reference](https://codex.wordpress.org/Function_Reference/wp_cron)
