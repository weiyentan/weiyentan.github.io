---
title:  "Ansible - Automation or a tool for Automation"
comments: true
date:   2020-01-06 16:31:23
categories: [thoughts]
tags: [ansible]
---

I have heard this so many times before. Many managers/middleware engineers  and zealots refer to Ansible as 'automation' making us believe that it  is the programming language that everyone should only be using.  This means all logic and such appearing the playbook/role that they are creating. 

They also think that it is the only system that people need to do things with. I have heard people say "if we use Ansible then I don't have to use this product anymore." (in most situations this is a patching tool typically SCCM). Its an either or situation.

On the flip side I have also heard system engineers (who know Bash/PowerShell) say well 'I have my trusty PowerShell / Bash tooling. Why would I need ansible for?

I cringe when I hear these two statements. Here are my thoughts on the matter.

## The case for ansible

Don't get me wrong. Using cmdline shell languages like Bash or PowerShell is great. In PowerShell I can create multiple sessions to servers I want to retrieve information from and I get instant results. I can also change state. If something is not in the right state or I want something changed linux /windows engineers can write a quick script  and perform that task.
That works when you are working on a smaller subset of tasks related to a small number of servers for example configuring os settings for region. You just need one script. 

The problem comes in when you scale that task and many others to multiple servers.

Lets say instead of configuring for regional settings you have to configure the settings on the entire operating system, SQL and an application. In an enterprise environment that I have seen that would mean three different teams .  

In those scenarios you will see the tasks being segregated out to many different teams. In the above example there would have been a Windows Admin to run the operating type tasks , a DBA to perform SQL installation and tasks and an application developer to perform tasks on the application stack.

This may also take the form of a change request  where different parties do different tasks relating to their skill set.

Each maybe running their own automation scripts.

Ansible plays a role here because its main power is that it brings together into one single script...in ansiblespeak it is known as a 'playbook'.

These can be summarised down to 'roles', pieces of automation and can be layered together.

It is also declarative/idempotent  which is very different than what what we do in PowerShell. In PowerShell you have to define flow and logic and have a whole lot of conditionals.


While other configuration platforms like Puppet and Chef have this too, Ansible does this better because it is a push configuration. Ansible is the controller. You can sequence a workflow on groups of different servers.

I have experienced both sides of the spectrum using PowerShell as 'wrapper scripts' and using Ansible. While there is a tiny bit more upfront work with Ansible in terms of defining where the roles come from, I have always found it easier to piece together a playbook than writing scripts.

## The cases where ansible does not seem to fit?
As  a Windows admin and more importantly PowerShell Core + using Ansible Tower there are times where using Ansible Tower seems over the top. One of those situations is break fix type scenarios. In Ansible Tower in order to add automation you have to add it in through a repository. In a break/fix scenario, where urgency is of importance and that is where PowerShell / Bash can do things quickly done . Sure , at a later stage the process can be added to the Tower environment to handle the issues in the future but at the time of the incident using command line to fix undiscovered issues is probably the best course of action.

