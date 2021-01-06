---
title:  "Ansible - How to use  groupvars to your advantage"
comments: true
date:   2020-01-6 18:48:23
categories: [ansible]
tags: [howto]
---
I am a long time user of Ansible.  When I first started out and for for the most part other people that I have seen, the common thing to do  is to  put variables in the extra-vars of ansible-playbook or  job templates in Ansible Tower/AWX.

This works for the most part  for the particular job at hand. However consider the scenario of where we have certain servers in different groups where the values are different ? Doing this the original way I will have to change the variables  in the extra-vars  and then run the playbook again.

Is there a better way?  The short answer is YES!

Enter Inventory/group Variables

Inventory files are used to store the hosts that ansible can reach. Many people I have seen (including me) just use it for basic connection settings.  But they have a lot more value than that. They allow you to create groups and -add- variables  to those groups or even at the host level. Whenever the playbook runs, ansible will pull the vars from the task/role/plays/ inventory and extra-vars take precedence over everything. ( there are a lot more variable precedence to work with  than that but  these are the variables that I work with.) As long  as my extra-vars is not overwriting what is defined in the inventory vars then Ansible will read the vars from the inventory.

In my group_vars folder there will be a file that is named as a group in my inventory file. In that yml file I would define all my variables. When a playbook runs  where a variable exists in the group/host var file, at execution Ansible will start to compile the variables together. 

What does this give me?  What it means is that I could have a group called webservers and a group called sqlservers and I could run the playbook 'once' and Ansible will make sure that each group is given the correct settings. An example is below:

Lets say I have a variable called foo_settings that I need to apply across both sqlservers and webservers group.
On sqlservers group the value needs to be 'sqlserver_settings' and webservers group needs to have 'webserver_settings' applied to it.
My data folder structure looks like this:
![example_Jekyll_structure](/assets/2021-01-06 18_06_04-Using_groupvars_to_your_advantage_diagram1.png)