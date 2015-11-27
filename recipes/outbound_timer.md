{
  "title": "Create a new Outbound Timer",
  "shortTitle": "Outbound",
  "date": "2015-11-27"
}
---
Outbound kicks allow you to get notified when your web-application is not reachable. When an expected outbound kick does not reach it's target, you get notified. Great for monitoring web sites and other publicly facing web resources.

1. Provide a name for the timer. Forward slashes can be used to provide grouping, for example *production/database*
2. Provide a description. The description will be included in alert emails.
3. Provide a URL.  This is the URL that will be kicked by our watchdogs.
4. Select a schedule, every *5* minutes for example.
4. Select a tolerance to account for normal variations.
5. Provide an alert email, this is where alerts will be sent.
6. Save the new timer.

Now your new outbound timer is ready to use.


### More info

- [WDT.io](https://wdt.io)
