{
  "title": "Create a new Inbound Timer",
  "shortTitle": "Inbound",
  "date": "2015-09-30"
}
---
Inbound kicks allow you to receive notification when you application is not functioning normally.  When an expected kick is not received, you get notified.  Great for monitoring cron jobs and scheduled tasks.

1. Provide a name for the timer. Forward slashes can be used to provide grouping, for example *production/arangodb*
2. Provide a description. The description will be included in alert emails.
3. Select a schedule, every *1* minute for example.
4. Select a tolerance of *1* minute.
5. Provide an alert email, this is where alerts will be sent.
6. Save the new timer.

Now our new inbound kick is ready to use.


### More info

- [WDT.io](https://wdt.io)
