{
  "title": "How to monitor Zeppelin schedule tasks",
  "shortTitle": "Monitoring Zeppelin tasks",
  "date": "2017-01-18"
}
---
Zeppelin is a web-based notebook that enables interactive data analytics.  Zeppelin is widely used by data-scientists to interact and collabare with data.  

Zeppelin comes with a facility to schedule Notes periodically, using a cron-like syntax.  However, these are difficult to monitor.

With WDT.io, you can now monitor that these scheduled Notes are running and receive alerts when the Notes are not completed successfully.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the notebook to send a kick to the URL copied from step 3

Now every time your Note runs, it'll also send a kick to WDT.io upon completion. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have an existing notebook with id **MYNOTEBOO**

Let us add a schedule for this notebook to execute it every 5 minutes.
```bash
curl -X POST -H "Content-Type: application/json" -d '{"cron": "0 0/5 * * * ?"}' http://zeppelin-url:zeppelin-port/api/notebook/cron/MYNOTEBOO
```

Every 5 minutes, the Note will run.  But what if it runs and fails, or fails to run?  This is where WDT.io monitoring comes into play. 

So we create a new inbound time **zep-note1**, set the schedule to **every 5 minutes** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/zep-note1**.  With that, we edit the Zeppelin Note and add a shell paragraph (%sh):
```bash
%sh
curl k.wdt.io/123abc/zep-note1
```

Now we'll be notified when the Zeppelin Note does not complete successfully and in a timely manner.

### More info

- [Zeppelin home page](https://zeppelin.apache.org/)
- [Zeppelin REST api reference](https://github.com/apache/zeppelin/blob/master/docs/rest-api/rest-notebook.md)
