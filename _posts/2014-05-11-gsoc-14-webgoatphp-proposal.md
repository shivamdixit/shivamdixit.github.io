---
layout: post
title: "GSOC '14 WebGoatPHP Proposal"
description: "My Google Summer of Code 2014 proposal for the project - OWASP WebGoatPHP"
category: gsoc
tags: [GSOC, WebGoatPHP, proposal]
imagefeature: cover11.jpg
comments: true
share: true
---

<figure>
  <img src="{{ site.url }}/images/gsoc-proposal/gsoc.png" alt="Google Summer of Code">
  <figcaption>Google Summer Of Code</figcaption>
</figure>

### Abstract

WebGoat is one of the most popular open source web applications developed by OWASP. It is a deliberately insecure web application developed using Java to provide a security awareness environment. It offers a set of challenges based on various vulnerabilities listed in OWASP. Since PHP is extremely popular in web applications and has many of the security flaws which Java doesn't, my work is to develop a deliberately insecure PHP web application.

### The Project

WebGoatPHP is a port of WebGoat to PHP and MySQL/SQLite databases. The goal is to create an interactive teaching environment for web application security by offering lessons in the form of challenges. In each challenge the user must exploit the vulnerability to demonstrate their understanding. WebGoatPHP will contain all the standard features of WebGoat as well as support for four different modes i.e single mode, workshop mode, contest mode and secure coding mode. Different modes will make WebGoatPHP much more usable and will create an ideal learning environment for different group of users. A framework will be developed that will facilitate contributors in adding challenges.

![Overview]({{ site.url }}/images/gsoc-proposal/overview.png "Overview of WebGoatPHP")

#### Different Operating Modes

**Single-User Mode**

User can browse all the challenges that are available, can view hints and submit solutions. All the     submissions are evaluated by server side scripts. Each challenge is accompanied by a verbose explanation of the vulnerability and it's solution. This mode is suitable for individuals who want a hands-on experience with various security flaws.

![Single User Mode]({{ site.url }}/images/gsoc-proposal/1.png "Single User Mode")

<br />
**Workshop Mode**

In this mode, WebGoatPHP has a centralized control system. A lecturer is presented with an admin dashboard where he can manage accounts of all the participants that will connect to the server in workshop mode. He can control various options like challenge selection, allow hints, allow providing feedback by other users etc. A lecturer can monitor progress of all the students. This mode provides an ideal collaborative learning environment.

![Workshop Mode]({{ site.url }}/images/gsoc-proposal/2.png "Workshop Mode")

<br />
**Contest (CTF) Mode**

This mode is ideal for hosting CTF style contests. Admin of the contest can add/select challenges, assign points, control duration of the contests, open hints for participants etc. A contestant is required to sign-up before taking part in a contest. A real time leader-board is also maintained on the basis of score and time. A secure core framework like OWASP PHPSEC is required to strengthen security of the application and to ensure fair competition.

![Contest Mode]({{ site.url }}/images/gsoc-proposal/3.png "Contest Mode")

<br/>
**Secure Coding Mode**

In secure coding mode user has to patch the security vulnerabilities. For each challenge, the source code is presented to the user and he has to modify it in such a way that vulnerability no longer exists. The user is not required to change the source code of the application, instead only the relevant part of the source code is shown to the user in a client-side IDE.

![Secure Coding Mode]({{ site.url }}/images/gsoc-proposal/4.png "Secure Coding Mode")

<br/>
<br/>

#### Classification of challenges/lessons:

WebGoatPHP covers most of the attacks listed in OWASP Top 10 2013 along-with some other critical vulnerabilities. Some of the important categories of the challenges are:

1.**Injection attacks**

Injection attacks remain to be the most common web application attacks in OWASP top 10 since many years. Injection flaws occur when data is sent to an interpreter as part of a query without sanitizing it. Different scenarios of injection attacks like SQL injection, LDAP injection, Command injection, XPATH injection etc. are present in WebGoatPHP.

![SQL Injection]({{ site.url }}/images/gsoc-proposal/sqli.png "SQL Injection")

<br />
2.**Broken session management**

PHP's internal session management has many flaws. Insecure implementation of session management can compromise user's identity. Session hijacking, session fixation attacks are the most common scenarios.

![Session]({{ site.url }}/images/gsoc-proposal/session_hij.png "Session Hijacking")

<br />
3.**Brute force attacks**

A brute force attack can manifest itself in many different ways. The aim of challenges in this category is to highlight the importance of disallowing weak passwords in web application as well as to familiarize the user with various brute force tools like Hydra, Brutus, DirBuster etc.

![Brute Force Attacks]({{ site.url }}/images/gsoc-proposal/brute_force1.png "Brute Force Attacks")

<br />
4.**Cross Site Scripting (XSS)**

An attacker can execute malicious script in the victim’s browser through a XSS attack. Using a script an attacker can steal cookies, redirect the user to a phishing websites etc. Challenges for stored XSS attack as well as reflected XSS attack are present in WebGoatPHP.

![XSS]({{ site.url }}/images/gsoc-proposal/XSS.gif "Cross Site Scripting")

5.**Network Attacks**

Man In The Middle attack, DNS spoofing are the popular examples in this category. An example of a challenge in this category is: Login is over SSL connection but session is not, a   user is required to crack it.

![MITM]({{ site.url }}/images/gsoc-proposal/mitm.JPG "Man In The Middle attack")

Apart from above listed attacks, WebGoatPHP has an extensive list of various other scenarios like CSRF, DOS, Web Services, Error Handling etc.

<br/>

#### Use Cases

Some of the use cases of the project are:

John is a freelancer web developer. He uses WebGoatPHP in single-user mode and secure coding mode to learn about various vulnerabilities and to ensure that his applications are free from them.

Dr. Bansal is the professor of Information Security. He uses workshop mode to give a hands-on experience to his students about various web application attacks and to monitor their progress.

Ashish is the coordinator of cyber  security club of his college. He hosts various CTF competitions in his college using contest mode of the WebGoatPHP.

#### Implementation

Various features will be implemented as follows:

**Evaluation:** Different methods will be used for evaluating a user’s submission based on the type of challenge. Number of false positives/negatives will be significantly reduced by using advance algorithms instead of simple conditional statements to verify the user’s input. Various methods like regular expressions, executing the user’s input, parsing the response etc. will be used. For example, to validate input for a SQL injection attack in a login form, the input will be concatenated with the query and will be executed. If the number of rows in the result returned are greater than 1, then it will be a successful SQL injection attack.

**Core Framework:** WebGoatPHP will be developed using OWASP PHPSEC framework/jframework because it supports best security practices, NIST 2 Role Based Access Control, optimized database drivers, MVC best practices etc. For deploying the application in a live environment the core framework must be secure.

**User Interface:** The User Interface of the web application will be developed using Bootstrap framework because its fully responsive, supports HTML5/CSS3 and it’s Open Source. The UI will contain minimum elements to ensure fast loading time of the web application.

Each lesson will have it's own directory which will contain all the files required for that challenge. Each challenge will have an ability to create/initialize and drop it's table for every user. The user will have an option to reset the tables.

<br/>

#### Why a new project ?

The present vulnerable web applications for learning security have many limitations. There is no mechanism through which a user can patch the vulnerability in the challenges and learn how to write secure code.The process of adding new challenges is cumbersome and requires a user to change the source code of the application. Most of the applications are not suitable for school/college environment. Apart from these limitations, they are over-complicated and cannot be safely deployed on a live server. Limitations of some of the most popular similar applications present a

_Damn Vulnerable Web Application:_ (DVWA)

DVWA is damn vulnerable and cannot be hosted on a live server as it will compromise the security of the server. It offers a very limited set of challenges and that too without any hints and explanations. There is no option for evaluating user’s submission or patching the vulnerability. Some of the methods used to prevent vulnerabilities are not the best one.

![DVWA]({{ site.url }}/images/gsoc-proposal/dvwa.png " DVWA using escape_string instead of prepared statements for preventing SQL injections.")

_OWASP Hackademic Challenges:_

It uses simple if/else statements for evaluating user’s submission which results into large number of false negatives. There are no solutions or hints for the challenges. It offers only 10 challenges and does not cover many of the critical vulnerabilities. It supports only single-user mode.

![Hackademic]({{ site.url }}/images/gsoc-proposal/hackademic.png "Hackademic using if/else statements for identifying a XSS vector")

_Google Guyere:_

The google guyere vulnerable application do not support most common attacks like SQL injection. In google guyere challenges are not well defined and user is required to refer to the documentation. For fixing the vulnerability a user to required to change the application code. Moreover the application is developed in Python.

_OWASP Mutillidae II:_

The OWASP mutillidae application offers a functionality like DVWA (Damn Vulnerable Web Application) hence most of the limitations of DVWA are also present in Mutillidae. Support for only single-user mode, difficult to add challenges, no evaluation mechanism etc. are some of it’s limitations.

<br/>
![Comparision]({{ site.url }}/images/gsoc-proposal/comparision2.png "Comparision of various present vulnerable applications")

<br/>
_Timeline and About Me sections are removed in this version of the proposal._
