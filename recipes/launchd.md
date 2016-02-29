{
  "title": "How to monitor launchd jobs",
  "shortTitle": "Monitoring launchd",
  "date": "2016-02-29"
}
---
In OS X, you can run a background job on a timed schedule using launchd ("Launch Daemon"). We can use WDT.io to be notified when a scheduled job failed.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. Extend the job to send a kick to the URL copied from step 3.

Now every time launchd runs your job, it'll also send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the job fails, WDT.io won't get the kick and will send an alert.


### Example

We have the following hourly launchd agent:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>org.you.something</string>
    <key>Program</key>
    <string>something.sh</string>
    <key>StartInterval</key>
    <integer>3600</integer>
  </dict>
</plist>
```
Every hour the something.sh script is executed. It typically finishes in a couple of second.

So we name our new inbound timer **something**, set the schedule to **every 1 hour** and the precision to **0.5 minutes**. The URL for this new timer will look similar to **k.wdt.io/123abc/something**. With that, we edit the agent and change it to:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>org.you.something</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/sh</string>
        <string>-c</string>
        <string>something.sh && curl -sm 30 k.wdt.io/123abc/something</string>
    </array>
    <key>StartInterval</key>
    <integer>3600</integer>
  </dict>
</plist>
```
Now, after reloading the agent using launchctl, we'll be notified when the job fails and can fix whatever caused the problem.

### More info

- [Apple's launchd documentation](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLaunchdJobs.html)
- [launchd.info](http://launchd.info)
