---
title: Learning By Failing with Conditional Access
description: This blog talks about what i did wrong when i went to set up Conditional Access in my microsoft tenant and what i learned from it. Additionally it is based on a talk i had recently at MVP-Dagen Roadshow Bergen. The presentation is available at my github. Hopefully this can help someone setting up Conditional Access avoid the pitfalls i fell into.
date: 2025-11-15T09:30:30+01:00
author: "Bendik Aarvik" 
license: 
hidden: false
comments: false
draft: true
images: []
resources:
- name: "featured-image"
  src: "featured-image.png"
tags : ["Entra ID","Conditional Access","Cyber Security"]
categories:
    - Cyber Security
    - Entra ID
    - Conditional Access
---

# Learning by Failing

## A Conditional Access horror story and what i learned from it

### Background

I was getting into homelabing a while back and since i work mostly with Microsoft services in the cloud i thought getting a Microsoft tenant would be smart so i could test things and get even more familiar with the services i use day to day. Additionally i wanted to use Microsofts services for mail, Outlook. The thing was that i had not yet learned about something crucial to any Conditional Access implementation, namely the Brake Glass Account.

I jumped into it with a lot of enthusiasm and guts, starting with configuring the tenant with Entra ID, Intune, E-Mail, Privileged Identity Management and then i got to Conditional Access.

### What happened?

While setting up Conditional Access i had issues with getting my policies to report that they had indeed blocked or allowed any logins. I was skiming through my sign-in logs but could not see any thing that applied to my account. Until this point i had only managed existing Conditional Access environments and not set one up my self. I tweaked the policies i had and used Microsoft`s own templates but nothing seemed to work. I started to think that the issue lied in faulty reporting when the policies stood to Report-only so i set all my policies to the state "on". After that not working tinkered around a bit more before i saw the exclude tab. Thinking i finaly found the culprit i removed the exclution of my account on all the policies and then refreshed my browser...

Here is a recreation of the error i got:
![azure-sign-in-error](./azure-sign-in-error.png)

### Consequences

As you can see i had locked myself out of my tenant properly. Spesificly i had required that i log on featured-image a domain or hybrid joined device in my cloud only environment. Effectivly i could not use any of my microsoft services related to Entra ID. At the time this was mostly effecting my management of Entra ID, Intune and Azure as well as OneDrive and Outlook 

I am glad i was in a small tenant with just myself. If this had happened in a production environment and or in an organization that relies on Entra ID to authenticate to more business critical services. Then this would be a disaster. My policies were scoped to all users and services so everything that was integrated with Entra would be impacted and locked.

#### Some questions to consider
- Can you do business if you are locked out of your authentication platform?
- Is Single Sign On (SSO) worth it if it can be this big of a single point of failure.
- How many eggs in one basket is too many?

### Solution

My solution started with DNS. In these days that is usualy not how it goes seeing both AWS and Azure Struggleing just a couple of weeks ago. Through DNS i could point back to my domain registrars own E-Mail solution so i could communicate on my E-Mail. Then i called up my old friend Microsoft as i could not open a ticket in the intended way. What met me on the phone was the awnser; "You are not an Administrator, you need to contact your Administrator about this issue." You see what had happened was that i had configured Privileged Identity Management and my account did not have the permanent assignment as Global Administrator. This made Microsoft`s support unsure of who i was as i looked like just another user. After alot of back and forth they finaly understood what i had done and they then requested i add a DNS TXT record with a spesific string so they could verify that i owned the domain that was connected to my tenant. After doing so they elevated my account to a Global Administrator and gave me 24 hours to fix my stuff.

In short, having my DNS services outside of the Microsoft sphere of influence ensured i could communicate with Microsoft efficiently and get back into my tenant through DNS ownership verification

### What did i learn?

#### Testing
- Report-only
- Test Groups

#### Brake Glass Account
- PIM 
- exclution
  - Excluding empty groups
- How do you handle creds and routines 
#### Not using templates or solutions you do not fully understand

#### DNS
- keeping dns with another provider than microsoft is smart to consider

#### Documentation
- IDPowerApps by Merill
