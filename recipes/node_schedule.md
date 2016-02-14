{
  "title": "How to monitor Node Schedule",
  "shortTitle": "Monitoring Node Schedule",
  "date": "2016-02-14"
}
---
Node Schedule is a flexible cron-like and not-cron-like job scheduler for Node.js. We can use WDT.io to be notified when a recurring job fails to execute.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time Node Schedule runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have a node application "hhgttg" with the follwing JavaScript code:

```JavaScript
var schedule = require('node-schedule');

schedule.scheduleJob('42 * * * *', function() {
  console.log('The answer to life, the universe, and everything!');
});
```
This job executes every day, every hour at 42 minutes past the hour.

So we name our new inbound timer **hhgttg/answer**, set the cron schedule to **42 \* \* \* \*** and the precision to **1 minute**. The URL for this new timer will look something like **k.wdt.io/123abc/hhgttg/answer**. With that, we change the code to:

```JavaScript
var schedule = require('node-schedule');
var request = require('request');

schedule.scheduleJob('42 * * * *', function() {
  console.log('The answer to life, the universe, and everything!');
  request.head('http://k.wdt.io/123abc/hhgttg/answer', {timeout: 30000});
});
```
Now we'll be notified when the job fails and can fix whatever caused the problem (maybe the hhgttg app stopped running).


### Seconds

WDT.io doesn't support seconds for cron schedules. If your Node Schedule uses a cron expressions with seconds, just drop the seconds for WDT.io. For example, if your code uses `'*/20 * * * * *'`, i.e. it runs every 20 seconds, use **\* \* \* \* \*** as the cron schedule in WDT.io.


### More info

- [Node Schedule](https://github.com/node-schedule/node-schedule)
- [Request](https://github.com/request/request)
