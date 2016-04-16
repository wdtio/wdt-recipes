{
  "title": "Create a new Inbound Timer",
  "shortTitle": "Inbound",
  "date": "2015-10-06"
}
---
Inbound kicks allow you to get notified when your application is not functioning normally. When an expected kick is not received, you get notified. Great for monitoring cron jobs and scheduled tasks.

1. Provide a name for the timer; forward slashes can be used to provide grouping, for example *production/database*
2. Provide a description; it will be included in alert emails
3. Select a schedule, every *1* minute for example
4. Select a tolerance to account for normal variations
5. Provide an alert email, this is where alerts will be sent
6. Save the new timer

Now your new inbound timer is ready to use.


### More info

- [WDT.io](https://wdt.io)
