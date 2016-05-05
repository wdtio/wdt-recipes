{
  "title": "How to monitor a webserver",
  "shortTitle": "Monitoring web servers",
  "date": "2015-06-27"
}
---
Apache and nginx are two popular webservers. We can use WDT.io to be notified when they stop responding to HTTP requests.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. Create a new outbound timer
3. Name the timer after the webserver
4. Enter the URL of your webserver including the protocol (http or https)
5. Select a short schedule to be notified of problems quickly
6. Save the new timer

Now WDT.io will send a regular kick to your webserver (an HTTP HEAD request to keep the load low). If the webserver fails to respond, WDT.io will send an alert.

If your webserver does not support HEAD requests, you can change it to a GET request. Also, if you like you can monitor specific pages by entering the full URL for that page.

### Example

We are the webmaster of qq.com. We set up an outbound timer named **qq**, enter **http://qq.com** as URL and pick **every 1 minute** as schedule.

<video width="420" src="https://s3-us-west-2.amazonaws.com/docs.wdt.io/website-monitor.mp4" controls muted></video>

Now we'll be notified when qq.com is offline and can fix whatever caused the problem (maybe our DNS provider is down).
