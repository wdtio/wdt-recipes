{
  "title": "How to monitor ArangoDB",
  "shortTitle": "ArangoDB",
  "date": "2015-09-24"
}
---
ArangoDB is a multi-model NoSQL database. We can use WDT.io to be notified when it stops.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Add the foxx-heartbeat app to ArangoDB and configure it with the timer URL.

```bash
foxx-manager update
foxx-manager install foxx-heartbeat /heartbeat url='http://<timer URL>' interval=<interval in seconds>
```

Now ArangoDB will send a kick (heartbeat) to WDT.io about every 60 seconds. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the heartbeat stops, WDT.io won't get the kick and will send an alert.


### Example

We have an ArrangoDB server that we'd like to receive a ping from every minute.

We create a new inbound timer, **arangodb/ping**, set the schedule to **every 1 minute** and the precision to **1 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/arangodb/ping**. With this URL, we deploy the foxx application, foxx-heartbeat.

```bash
foxx-manager update
foxx-manager install foxx-heartbeat /heartbeat url='http://k.wdt.io/123abc/arangodb/ping' interval=60
```

Now we'll be notified when ArangoDB fails to send a heartbeat.

### More info

- [ArangoDB](https://www.arangodb.com)
- [foxx-heartbeat](https://github.com/pekeler/foxx-heartbeat)
