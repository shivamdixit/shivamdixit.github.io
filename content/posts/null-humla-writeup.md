---
title: "Null HUMLA Write-up"
date: 2016-05-02
description: "Write-up of Delhi null HUMLA workshop on android security and penetration testing."
category: [security]
slug: security
tags: [writeup, nullcon, OWASP, null, HUMLA]
draft: false
---

Last week I got a chance to attend my first Null HUMLA workshop. People who are not familiar with Null, it is a community of security enthusiasts who are willing to share knowledge and trying to make the world a more secure place. In this post, I'll share my experience with the workshop.

### Introduction

Null community organizes three different kinds of events:

* **Humla** (for offensive security)
* **Bachaav** (for defensive security)
* **Pulia** (for bridging the gap between two)

The workshop was organized at Sapient, Gurgaon, which is located in the heart of the city. There was a long queue of hackers at the reception desk, all looking excited and ready to break the ice.

"_Android Fight Club_"---as the name suggests, it was a workshop on android security and penetration testing. I have a background in web application security, but Android was something totally new for me. We were asked to pre-install some of the tools and VMs for the hands-on session.

<img src="{{ site.url }}/images/null-humla-post/img3.JPG" alt="House full" width="750">

The session was a house full with ~45 participants. It started with the introductions by the attendees. Everyone was asked to tell a _cool thing_ about themselves. Abhinav (speaker) was charismatic and he kept us engaged throughout the session with his witty sense of humor and funny slides.

<img src="{{ site.url }}/images/null-humla-post/img2.png" alt="Tea Break" width="750">

### The Workshop

Abhinav explained us about the architecture, privileges & permissions, and other high-level details of android applications.

<img src="{{ site.url }}/images/null-humla-post/img1.png" alt="Architecture of Android" width="750">

> Because _why_ is more important than _how_ - Abhinav

We were given insights of android market share and why android security is essential in today's world. The rest of the workshop was divided into two major parts:

* Static analysis
* Dynamic analysis

The hacking started with the static analysis of android applications. We tried tools like apktool, adb, dex2jar, etc.,  read SMALI files, discussed binary protection and other static analysis tools available in the market.

<img src="{{ site.url }}/images/null-humla-post/img4.JPG" alt="Abhinav" width="750">

It was followed by dynamic analysis, and hands-on with tools like Drozer, burp suite, etc. We tried to find bugs in a vulnerable Goat Droid app by OWASP. We exploited various components like:

* Exported Services
* Exported Broadcast receivers
* Exported Activities
* Exported Content Provider

#### Key Takeaways

Some of the key takeaways from the workshop were:

* Android directory structure and permission model
* Knowledge of various static and dynamic analysis tools for pen-testing
* Various common exploitable vulnerabilities
* Emulators and VMs
* How to start earning money Gangsta style (bug-bounty)

Overall it was a fun and informative session. I met lots of security enthusiast, discussed the latest trends in security and made new friends. After all, the best part of any meet-up is the amazing people in it.

### Organizers

The event was very well organized, thanks to [Vaibhav](https://twitter.com/VaibhavGupta_1), [Vishal](https://www.linkedin.com/in/vishalasthana), and [Sandeep](https://www.owasp.org/index.php/User:Sandeep_Singh) for taking the initiatives of organizing excellent security meet-ups and workshops in Delhi/NCR region.

Looking forward to seeing you all at the next event!
