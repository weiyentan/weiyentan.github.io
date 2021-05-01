---
title:  "AWX - Creating Execution Environments"
comments: true
date:   2021-05-02 23:48:23
categories: [ansible]
tags: [howto]
---
The final step that I wanted to test out in mmy transition to AWX 19 from my previous install of AWX 12 was to make sure that I could use the new features in AWX 18+ which is [Execution Environments](https://ansible-runner.readthedocs.io/en/latest/execution_environments.html).

Execution Environments are really containers which has primarily got ansible and python bundled with dependencies that you as a developer might want. This can include things relating to Ansible such as collections , python requirememts in the form of pip3 requirememts but what is of more interest to me is the extra binaries that I can put in the docker image.

I started off my IT automation career using PowerShell.  So I wanted to take what I know to create custom ansible libraries and module_utilities that would run in PowerShell on the ansible controller.
By using Execution Environments I will be able to achieve this because I can install PowerShell in the docker build image and then I can use PowerShell to automate. Without further ado lets go through the process!

Execution Environment can be created straight by a dockerfile BUT there is a tool called Ansible-Builder that does the task for us. 

In order to install Ansible-Builder its recommended that you use a python virtual environment. You should be on Python 3.x . So here is the steps that I did to create a python virtual environment and get Ansible-Builder. 

```
mkdir -p  ~/venv/ansible-builder
python3 -m venv ~/venv/ansible-builder
source ~/venv/ansible-builder/bin/activate
pip3 install ansible-builder==1.0.0.1a
```
Its important that you choose 1.0.0.1a because the default gives you 0.6 which will bork the compilation the dockerfile.

