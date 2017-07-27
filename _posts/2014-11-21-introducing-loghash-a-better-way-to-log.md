---
layout: post
title: Introducing LogHash, a better way to log
slug: introducing-loghash-a-better-way-to-log
date: 2014-11-21 10:46:30Z
---

Every developer I know, on any language or platform, at some time generate logs. Some of them are highly structured, some of them are only plain text. They can serve as a debugging tool or be [at the heart of complex infrastructures][heart-logs-review]. Today, I'll talk about **How we can make a better use of plain text logs**.

### "Logs hell"
When all your team use logs, they'll inevitably add-op and becoming more and more complex to handle, search or process. Most of us ends-up using `Ctrl/Cmd+F` to find the log line they've just added.

Few months later, while tracking down a bug or working on old code, you will inherit of all these message logs, in your Visual Studio Output, your Adb log, your NSLogger or in some table storage/ElasticSearch/Kibana,etc... It will take you time to make sense from them. You will manage that, of course, after some time.

All these "technical/debug logs" ends up being read only by developers, and some adventurous IT staff. Your marketing/growth hacking department ask you for some stats, funnels and cohorts? No problem! Add a MixPanel/kissmetrics/flurry [place SAAS analytics platform here] tag and basta. Your design team asks for user tracking feature? Go add Optimizely library. You - the dev team - want to track bugs on your Android app? There's Crittercism for that...

But, how many level of infos needed by all these solutions are already here, just in the middle of all your technical logs?

### The need of a "Markdown for logs"

For being able to extract all these infos from your logs, you need to structure them. You need **semantic logs**! If you take a look at the [Semantic Logging Application Block][slab-codeplex] from Microsoft P&P (1), you'll see the *real* cost of semantic logging: **For now, semantic logging means One class by log information**. It's just a hell.

What we need is a semantic solution that:
- Works on any platform, any language, any app type,
- Does not requires any had changes in your log pipeline,
- Can be so simple that the only thing required to get started is few keystrokes,
- Can be adopted by one developer in a team, and then spread out.

Today, I'm releasing the first version of the **LogHash** markup language specifications, **A markdown-like markup language for your logs**.

You can see them on the [LogHash GitHub repository][gh-loghash-spec]. Feel free to contribute, or create an issue if you have any question/remark.

I'm working on and testing this markup language for more than 6 months now, trying to battle-test it on the Deezer mobile apps. I've already gained so much time while developing or debugging features. It's time to see if it can be useful for other people too ;)

### What's next?
**TOOLS!**. To get the maximum out of this markup language, you need a parser, and then, you need a tool to work on it. I've already prototyped two parsers (one in C#, and one in JavaScript). I've also created a little Visual Studio addin that replaces the VS Output, with search and filtering capabilites.

The very next step is to release a web-based tool to lets you explore your own LogHash logs. You should follow me on [twitter][@cmaneu] to be among the first to test it ;).

I'll also rework my Visual Studio Addin from scratch (the first try is never a good one, especially when you're working on VS/Office COM Addins :).

Meantime, you can try to *loghash your logs* and tell me what you think.



(1): I've been a reviewer of the [Enterprise Library 6][entlib6], where SLAB was
introduced.

[heart-logs-review]: http://book.maneu.net/I-Heart-Logs/
[gh-loghash-spec]: https://github.com/cmaneu/loghash-spec
[slab-codeplex]: https://slab.codeplex.com/
[entlib6]: http://msdn.microsoft.com/en-us/library/dn169621.aspx
[@cmaneu]: http://twitter.com/cmaneu

