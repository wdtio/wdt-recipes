{
  "title": "How to monitor cron jobs created with puppet",
  "shortTitle": "Monitoring puppet cron jobs",
  "date": "2016-03-17"
}
---
Puppet is an open-source declarative configuration management system.  It comes with a built-in type to manage cron jobs.
We can use WDT.io to extend these puppet cron jobs and receive notification when a scheduled job fails to run.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

Now every time cron runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails or does not run (perhaps your system has run out of space), WDT.io won't get the kick and will send an alert.

### Example

We have the following puppet resource declaration that defines a cron job for our backup to be executed by the admin user, daily at 02:00.

```bash
cron { 'backups':
    command => "/home/admin/backup.sh",
    user    => "admin",
    hour    => 2,
    minute  => 0,
}
```

Let's create a new inbound timer and name it **backup/[hostname]**.  We'll set the schedule type to cron, and use ```0 2 * * *```.  Since the job takes less than 10 minutes to run, we can use **10 minutes** as our precision.  The URL for this new inbound timer will look something like **k.wdt.io/123abc/backup/[hostname]**.  Now we can edit our puppet resource declaration:

```bash
cron { 'backups':
    command => "/home/admin/backup.sh && curl k.wdt.io/123abc/backup/${hostname}",
    user    => 'admin',
    hour    => 2,
    minute  => 0,
}
```

Notice how we are leveraging the facter fact **$hostname** to make our puppet manifest more portable.

Now we'll be notified when the job fails or fails to run and can fix whatever caused the problem.

### More info

- [puppet reference cron type](https://docs.puppetlabs.com/puppet/latest/reference/type.html#cron)
