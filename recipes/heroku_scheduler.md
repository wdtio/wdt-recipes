{
  "title": "How to monitor Heroku Scheduler jobs",
  "shortTitle": "Heroku scheduler",
  "date": "2015-11-06"
}
---
Heroku is a PaaS supporting several stacks. Heroku Scheduler is an add-on for running jobs on Heroku at scheduled time intervals, similar to cron. We can use WDT.io to be notified when a Heroku Scheduler job failed.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time Heroku Scheduler runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

Assuming we're using the node.js stack, we have the following Heroku Scheduler job on one of our Heroku apps:

```bash
node cleanup.js
```
The frequency is "Every 10 minutes" which means that Heroku Scheduler runs the cleanup script every 10 minutes. Our script typically finishes in under a minute, but as long as it completes in two minutes, we're OK.

So we name our new inbound timer **heroku/cleanup**, set the schedule to **every 10 minutes** and the precision to **2 minutes**. The URL for this new timer will look something like **k.wdt.io/123abc/heroku/cleanup**. With that, we edit the job on the [Heroku Scheduler dashboard](https://scheduler.heroku.com/dashboard) click on Edit and change our job to:

```bash
node cleanup.js && curl -s -m 30 http://k.wdt.io/123abc/heroku/cleanup
```
Now we'll be notified when the job fails and can fix whatever caused the problem (maybe we have a syntax error in cleanup.js).

### More info

- [Heroku](https://heroku.com)
- [Heroku Scheduler](https://elements.heroku.com/addons/scheduler)
- [Heroku Scheduler docs](https://devcenter.heroku.com/articles/scheduler)
