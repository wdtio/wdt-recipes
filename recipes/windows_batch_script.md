{
  "title": "How to monitor Windows task scheduler batch scripts",
  "shortTitle": "wintask",
  "date": "2015-09-17"
}
---
Windows Task Scheduler is a Windows component that provides the ability to launch scripts or programs on a defined scheduled. We can use WDT to be notified when a job failed.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. Create a new inbound timer.
   1. Provide a name for the timer.  Forward slashes can be used to provide grouping, for example *production/cron/backup*
   2. Provide a description.  The description will be included in alert emails.
   3. Select a schedule (*every*) that mirrors your cron job.
   4. Select a precision (*tolerance*) to account for variations in the time it can take to execute the job.
   5. Provide an alert email, this is where job notifications will be sent.
   6. Save the new timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.  Note that you will want to have curl installed on and in your path.  Here are instructions for installing [curl on windows](windows_curl)

Now every time cron runs your job, it'll also send a kick to WDT. This regular kick prevents WDT from sending an alert to you. If, for whatever reason the job fails, WDT won't get the kick and will send an alert.


### Example

We have a scheduled task to copy backups from our SQL server to our NAS, it is launched via a batch script.
Every day at 20:00 the copy-backup.bat script is executed. It typically finishes in under a minute, but as long as it completes in two minutes, we're OK.

So we name our new inbound timer **db/copy-backup**, set the schedule to **every 1 day** and the precision to **2 minutes**. The URL for this new timer will look something like **k.wdt.io/123abc/db/copy-backup**. With that, we can edit our batch script, for example:

```batch
COPY /Y "C:\DB\bk\*.BAK" \\NAS\backups\DB && C:\curl.exe k.wdt.io/123abc/db/copy-backup
```
Now we'll be notified when the job fails and can fix whatever caused the problem (maybe we ran out of diskspace).

### More info

- [man page for Windows Task Scheduler](http://windows.microsoft.com/en-ca/windows/schedule-task#1TC=windows-7)
