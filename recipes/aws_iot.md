{
  "title": "How to build a Dead Man's Switch with the AWS IoT Button",
  "shortTitle": "AWS IoT",
  "date": "2016-12-10"
}
---
A dead man's switch is a device that needs to be used continuously or regularly to prevent an action from happening. The traditional example is a foot pedal in a train that the driver needs to push down. Should the driver become incapacitated (heart attack, for example), the foot pedal is likely released causing the train to automatically stop (the action).

The AWS Button is a very simple IoT device that has just one physical button. When the button is pressed, the device sends a signal to an AWS web service to do whatever it is programmed it to do.

We can build our own dead man's switch by having the AWS Button invoke a simple AWS Lambda function which kicks a watchdog timer on WDT.io.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already
2. [Create](inbound_timer.html) a new inbound timer
3. Copy the URL of this new timer
4. Register and set up your AWS Button
5. Implement a Lambda function that makes an HTTP request to the URL copied from step 3

Now if the button isn't pressed within the schedule of the watchdog timer, you'll get an alert.

### Example

Your house's furnace has an air filter that needs to be changed once a month. So you put the AWS Button on the furnace and every time you change the filter you also push the button. The corresponding watchdog timer is configured with a schedule of every-1-month and it'll send you an alert if the button isn't pushed within that time period.

We want to implement our Lambda function in JavaScript.

So we start by creating an inbound watchdog timer and call it "furnace-filter". We set the schedule to every-1-month and give it a tolerance of two days. The URL for this new timer will look something like **k.wdt.io/123abc/furnace-filter**.

Then we follow the instructions on [Getting Started with the AWS IoT Button](https://aws.amazon.com/iotbutton/getting-started/) using the Configuration Wizard. After generating certificate and keys, and connecting the AWS Button to our wifi network, we set up the Lambda function.

Name the function also "furnace-filter", select the Node.js runtime, and enter the following code:

```JavaScript
const http = require('http');

exports.handler = (event, context, callback) => {
    http.get('http://k.wdt.io/123abc/furnace-filter', res => callback(null, 'kicked WDT.io'));
};
```

Make sure to use the actual URL from the watchdog timer in your code. Set the timeout to 30s and select the existing role `lambda_basic_execution`. The other defaults are fine. Then create the function.

You should see a blue Test button. Push it and about 15s later you should see on WDT.io that a kick has been received for your furnace-filter watchdog timer. If that worked, you can enable the AWS IoT trigger. Now push the physical button on the AWS Button and again, 15s later, you should see a new kick in WDT.io.

Now if you wait more than one month plus two days to change the filter, you'll get an alert from WDT.io as a reminder.


### More info

- [Dead Man's Switch on Wikipedia](https://en.wikipedia.org/wiki/Dead_man's_switch)
- [AWS IoT Button](https://aws.amazon.com/iotbutton/)
