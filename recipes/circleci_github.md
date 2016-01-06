{
  "title": "How to monitor CircleCI",
  "shortTitle": "circleci",
  "date": "2016-01-06"
}
---
CircleCI is a continuous integration service. It integrates with Github such that every time you push a change to Github, CircleCI builds your latest code and runs your tests, notifying you if your build or tests fail.

If your build and tests succeed, CircleCI does not notify you (unless last time it failed). Now if for some reason this integration fails, you will not find out about it until you realize that you pushed something that should have failed but didn't receive any notification.

We can use WDT.io to monitor this integration by setting up an on-demand timer which receives it's first kick by a post-push hook from git or Github, and which receives the second kick by a CircleCI webhook.

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](ondemand_timer.html) a new on-demand timer.
3. Copy the Start-URL of this new timer.
4. Create or edit a post-push script which kicks the Start-URL.
3. Copy the Stop-URL of this new timer.
4. Create or edit a post-build script which kicks the Stop-URL.

Now every time you push a code change, it'll also send a kick to WDT.io starting the new timers. And when the build (and tests) complete, the second kick is sent ending the timer. If, for whatever reason the second kick doesn't arrive, WDT.io will send you an alert.


### Example

You have a project named "awesome_project". You're using Github and your project is at github.com/best_coder/awesome_project. You have CircleCI set up such that it builds awesome_project and runs the tests after every push to Github. CircleCI typically takes two minutes to complete the build and tests.

So we create a new on-demand timer named **awesome_project/circleci**, with **https://github.com/best_coder/awesome_project** as description, and set the max-time to **5 minutes**. After saving, we get

- Start URL: **k.wdt.io/abc123/awesome_project/circleci?1**
- Stop URL: **k.wdt.io/abc123/awesome_project/circleci?2**

Next we go to the Project Settings on Github, click on "Webhooks & services", and then click on "Add webhook". **http://k.wdt.io/abc123/awesome_project/circleci?1** becomes our "Payload URL", the "Content type" doesn't matter, and "Secret" should be blank. We want "Just the push event" and "Active".

In our project we create or edit **circle.yml** and add the following lines:

```
notify:
  webhooks:
    - url: k.wdt.io/abc123/awesome_project/circleci?2
```

`git add circle.yml` if necessary, and then commit and push it to Github. This will trigger the next build and you can observer on WDT.io how the first kick starts our new tiimer and the second kick stops it.


### More info

- [CircleCI notifications](https://circleci.com/docs/configuration#notify)
- [Github webhooks](https://developer.github.com/webhooks/)
