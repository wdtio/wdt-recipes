{
  "title": "How to monitor Zabbix frontend",
  "shortTitle": "zabbix-web",
  "date": "2015-11-27"
}
---
Zabbix is an enterprise-class open source monitoring solution for network and application monitoring.  It provides a web-frontend that displays a wealth of information.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](outbound_timer.html) a new outbound timer
3. In the URL field, paste the URL of your zabbix frontend, specify a frequency and alert email.

Now, WDT will send an outbound kick and dispatch an alert when the Zabbix-frontend is unreachable.

### Example

We have the following zabbix frontend URL: ```http://example.com/zabbix```.  As with most monitoring systems, the availability of this frontend is imperative.

So we create a new outbound timer **zabbix/frontend**, specify the zabbix frontend URL, set the schedule to **every 5 minutes** and specify an alert email.

Now we'll be notified when the zabbix frontend is unavailable

### More info

- [zabbix home page](http://zabbix.com)
