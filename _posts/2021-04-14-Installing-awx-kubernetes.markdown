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
  
An additional step that I was interested in the new versions of AWX is the [Ansible Execution Environments](https://docs.ansible.com/ansible-tower/latest/html/administration/external_execution_envs.html). Execution environments are essentially containers that hold your dependecies to your automation. In the past this used to be in a Python virtual environment. The team has changed that to be a container. These containers can be customised to your specification. This has  massive potential in what can be done in terms of automation which can spiral into a new blog post. For now I will just stick with just the install of AWX.

Much of the installation of AWX   works straight out of the box. There were a few problems. The first problem is that initially I chose to use an external database for the external databases but I found that while the install populated the database the final install did not pick up the external database. For the sake of getting it working I changed tact and chose to use the internal database setup.

As I have my persistent volumes managed by storage classes I found that the persistent volume claim requested by the postgres install was not being fulfilled. This was holding up my install. With a bit of investigation using kubectl I found that the volume that it was requesting from was not set up correctly. 

The command that I used was:
```
kubectl get pvc
```
There I could see the pvc pending

So I changed the default to a  storage class that I know that have worked and sure enough the PVC was fulfilled and the installation continued.

## Kerberos Setup
In order to get this done, the krb5 needs to be referenced. With the docker version allI needed to do was to configure krb5.conf  on the host and somehow the install managed to pick this up.
In Kubernetes it is very different. There has to be some kind of 'map'.