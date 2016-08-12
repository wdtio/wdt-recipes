{
  "title": "How to monitor Azure Scheduler",
  "shortTitle": "Monitoring Azure Scheduler",
  "date": "2016-08-12"
}
---
Azure is a cloud computing platform and infrastructure from Microsoft. Azure Scheduler is an add-on for starting jobs on a schedule. We can use WDT.io to be notified when such a job failed.

Azure Scheduler itself does not run any code. It only starts it somewhere else by invoking it via HTTP(S), a service bus, or storage queue. This is why we need to instrument the code (job) instead of the scheduler.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

How to extend the job depends on the programming language and stack being used. See [How to send kicks to WDT.io programmatically](programmatic_kicks.html) for several examples. Now every time Azure Scheduler runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

Assuming we're using the node.js stack and call an Express controller action `cleanup` every 5 minutes from the Azure Scheduler.

So we name our new inbound timer **azure/cleanup**, set the schedule to **every 5 minutes** and the precision to **1 minute**. The URL for this new timer will look something like **k.wdt.io/123abc/azure/cleanup**. With that we edit the controller action to also do the following:

```JavaScript
require('request').head('http://k.wdt.io/123abc/azure/cleanup', {timeout: 30000});
```

Now we'll be notified when the job fails and can fix whatever caused the problem.

### More info

- [Azure](https://azure.microsoft.com)
- [Azure Scheduler](https://azure.microsoft.com/en-us/services/scheduler/)
- [Azure Scheduler docs](https://azure.microsoft.com/en-us/documentation/services/scheduler/)
