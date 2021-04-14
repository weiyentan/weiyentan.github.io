---
title:  "AWX - Installing through Operator with version 18-19"
comments: true
date:   2021-04-12 18:48:23
categories: [ansible]
tags: [howto]
---

Recently I saw that the  team released AWX 18 last month and AWX 19 a few days ago.

So I thought I might as well give it a go.

There a couple of lessons that I learnt front this and I thought I would pen them down for future reference and for any help that may experience the same thing.

The AWX operator install is very different than the previous docker install. This is largely because the structure is for kubernetes.

I had a few goals in using kubernetes version of AWX as a follow on to my previous install of the docker version of AWX. They were as follows:

* Kerberos Authentication to Windows Machines
* LDAP authentication to Ansible Tower
* There are some PowerShell cmdlets that I use to interface with Tower. Making sure that they still work ( My language is in PowerShell so naturally I want to keep this going.)
  
An additional step that I was interested in the new versions of AWX is the [Ansible Execution Environments](https://docs.ansible.com/ansible-tower/latest/html/administration/external_execution_envs.html). Execution environments are essentially containers that hold your dependecies to your automation. In the past this used to be in a Python virtual environment. The team has changed that to be a container. These containers can be customised to your specification. This is massive potential in what can be done in terms of automation which can spiral into a new blog post. For now I will just stick with just the install of AWX.

I used Rancher 