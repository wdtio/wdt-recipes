{
  "title": "How to monitor IP addresses",
  "shortTitle": "Monitoring IP addresses",
  "date": "2016-05-16"
}
---
The IP address provided by home internet providers is often semi-static. In other words, the IP address is not guaranteed to stay the same, but in practice it doesn't change often. In that case, many people set up a DNS A-records to make their home computer reachable by name. We can monitor the IP address to be notified whenever it changes, so that we can update the A-record.

A simple way to find out the current IP address is by using [ipify.org](https://www.ipify.org).

```bash
curl https://api.ipify.org
```

Let's say say the IP address is currently 12.23.34.45 and we want to monitor it every hour using a cronjob.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer "ip-address" with a schedule of every 1 hour +1 minute tolerance, and a comment "update A-record and cronjob with new IP address"
3. Copy the URL of this new timer
4. Edit the crontab and add this line:

```bash
0 * * * * [ `curl -s https://api.ipify.org` == 12.23.34.45 ] && curl -sm 30 <the URL from step 3>
```

Now we'll be notified when the IP address changed and can update the A-record at our DNS provider. Also, we'll need to update the cronjob to use the latest IP address.

A more advanced way is to compare the IP address to the value of the A-record using a DNS lookup utility such as dig. That way, if the IP address changes, we only need to update the A-record but can leave the cronjob as is.

```bash
dig +short myhomeserver.com
```

```bash
0 * * * * [ `curl -s https://api.ipify.org` == `dig +short myhomeserver.com` ] && curl -sm 30 <the URL from step 3>
```



### More info

- [man page for cron](http://linux.die.net/man/5/crontab)
- [man page for curl](http://linux.die.net/man/1/curl)
- [man page for dig](http://linux.die.net/man/1/dig)
