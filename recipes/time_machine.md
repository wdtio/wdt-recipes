{
  "title": "How to monitor Time Machine",
  "shortTitle": "time machine",
  "date": "2016-01-03"
}
---

Time Machine is the built-in backup system of Mac OS X. By default, it runs once every hour. It may stop working if it can't find the designated backup disk, or if that disk is out of space and the deletion of older backups doesn't free up the required amount of disk space. 

We can use WDT.io to monitor Time Machine so that we get notified when it failed to backup. Since Time Machine stops while the computer is off, this recipe is more suitable for always-on computers.

The basic idea is that we create a script that checks that the latest backup is not older than an hour. If the result is positive, the script kicks an inbound timer on WDT.io. This timer is configured to expect a kick every hour.  When the inbound timer doesn't get kicked on time, it'll alert.

Keep on reading for a full explanation of how we can accomplish this monitoring, or skip to the end for the final solution.


### tmutil

Mac OS X comes with a built-in command line util `tmutil` that can be used to access and control Time Machine. We're specifically interested in `tmutil latestbackup` which returns the full path of the latest backup. The output looks something like

```
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-144156
```

Depending on how Time Machine is set up on your computer, you may get a permission error from `tmutil` and have to use `sudo tmutil latestbackup` instead.

The name of the backup is the date and time of when the backup was done. So let's get this path and put it in a variable

```
LAST_PATH=$(tmutil latestbackup)
```


### date

We can use the `date` command to parse the date-timestamp of that backup, but first we'll have to separate the date-timestamp from the path. One way to do that separation is with `basename`.

```
DATE_TIMESTAMP=$(basename "$LAST_PATH")
```

Now we're able to parse the date-timestamp into an actual date. Recall that our timestamp value looks something like `2016-01-03-144156` which is Year-Month-Day-HourMinuteSecond. This format can be parsed with `date -j -f "%Y-%m-%d-%H%M%S"`. The `-j` parameter prevents `date` from changing the current date on your computer.

There is no functionality built into `date` for getting the age of a date which is what we're really interested in (to test if the latest backup is no older than an hour). But there is a way to get the number of seconds since epoch (January 1st, 1970) with `date "+%s"`. So we'll get the number of seconds for our date-timestamp, and also the number of seconds for now, then we find the different between these two numbers which gives us the age of the date-timestamp in seconds.

```
DATE_TIMESTAMP_SECONDS=$(date -j -f "%Y-%m-%d-%H%M%S" $DATE_TIMESTAMP "+%s")
CURRENT_SECONDS=$(date "+%s")
```


### curl

For one hour we have 60 seconds times 60 minutes wich makes 3600, so if our age is not greater than that, we'll know that the latest backup is recent and assume Time Machine is still working and send a kick to WDT.io. The `curl` command is convenient for sending kicks.

```
(($CURRENT_SECONDS - $DATE_TIMESTAMP_SECONDS < 3600)) && curl -Im 30 http://k.wdt.io/your/timer
```


### Full script

```
LAST_PATH=$(tmutil latestbackup)
DATE_TIMESTAMP=$(basename "$LAST_PATH")
DATE_TIMESTAMP_SECONDS=$(date -j -f "%Y-%m-%d-%H%M%S" $DATE_TIMESTAMP "+%s")
CURRENT_SECONDS=$(date "+%s")
(($CURRENT_SECONDS - $DATE_TIMESTAMP_SECONDS < 3600)) && curl -Im 30 http://k.wdt.io/your/timer
```

Putting it all together into a one-liner.

```
(($(date "+%s") - $(basename "$(tmutil latestbackup)" | xargs date -j -f "%Y-%m-%d-%H%M%S" "+%s") < 3600)) && curl -Im 30 http://k.wdt.io/your/timer
```

We intend to use this line in our crontab where the percentage character has a special meaning, so we need to escape ours.

```
(($(date "+\%s") - $(basename "$(tmutil latestbackup)" | xargs date -j -f "\%Y-\%m-\%d-\%H\%M\%S" "+\%s") < 3600)) && curl -Im 30 http://k.wdt.io/your/timer
```


### About that one hour

To be precise, Time Machine doesn't make a backup every hour. It waits for an hour between backups. This means that backups are spaced by an hour plus the time it took to make a backup. On my machine the output of `tmutil listbackups` looks like this:

```
...
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-174604
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-184725
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-194841
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-204958
/Volumes/seagate4tb/Backups.backupdb/mini2/2016-01-03-215123
```

To account for this extra time, we'll allow backups to take up to 30 minutes. With that, we allow the last backup to be up to 90 minutes old (5400 instead of 3600 seconds).


### Setting everything up

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer "TimeMachine", every 1 hour, tolerance of 4 minutes.
3. Copy the URL of this new timer.
4. Use `crontab -e` to add a new cronjob (if you needed `sudo` to run `tmutil` above, use `sudo crontab -e` to create a cronjob for root):

```
0 * * * * (($(date "+\%s") - $(basename "$(tmutil latestbackup)" | xargs date -j -f "\%Y-\%m-\%d-\%H\%M\%S" "+\%s") < 5400)) && curl -Im 30 http://<the URL from step 3>
```

This cronjob will run every hour and send a kick to your timer at WDT.io as long as the latest backup is recent enough. If it's not, there will be no kick and WDT.io will send you an alert so you can investigate why Time Machine stopped backing up for you.


### More info

- [Time Machine](https://support.apple.com/en-ca/HT201250)
- [tmutil](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/tmutil.8.html)
- [basename](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/basename.1.html)
- [date](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/date.1.html)
- [curl](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/curl.1.html)
- [crontab](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/crontab.1.html#//apple_ref/doc/man/1/crontab)
