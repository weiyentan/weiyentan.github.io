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
In order to get this done, the krb5 needs to be referenced. With the docker version all I needed to do was to configure krb5.conf  on the host and somehow the install managed to pick this up.
In Kubernetes it is very different. There has to be some kind of 'map'. At first I thought that this was just a adding a configmap to the  to the current  AWX deployment, but I should have read closer. It was not apparent to me but the configuration of the resulting AWX deployment (I only installed up to AWX 12) is actually dependent on the AWX Operator. That definition comes in the form of  a CRD (Custom Resource Definition).
The steps are:

* Define the CRD
* kubectl apply -f /PATH/TO/YOUR/
* Execute the Operator 

As a first time user of Kubernetes i just followed the instructions in the operator AWX github page and just run this yml. 

```
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx

```
What I realised is that this is the heart of the configuration of AWX. Based on the help of some people in the AWX Google Group  (shout out to [Brandon Ellis](https://github.com/BrandonALXEllisSS)) who pointed me on the right direction.

This is the final configuration of my yml file.
```
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx
spec:
  tower_ingress_type: Ingress
  tower_task_extra_volume_mounts: |
    - name: krb5
      mountPath: /etc/krb5.conf
      subPath: krb5.conf
  tower_extra_volumes: |
    - name: krb5
      configMap:
        defaultMode: 420
        items:
          - key: krb5.conf
            path: krb5.conf
        name: krb5.conf
  ldap_cacert_secret: awx-ldap-cert
  ```
The tower_task_extra_volume_mounts is defined as a configmap.
ldap_cacert_secret is my CA cert in PEM format as a kubernetes secret.

With this setup I was able to create a AWX install that viewable through an ingress. Has AD LDAP authentication integration and can communicate through to Windows Machines using kinit. 

I hope that this post has helped you.

