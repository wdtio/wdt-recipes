{
  "title": "How to monitor unexpected DNS changes",
  "shortTitle": "Monitoring DNS changes",
  "date": "2016-09-14"
}
---
DNS is a necessary pillar for functional domain name resolution from IP addresses to domain names.
Unexpected changes to your domain name records can lead to surprising and often negative results.
Periodically verifying these records manually can be a long and tedious task.

So let's automate this verification by setting up a cron job which periodically invokes the `host` command to fetch DNS records and compare them to an expected result. Then we set up an inbound watchdog timer to monitor this cron job and alert us if the records do not match the expected response.


### Example

We want to monitor changes to our A record and validity in the response.
Let us manually verify the output for:
```bash
host -t A example.com
```

Satisfied with the response, let us obtain the md5sum of the response.
```bash
host -t A example.com | md5sum | awk '{print $1}'
```
In this case the response is **5f215a90f67cc08f3e40e832728cdea7**.

Let us now setup a cron job to verify this output periodically.

```bash
*/30 * * * *    [[ `host -t A example.com | md5sum | awk '{print $1}'` == "5f215a90f67cc08f3e40e832728cdea7" ]]
```

Every 30 minutes, verify the A record for example.com, reduce the response with `md5sum` and compare this to the expected response.

So we [create a new inbound watchdog timer](inbound_timer.html) **DNS-A**, set the schedule to **every 30 minutes** and the precision to **2 minutes**.  The URL for this new timer will look something like **k.wdt.io/123abc/DNS-A**.  With that, we edit the crontab using `crontab -e` and change our entry to:

```bash
*/30 * * * *    [[ `host -t A example.com | md5sum | awk '{print $1}'` == "5f215a90f67cc08f3e40e832728cdea7" ]] && curl -sm 30 k.wdt.io/123abc/DNS-A
```

Now we will be notified when the A record changes or in the catastrophic circumstance that there is no response (DNS is down or unglued). We will also be alerted if the cron job itself fails to run.

The watchdog timer can be further improved by pasting the expected output of the `host` command into the description so that in case of an alert, we can easily compare the current output to what we expect.

We can monitor other types of record supported by the host command. For example using `host -t MX example.com` for MX records.

### More info

- [host man page](http://linux.die.net/man/1/host)
