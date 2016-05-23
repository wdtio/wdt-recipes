{
  "title": "How to monitor the Postmark status API",
  "shortTitle": "Monitoring Postmark status API",
  "date": "2016-05-20"
}
---
[Postmark](https://postmarkapp.com) is a transactional-email service provider and has a new API for querying their current status. Let's use WDT.io to get alerted in case the API reports any problem. The endpoint we're interested in is at `https://status.postmarkapp.com/api/1.0/status` which returns a response in JSON format that looks like this:

```JavaScript
{"status":"UP","lastCheckDate":"2016-05-20T20:30:29Z"}
```

We can create a cronjob that queries the api on a regular basis. Then we parse the JSON and test that the value for "status" is "UP". When it's "UP", we kick an inbound watchdog timer on WDT.io to prevent it from alerting us.

An easy way to parse JSON from the shell is jq. `jq -r .status` returns the value for "status" in raw format so that we can easily test it. Putting it together, this will return either UP, MAINTENANCE, DEGRADED, DELAY, or DOWN:

```bash
curl -s https://status.postmarkapp.com/api/1.0/status | jq -r .status
```

Let's put it all together:

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "postmark" with a schedule of every 2 minutes +0.5 minute tolerance, and a comment "monitor status of https://status.postmarkapp.com"
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
*/2 * * * * [ `curl -s https://status.postmarkapp.com/api/1.0/status | jq -r .status` = UP ] && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the status is not UP and can possibly delay sending of any transactional email.



### More info

- [Postmark status API announcement](https://postmarkapp.com/blog/getting-creative-with-postmarks-status-api)
- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
- [jq](https://stedolan.github.io/jq/)
- [json](http://trentm.com/json/) (jq alternative)
- [jsawk](https://github.com/micha/jsawk) (jq alternative)
