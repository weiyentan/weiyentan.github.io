---
title:  "Provisioning and Configuring k8s clusters"
comments: true
date:   2022-01-01 09:30
categories: [kubernetes]
tags: [howto]
___

Last year I started learning about kubernetes and used rancher to configure the k8s cluster. Rancher (the container orchestration tool) is great to learn about kubernetes with a  Webui. It can manage the complete life cycle of the kubernetes cluster. 

Recently I broke my cluster by inadvertently and decided rather than fixing it I would rebuild it through automation as I previously there were parts that we done manually. 

As there were a lot of moving parts and gotchas I thought I will document it down. 

I will break it down into a two part post. 

I'll cover provisiong and configuration of the 
underlying infrastructure. 

In the second part I'll cover the rancher applications and using a technology called fleet to deploy applications. 

###


