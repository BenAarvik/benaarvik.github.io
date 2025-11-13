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
tags : ["Entra ID","Conditional Access","Cyber Security","Learning by Failing"]
categories:
    - Entra ID
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

### Solution

My solution started with DNS. In these days that is usualy not how it goes seeing both AWS and Azure Struggleing just a couple of weeks ago. Through DNS i could point back to my domain registrars own E-Mail solution so i could communicate on my E-Mail. Then i called up my old friend Microsoft as i could not open a ticket in the intended way. What met me on the phone was the awnser; "You are not an Administrator, you need to contact your Administrator about this issue." You see what had happened was that i had configured Privileged Identity Management and my account did not have the permanent assignment as Global Administrator. This made Microsoft`s support unsure of who i was as i looked like just another user. After alot of back and forth they finaly understood what i had done and they then requested i add a DNS TXT record with a spesific string so they could verify that i owned the domain that was connected to my tenant. After doing so they elevated my account to a Global Administrator and gave me 24 hours to fix my stuff.

In short, having my DNS services outside of the Microsoft sphere of influence ensured i could communicate with Microsoft efficiently and get back into my tenant through DNS ownership verification

The time to solve my issue was about 4 weeks, as i did not have any fancy support deals with Microsoft. There was alot of back and fourth and due to me using Privileged Identity Management and not being an admin i had to do extra verification steps. I also missed some calls due to getting them while at work or working with other things. I have seen this happen to a company, they got it solved in 2.5 days as the only focus for them was getting their environment up and going again as they were losing money every day.

### What did i learn?

#### Exclutions

The most important thing i learned was that you must always have the correct exclution to your Conditional Access policies. This being your Brake Glass Account/Accounts. Thankfully since my mishap Microsoft took to agreeing with that and it is now not possible to setup a Conditional Access policy without an exclution but! Stay vigilant you can still add an empty group for an exclution so make sure to either lock the groups with Restricted Administative Units so no one can tamper with them or monitor them with the Conditional Access Optimization Agent. (Thank you to Julian Rasmusen for demoing this right after my talk!)

#### Testing

I should have tested things more throughly but because it was my environment that only served me i got sloppy. What i will do going forward is have a couple test users, one "normal" and one "admin" that i can test and emulate what i do in a day then analyse the results from the report only overview. This ensures i can understand what the policies do before i implement them as well as not locking my self out completely while only testing.

#### Brake Glass Account

Here i will preface that you should consult the documentation of your identity provider as a Brake Glass Account(BTG) is really important that you set up correctly. Here is a link to the docs for [Entra ID](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access). Here you can also find the resources on how to look after its credentials. The TLDR is use a FIDO2 key for mfa and store the password and FIDO2 key in separate safes in different locations.

BTG Accounts is an emergancy access account that is supposed to be used for situations where one would need to be an administrator but one does not have access through "normal" means, for example if you are locked out of your tenant due to Conditional Access or you have an emergency and the normal means to aquire the Global Administrator role is through approval, and due to the emergency the party responsible for approving is not able to do so. Then you would be able to access the BTG account and make the changes needed. 

I thought Privileged Identity Management(PIM) was a smart thing to always use. I was not entirely wrong but in my situation with no BTG account it created more hassle as Microsoft could not see that i had any admin accounts at all because of me implementing PIM and my admin account having just-in-time(JIT) access to their admin roles. Had i setup a BTG account, i would be able to give them a quick awnser instead of trying to explain PIM to the support tech so they could understand that, yes im an admin just not right now... 

#### Not using templates or solutions you do not fully understand

This is the age old, "If you find a script online do not just run it. Read it till you understand it. Then you may use it if it does what you want to." I added all the "cool" policies to my setup without seeing if i needed them. For my cloud only setup i did not need a policy that locked out all devices that were not AD Joined...

#### DNS

If i would have moved my domain completely to being managed by Microsoft i would not have been able to edit the TXT record to prove i owned it. I will be keeping my domains with a third party because of this.

#### Is SSO bad because its a huge single point of failure?

Short awnser; No. 
Longer awnser; The control and visability you get from having one authentication platform is still not understood enough in my opinion. Too often i see organizations who have their office apps and e-mail in one place and then they register to a third party app with their work mail. This is effectivly shadow IT and makes you loose control of your attack surface. You no longer get to enforce your password, mfa and conditional access policies. The user also has to remember another password, if you have enough services like this there will be compromise to password strength as users have to remember more passwords they tend to use the same or weaker passwords.

#### Documentation

I found documenting Conditional Access to be a bit of a drag, wanting to automate it i found Merill Fernando´s idPowerApp Conditional Access Documenter it is a tool that does a good job at documenting your policies [IDPowerApp](https://idpowertoys.merill.net)
