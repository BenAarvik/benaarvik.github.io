---
title: Monitoring to stay ahead
description: This blog post discusses the importance of monitoring for security purposes in order to stay ahead of the constantly changing threat landscape.
date: 2022-12-22T09:30:30+01:00
author: "Bendik Aarvik" 
license: 
hidden: false
comments: false
draft: false
images: []
resources:
- name: "featured-image"
  src: "featured-image.png"
tags : ["Sentinel","SIEM","Cyber Security"]
categories:
    - Cyber Security
---


# Monitoring to stay ahead of the threat landscape

It is often difficult to catch everything that is crawling around your network. 
It is especially hard to catch what is outside your network. 
It is also where new vulnerabilities get discovered day by day and hour by hour. 

Recently there was a Zero-Day vulnerability in exchange that shook the industry. Thankfully, Microsoft was early out with mitigation steps so people could secure their systems. Two days later it was confirmed that attackers had bypassed the mitigation. That is how quickly things can change. From zero danger to full danger, all in hours and minutes. Even a put out fire needs to be monitored so it does not go ablaze again.

Almost all applications save everything that is happening in the application to a log file, these can be extremely valuable when investigating a security incident or looking for a unnoticed breach. If we were to read all of these logs we would quickly get an information overload. These logs are extensively detailed to the point that it could take an hour to read forty two seconds worth of logs. It is simply Too Much Information (TMI).



Tools like Microsoft Sentinel can help us stay ahead of the logs by monitoring them as they are created and analyzing them for us. Sentinel is a tool that leverages Machine learning and predefined rules to define alerts, behavioral patterns and anomalies to these patterns. This ensures that all logs are investigated and we only need to act on actual security incidents.

Attackers also often leverages known weaknesses in applications but sometimes they use unknown weaknesses. These are known as zero-day vulnerabilities or exploits. These are especially dangerous as there are often no mitigation known to stop the attack and they are hard to trace as no one know how they function. These are almost impossible to defend yourself from. How are we supposed to? We are looking for a needle in a hay stack without knowing what the needle look like.

To be able to start to defend ourselves against zero-day vulnerabilities like this we would need to scour the internet for both the known and unknown exploits to see if any of our own systems are effected. We would have to do this day-in and day-out because we never know when a new vulnerability or exploit would be known. 

What we could do to get a birds eye view of the threat landscape is to automate it. We can then search on forums and new outlets and get the automation to alert us when there are news related to cybersecurity and relevant systems. We can then cross check the new information with our own systems to see if we are impacted.

Sentinel can also automate this by subscribing to what we call threat intelligence feeds. These are feeds of detailed information about the latest security news. Here we get granular information and can corelate specific files, ip addresses, links and domain names to real security events around the world. This type of information is also known as an Indicator of Compromise (IoC). With IoCs we can for example we can be alerted when a user accesses a known domain correlated to ransomware. This ensures that all the newest threat intelligence is active and helping us at all times.

And thats how we use Monitors to stay ahead of the threat landscape.

<br>
<br>
<br>
<br>

You can also read this on Amesto Fortytwo's blog [here](https://blog.amestofortytwo.com/monitoring-to-stay-ahead/)