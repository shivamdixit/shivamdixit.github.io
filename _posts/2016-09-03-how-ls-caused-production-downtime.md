---
layout: post
title: "How 'ls' command output caused a downtime on prod"
description: "The innocuous command 'ls' can sometimes be dangerous too. Read how."
category: linux
tags: [bash, shell, linux]
imagefeature: cover17.jpg
comments: true
share: true
---

**ls** is one of the innocuous commands that sysadmins probably use hundreds of times a day. But yesterday, we faced downtime on production because of this command. Here's the full story...

One of our production app server was behaving funny yesterday, because of which an Ops Engineer was doing routine checks on the server. He ran the beloved `ll` command (which is aliased to `ls -lah`). The output of the command was something like:

![Output]({{ site.url }}/images/ls-post/output.png)

As you can see, all the symlinks and files were neatly listed. Nothing unusual, right? The Engineer copied the output and pasted it over to someone on Slack.

After few minutes, the original issue was identified and fixed. Soon after, the home page started responding randomly with a 200 OK **blank** response! *WTF!*

It took us a while to identify that `index.php` file (the front controller) was empty on one of the app servers. Since the servers are behind load balancers, requests which were hitting this server were getting the blank response. We quickly deployed the latest file and everything was back to normal.

Though the issue was resolved, it made us thinking---who truncated the contents of `index.php` file? Is it the buggy deployment script? Or someone compromised our servers? Or it was a _paranormal_ activity?

Thankfully, the answer is none of the above. Let's go back to our `ls -lah` command output:

![Output]({{ site.url }}/images/ls-post/output.png)

Did you spot something strange? Notice the numbers before every line. Yes, this is a screenshot of `history` command. When I was going through the `history`, at first glance I just ignored it as command and its output, but then I quickly came to senses and realised that history doesn’t show the command output. It was the output of the command which was being executed as a command. Let’s recap…

The Engineer's investigation was going well until he mistakenly pasted the clipboard contents (which had ll's output) on the shell. Since it had line-breaks, he couldn't do much to stop it. The output of command execution was:

{% highlight bash linenos %}

[root@app04]lrwxrwxrwx 1 nobody nobody 70 Apr 12 13:22 index.php -> /master/current/index.php
bash: lrwxrwxrwx: command not found

{% endhighlight %}

Aha! `bash: lrwxrwxrwx: command not found` --- what a relief to see that command didn't execute. _Nothing went wrong._

Well, at some point in time, we all have pasted something weird in our shells. Nothing happens usually because the output almost never makes a valid command!

Though the command didn't execute, _"command not found"_ error was printed on the `stderr` and `stdout` was empty. The symlink sign `->` in the output has a hidden redirection operator `>`, which caused the output of `stdout` (which is empty) to overwrite the contents of index.php file.

After this, we all had a good laugh.

**Lessons learned---be respectful to shell and never take it for granted**

Put a comment if you have got ideas on how to avoid this from happening in future?
