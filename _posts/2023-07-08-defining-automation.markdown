---
title:  "Defining Automation - What is it really and what does it consist of?"
comments: true
date:   2023-07-08 12:30:00
categories: [automation]
tags: [thoughts]
---

I have given this topic a lot of thought and spoken to many different people about this topic. What is automation really? Of course many people have different views on the matter. Of course we get the old adage definition of reducing down the toil of mundane and repeatable tasks. I am not disputing that and I am not going to expand that because this definition has beaten as a dead horse. 

I think the more interesting question is what does automation consist of? Because one could argue that automation stops at running scripts / creating functions in your preferred language albiet PowerShell/Bash from  a management bastion host or like Go / Python being packaged up into a web application that can be put into a Platform as a Service in the cloud. But for me there is a lot more to it than that. 

There is no doubt that these all contribute automation. However the process to write , test , implement the automation is where I think the magic starts to happen. As an ex colleague of mine said, when I demonstrated the concept this is what you call by end to end automation.

In my mind, automation consists of four main parts.

* Plumbing
* Orchestration/Configuration Management
* Flow

# Plumbing
These are programming languages. PowerShell, Bash, Go, Python.  While they can exist on their own, and they could overlap the other two  I have found that these are best served  in a framework or exist in a flow category. Plumbing tools contain logic and various complexities. 

# Orchestration/Configuration Management
Programs that are designed for Orchestration  are systems that are designed to layer and order your automation. With these systems these are not designed to be programmed. You are just there to declare a state. 

Underneath the hood the plumbing tools are being used to handle what needs to be done. 

Configuration Management fits into this mode as well. Configuration Management philosophy is idempotent. This means that you declare what you want and underneath the hood your plumbing tools are doing all this. These programs are designed to take inputs and then pass that into the plumbing tools.

Tools like Ansible, Puppet/Bolt

# Flow
These programs fit into a category like Devops tools like Pipelines. There is an overlap with tools like AWX / Ansible Automation Tower. Suffice to say that these spots are not to have any logic. Its just a directive to say run this. Do that.

