---
layout: post
title: Powershell to slack
slug: powershell-to-slack
date: 2014-08-26 16:22:21Z
---

Since few months, I'm using **[Slack](https://slack.com/r/02b6plp2-02gq2j5c)** within my team. It's a *chat on steroids for teams*. One of the neat feature of Slack is the Integration with online tools like GitHub, Crashlytics, Jenkins, Pingdom, Stripe and more. As I'm building a lot of little internals tools, I've a bunch of powershell scripts to run automated actions. So, it could be nice to notify a Slack room directly from a Powershell script (like a *psake* build script) in case of success. And it's really simple with `Invoke-RestMethod` commandlet.

```php
$notificationPayload = @{ 
    text=":tada: New message from Powershell"; 
    username="Powershell"; 
    icon_url="http://thespinningmule.com/wp-content/uploads/2014/08/metro-powershell-logo.png"
} 

Invoke-RestMethod -Uri "https://deezer.slack.com/services/hooks/incoming-webhook?token=yourToken" -Method Post -Body (ConvertTo-Json $notificationPayload)
```

And it's all it takes ! 

![Slack powershell](/content/images/2014/Aug/slack_powershell.PNG)
