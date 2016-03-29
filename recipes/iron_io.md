{
  "title": "How to monitor IronMQ, IronWorker, and IronCache",
  "shortTitle": "Monitoring IronMQ, IronWorker, and IronCache",
  "date": "2016-03-28"
}
---
Iron.io offers a commercial workload management solution comprised of a message queue service (IronMQ), a job processing manager (IronWorker), and a cache (IronCache). We can use WDT.io to monitor all three.

### IronMQ

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. Create a new outbound timer.
3. Name the timer after the project and queue.
4. Use the webhook URL of your queue minus "/webhook" as the URL of the new timer.
5. Select GET.
6. Select every 1 minute as the schedule.
7. Save the new timer.

For example, if the project name on iron.io is "homework" and the name of the queue is "task-queue", the webhook URL will look something like https://mq-aws-eu-west-1-1.iron.io/3/projects/123/queues/task-queue/webhook?oauth=4a5b6c. So we can name the outbound watchdog timer **homework/task-queue** and use **https://mq-aws-eu-west-1-1.iron.io/3/projects/123/queues/task-queue?oauth=4a5b6c** as URI.

Now when the queue becomes unavailable, WDT.io will send an alert.

### IronWorker

Workers are simply programs, so we extend our worker program to kick an inbound watchdog timer so that an alert gets sent when the worker fails to run or breaks. Please see [How to send kicks to WDT.io programmatically](programmatic_kicks.html) for details on how to do this. The only thing specific to IronWorker is that the inbound timer should be named after the project and task. So if the project's name is "homework" and the worker's name "math-worker", we'd name the timer **homework/math-worker**. And we set the schedule of the timer to match the one of the worker.

### IronCache

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. Create a new outbound timer.
3. Name the timer after the project and cache.
4. Use the REST endpoint for getting info about your cache as the URL of the new timer.
5. Select GET.
6. Select every 1 minute as the schedule.
7. Save the new timer.

For example, if the project name on iron.io is "homework" and the name of the cache is "submitted_cache", the endpoint URL will look something like **https://cache-aws-us-east-1.iron.io/1/projects/123/caches/submitted_cache?oauth=4a5b6c**. So we can name the outbound watchdog timer **homework/submitted_cache**.

Now when the cache becomes unavailable, WDT.io will send an alert.

### More info

- [iron.io](https://iron.io)
- [docs](http://dev.iron.io)
