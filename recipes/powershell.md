{
  "title": "How to monitor Windows PowerShell Scripts",
  "shortTitle": "Monitoring PowerShell",
  "date": "2015-11-27"
}
---
Windows PowerShell is a task automation and configuration management framework from Microsoft built on .NET framework.  It provides a variety of tools for facilitating machine management.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time the script is run, WDT should receive an inbound kick and dispatch an alert when the kick is not received.

### Example

We have a script that runs periodically to make sure our Quickbooks backup is pushed to remote sources.  It writes output to a local log.
Without reviewing the log periodically, we are unaware of failures.  

So we name our new inbound timer **backup/qb**, set the schedule to **every 1 day** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/backup/qb**.  With that, we edit the PowerShell script and add the following line:

```
Invoke-RestMethod "http://k.wdt.io/123abc/backup/qb"
```

Now we'll be notified when the job fails and can fix whatever caused the problem (maybe our remote source is unavailable).

### More info

- [Invoke-RestMethod](https://technet.microsoft.com/en-us/library/hh849971.aspx)
