---
title:  "Provisioning and Configuring k8s clusters"
comments: true
date:   2022-01-01 09:30:00
categories: [kubernetes]
tags: [howto]
---

Last year I started learning about kubernetes and used rancher to configure the k8s cluster. Rancher (the container orchestration tool) is great to learn about kubernetes with a  webui. It can manage the complete life cycle of the kubernetes cluster. 

Recently I broke my cluster by inadvertently and decided rather than fixing it I would rebuild it through automation as  previously there were parts of my installation that we done manually. 

As there were a lot of moving parts and gotchas I thought I will document it down. 

I will break it down into a two part post. 

I'll cover provisioning and configuration of the kubernetes cluster  of the underlying infrastructure using Terraform and Ansible.

In the second part I'll cover the rancher applications and using a technology called fleet to deploy applications. 


## Provisioning and configuring infrastructure 

One of the goals that I  had was to be able treat all my infrastructure as code. What this  meant is  that at any one time I should be able to destroy the cluster and restore it back again. I chose to use Terraform and Ansible to do this.

The reason why I liked Terraform is that its primary a declarative language. I can describe what my infrastructure looks like and then Terraform will build that out. If I neeed to destroy I can do reprovision as needed.  If I want to add memory or CPU  I can change the declaration in my code and Terraform will go about and make the necessary changes. It makes for a very clean way of working. I use Gitlab for my source control and by doing this I can use pipelines to manage my code execution. The inner workings is a discussion for another post.   

However there is some cavaets and gotchas to this.  One of this is if you declare something in your configuration and then remove it, Terraform will make the necessary changes. Also some changes ie "changing network ip addresses" can mean that the whole vm can get recreated. Say for example you declare a hard disk is in your configuration and you decide to remove it from your declaration...when the code get committed to the main/master branch Terraform will go about removing that disk.

As far as configuring the OS I used ansible. This is a very simple but powerful configuration management tool. It doesnt require an agent. In a nutshell I created an 'inventory file'. These contained my virtual machines. Then I organised everything into either a master / worker / storage group and an overarching  kubernetes group.

Then when I wanted to apply configurations I created 'roles' (these are groups of tasks ) and set them in to a playbook (which is essentially a script). I made it as generic as possible. I set variables to them and then applied the variable settings to the inventory. 

By doing it this way different groups would get different settings applied but using the same playbook.

This setup of using terraform to provision and then use ansible to configure with a good combination to use. They are each powerful tools in their own respective right.

In part 2 I will be focusing in on the issues that I found when using various different kubernertes applications and will go over what I  did to remedy the situation.