{
  "title": "How to monitor ArangoDB",
  "shortTitle": "ArangoDB",
  "date": "2015-09-24"
}
---
ArangoDB is a multi-model NoSQL database. We can use WDT.io to be notified when it stops.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. Create a new inbound timer.
   1. Provide a name for the timer. Forward slashes can be used to provide grouping, for example *production/arangodb*
   2. Provide a description. The description will be included in alert emails.
   3. Select a schedule, every *1* minute for example.
   4. Select a tolerance of *1* minute.
   5. Provide an alert email, this is where alerts will be sent.
   6. Save the new timer.
3. Copy the URL of this new timer.
4. Add the foxx-heartbeat app to ArangoDB and configure it with the timer URL.

```bash
foxx-manager update
foxx-manager install foxx-heartbeat /heartbeat url='http://<timer URL>' interval=60
```

Now ArangoDB will send a kick (heartbeat) to WDT.io about every 60 seconds. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the heartbeat stops, WDT.io won't get the kick and will send an alert.

### More info

- [ArangoDB](https://www.arangodb.com)
- [foxx-heartbeat](https://github.com/pekeler/foxx-heartbeat)
