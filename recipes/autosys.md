{
  "title": "How to monitor AutoSys scheduled jobs",
  "shortTitle": "Monitoring AutoSys",
  "date": "2016-04-05"
}
---
AutoSys (CA Workload Automation AE) is used for defining, scheduling and monitoring jobs on a UNIX-like system.  The AutoSys ecosystem consists of 3 components: application server, scheduler, and a database.  Jobs can be defined through the graphical user interface, or using AutoSys Job Information Language (JIL) through the command-line.

We can use WDT.io to be notified when a scheduled autosys job fails to run.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

Now every time your AutoSys job is run, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails or does not run (perhaps permissions on the script are incorrect and it has failed to launch), WDT.io won't get the kick and will send an alert.


### Example

We have the following backup.jil on our production server, it backs up assets offsite:

```bash
insert_job:prodbackup
machine:prod1
owner:root
command:/root/backup.sh
date_conditions:1
days_of_week:all
start_times:"22:00"
term_run_time:600
```

Every day at 22:00, the script **/root/backup.sh** is executed.  Autosys will automatically terminate the job if it runs for greater than 600 seconds.

So we name our new inbound timer **backupdb**, set the schedule to **every 1 day** and the precision to **10 minutes**, which is the term_run_time as defined in the JIL.  The URL for this new timer will look something like **k.wdt.io/123abc/backupdb**. With that, we edit backup.jil and change it to:

```bash
insert_job:prodbackup
machine:prod1
owner:root
command:/root/backup.sh && curl -sm 30 k.wdt.io/123abc/backupdb
date_conditions:1
days_of_week:all
start_times:"22:00"
term_run_time:600
```

Now we'll be notified when the job fails or fails to run and we can fix whatever caused the problem (maybe the backup job now takes longer than 10 minutes to run, or someone has forgotten to delete their temporary dataset).

### More info

- [AutoSys reference guide](https://support.ca.com/cadocs/0/CA%20Workload%20Automation%20AE%20Release%2011%203%206%20-%20Public%20Access-ENU/Bookshelf_Files/PDF/WA_AE_User_ENU.pdf)
- [AutoSys cheat sheet](http://supportconnectw.ca.com/public/autosys/infodocs/autosys_cheatsheet.asp)
