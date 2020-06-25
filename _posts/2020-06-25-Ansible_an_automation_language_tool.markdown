---
title:  "Ansible - Automation or a tool for Automation"
comments: true
date:   2020-06-25 12:31:23
categories: [thoughts]
tags: [ansible]
---

I have heard this so many times before. Many managers/middleware engineers  and zealots refer to Ansible as 'automation' making us believe that it  is the programming language that everyone should only be using.  By that I mean all logic and such appear in the playbook/role that they are creating. 
They also think that it is the only system that people need to do things with. I have heard people say "if we use Ansible then I don't have to use this product anymore." (in most situations this is a patching tool typically SCCM). Its an either or situation.

On the flip side I have also heard system engineers (who know Bash/PowerShell) say well 'I have my trusty PowerShell / Bash tooling. Why would I need ansible for?

I cringe when I hear these two statements. Here are my thoughts on the matter.

*** The case for ansible

Don't get me wrong. Using cmdline shell languages like Bash or PowerShell is great. In PowerShell I can create multiple sessions to servers I want to retrieve information from and I get instant results. I can also change state. If something is not in the right state or I want something changed linux /windows engineers can write a quick script  and perform that task.
That works when you are working on a smaller subset of tasks related to a small number of servers for example configuring os settings for region. You just need one script. 

The problem comes in when you scale that task to multiple servers.

Lets say instead of configuring for regional settings you have to configure the settings on the entire operating system. That might mean configuring  

In those scenarios you will see the tasks being segregated out to many different teams. If a SQL server was being built , the Windows Team will have stood up a Windows Server , configured it and then passed straight to a DBA to install the SQL Server to run their scripts.
