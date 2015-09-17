{
  "title": "How to monitor a webserver",
  "shortTitle": "webserver",
  "date": "2015-06-27"
}
---
Apache and nginx are two popular webservers. We can use WDT to be notified when they stop responding to HTTP requests.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. Create a new outbound timer.
3. Name the timer after the webserver.
4. Enter the URL of your webserver including the protocol (http or https).
5. Select a short schedule to be notified of problems quickly.
6. Save the new timer.

Now WDT will send a regular kick to your webserver (an HTTP HEAD request to keep the load low). If the webserver fails to respond, WDT will send an alert.


### Example

We are the webmaster of example.io. We set up an outbound timer named **example.io**, enter **http://example.io** as URL and pick **every 1 minute** as schedule.

Now we'll be notified when example.io is offline and can fix whatever caused the problem (maybe our DNS provider is down).
