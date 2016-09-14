{
  "title": "How to monitor unexpected DNS changes",
  "shortTitle": "Monitoring DNS changes",
  "date": "2016-09-14"
}
---
DNS is a necessary pillar for functional domain name resolution from IP address to domain names.
Unexpected changes to your domain name records can lead to unexpected and often negative results.
Periodically verifying these records manually can be a long and tedious task.

With WDT.io, you can setup simple monitors for changes to DNS and receive alerts when the response is not expected.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Extend the job to send a kick to the URL copied from step 3

### Example

We want to monitor changes to our A record and validity in the response.
Let us manually verify the output for:
```bash
host -t A example.com
```

Satisfied with the response, let us obtain the md5sum of the response
```bash
host -t A example.com | md5sum | awk '{print $1}'
```
In this case the response is **5f215a90f67cc08f3e40e832728cdea7**

Let us now setup a cron job to verify this output periodically.

```bash
*/30 * * * *    [[ `host -t A example.com | md5sum | awk '{print $1}'` == "5f215a90f67cc08f3e40e832728cdea7" ]]
```

Every 30 minutes, verify the A record for example.com, reduce the response to md5sum and compare this to the expected response.

So we create a new inbound timer **DNS-A**, set the schedule to **every 30 minutes** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/DNS-A**.  With that, we edit the crontab using `crontab -e` and change our entry to:



```bash
*/30 * * * *    [[ `host -t A example.com | md5sum | awk '{print $1}'` == "5f215a90f67cc08f3e40e832728cdea7" ]] && curl -sm 30 k.wdt.io/123abc/DNS-A
```

Now we will be notified when the A record changes or in the catastrophic circumstance that there is no response (DNS is down or unglued).

We can do similar for record type MX , the root command becomes `host -t MX example.com`
Or any other type of record supported by the host command

### More info

- [host man page](http://linux.die.net/man/1/host)
