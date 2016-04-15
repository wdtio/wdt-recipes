{
  "title": "How to monitor Agenda",
  "shortTitle": "Monitoring Agenda",
  "date": "2016-02-14"
}
---
Agenda is a light-weight job scheduling library for Node.js. We can use WDT.io to be notified when a recurring job fails to execute.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time Agenda runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have a node application "user-manager" with the following JavaScript code:

```JavaScript
var Agenda = require('agenda');
var agenda = new Agenda(options);

agenda.define('delete old users', function(job, done) {
  User.remove({lastLogIn: {$lt: twoDaysAgo}}, done);
});

agenda.on('ready', function() {
  agenda.every('3 minutes', 'delete old users');
  agenda.start();
});
```
This job executes every three minutes.

So we name our new inbound timer **users/delete-old**, set the schedule every **3 minutes** and the precision to **1 minute**. The URL for this new timer will look something like **k.wdt.io/123abc/users/delete-old**. With that, we change the code to:

```JavaScript
var request = require('request');
var Agenda = require('agenda');
var agenda = new Agenda(options);

agenda.define('delete old users', function(job, done) {
  User.remove({lastLogIn: {$lt: twoDaysAgo}}, done);
  request.head('http://k.wdt.io/123abc/users/delete-old', {timeout: 30000});
});

agenda.on('ready', function() {
  agenda.every('3 minutes', 'delete old users');
  agenda.start();
});
```
Now we'll be notified when the job fails and can fix whatever caused the problem (maybe the user-manager app stopped running).


### Seconds

WDT.io doesn't support seconds for cron schedules. If your Agenda job uses a schedule with seconds, just drop the seconds for WDT.io. For example, if your code uses `.every('40 seconds')`, use every **1 minute** as the cron schedule in WDT.io.


### More info

- [Agenda](https://github.com/rschmukler/agenda)
- [Request](https://github.com/request/request)
