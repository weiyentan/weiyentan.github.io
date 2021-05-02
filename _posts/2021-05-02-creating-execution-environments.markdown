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

Execution Environment can be created straight by a dockerfile BUT there is a tool called Ansible-Builder that does the task for us! 

In order to install Ansible-Builder its recommended that you use a python virtual environment. You also should be on Python 3.x. So here are the steps that I did to create a python virtual environment and get Ansible-Builder.
```bash
mkdir -p  ~/venv/ansible-builder
python3 -m venv ~/venv/ansible-builder
source ~/venv/ansible-builder/bin/activate
pip3 install ansible-builder==1.0.0.1a
```
Its important that you choose 1.0.0.1a because the default gives you 0.6 which will bork the installation of your Execution Environment.

Now its time to create your files that will make up your Ansible builder.

* execution-environment.yml
* requirements.yml
* requirements.txt
* bindep.txt

Here is a breakdown of what each file is:

## execution-environment.yml
This a file that ansible builder uses to create a container that can be used in AWX.

## requirements.yml
This is what your ansible requirement would typically look like. The examples that I got are for collections so for now I stick with that. Put any collections that you would like into here and the builder will pull them down. At this stage they are from galaxy and possibly from a repository although I haven't tested this.

## requirements.txt
This is your python dependencies that you might want in your container. This is what used to be the old virtual environments in previous builds of AWX.

My files look like this:
## execution-environment.yml

```yml
---
version: 1
dependencies:
  galaxy: requirements.yml
  python: requirements.txt
  system: bindep.txt
additional_build_steps:
  append:
    - RUN alternatives --set python /usr/bin/python3
    - RUN pip3 uninstall --yes ansible-runner && pip3 install ansible-runner==2.0.0a1
    - RUN curl https://packages.microsoft.com/config/rhel/7/prod.repo | tee /etc/yum.repos.d/microsoft.repo
    - RUN yum install -y powershell
    - COPY --from=quay.io/project-receptor/receptor:0.9.7 /usr/bin/receptor /usr/bin/receptor
    - RUN mkdir -p /var/run/receptor
    - USER 1000
    - ADD run.sh /run.sh
    - CMD /run.sh
    - RUN git lfs install

```

As you can see the extra steps that I have is two lines that install PowerShell in the container. This can be taken out, but this was part of what I wanted to achieve so I have added it. 
The dependencies section is what you want in the container. The only exception  that you need certain files is  to is bindep.txt.

Here is what I have in the context of bindep.txt

```bash

python38-devel [platform:rpm compile]
subversion [platform:rpm]
subversion [platform:dpkg]
git-lfs [platform:rpm]

```

bindep.txt

```bash

awxkit>=19.0.0
pypsrp
urllib3

```
Awxkit is tools to manage tower , pyprsp is a way to connect to Windows machines and urllib is a library to manage urls.

requirements.yml
```bash

collections:
  - name: awx.awx
  - name: azure.azcollection
  - name: amazon.aws
  - name: theforeman.foreman
  - name: google.cloud
  - name: openstack.cloud
  - name: community.vmware
  - name: ovirt.ovirt
  - name: kubernetes.core
  - name: ansible.posix
  - name: ansible.windows
  - name: redhat_cop.tower_configuration
  - name: community.general

  ```
These are the collections that I wanted in the base image. Feel free to add or remove as you see fit. Remember you can add collection through the project too. 

run.sh
```bash
#! /bin/bash

ansible-runner worker --private-data-dir=/runner
```



Here is an explaination of some of the things I did. 
```yml

 - RUN alternatives --set python /usr/bin/python3
 - RUN pip3 uninstall --yes ansible-runner && pip3 install ansible-runner==2.0.0a1

```
The default pip3 image does not work. I experienced a whole lot of issues with it despite what people said.

```bash 
When I tried to use the  default image or any images in quay.io I would get this error in the Ansible job template screen:

Traceback (most recent call last):
  File "/var/lib/awx/venv/awx/lib64/python3.8/site-packages/awx/main/tasks.py", line 1397, in run
    res = receptor_job.run()
  File "/var/lib/awx/venv/awx/lib64/python3.8/site-packages/awx/main/tasks.py", line 2957, in run
    return self._run_internal(receptor_ctl)
  File "/var/lib/awx/venv/awx/lib64/python3.8/site-packages/awx/main/tasks.py", line 3008, in _run_internal
    raise RuntimeError(detail)
RuntimeError: Pod Running
```
When I checked the execution environment container before it terminated was this error:

```python
{"status": "error", "job_explanation": "Failed to extract private data directory on worker.", "result_traceback": "Traceback (most recent call last):\n  File \"/usr/local/lib/python3.8/site-packages/ansible_runner/streaming.py\", line 107, in run\n    unstream_dir(self._input, data['zipfile'], self.private_data_dir)\n  File \"/usr/local/lib/python3.8/site-packages/ansible_runner/utils/streaming.py\", line 52, in unstream_dir\n    with zipfile.ZipFile(tmp.name, 'r') as archive:\n  File \"/usr/lib64/python3.8/zipfile.py\", line 1268, in __init__\n    self._RealGetContents()\n  File \"/usr/lib64/python3.8/zipfile.py\", line 1335, in _RealGetContents\n    raise BadZipFile(\"File is not a zip file\")\nzipfile.BadZipFile: File is not a zip file\n"}
{"eof": true}
```

After adding to this [thread](https://github.com/ansible/awx/issues/9917) I found out that I was not alone.  Thanks to  @Bbett on that issue. The solution to my problem above was this:
```bash
 - RUN pip3 uninstall --yes ansible-runner && pip3 install ansible-runner==2.0.0a1
```

So what you should have is a working directory with all the files in it.

I also created a directory called context in the working directory. In that I copied run.sh and made it executable 
```
chmod + x run.sh 
```

I do this on both files.  This is a crucial step otherwise it can't be executed in the container and it will come up with permission denied errors.

To build your container you write something similar to the following:

```bash
ansible-builder build --tag registry_tag
```
where registry tag is your image registry. Don't forget to login to your container registry with either:

* docker login[REGISTRY_URL] 
* podman login [REGISTRY_URL]

You can then push this to your registry using either of the commands depending on what you are using. 

* docker push [REGISTRY_TAG]
* podman push  [REGISTRY_TAG]

In AWX if you are admin  you will see Execution Environments on the left hand side under Administration.
Click on it and you will be presented with a screen with a list of images. Click Add. Fill out the values appropriately.

When it comes to using it in your automation you can set them in projects or job templates of AWX.

One tip. Sometimes I found my docker image to be too large so my kubernetes cluster had problems downloading it. The solution was to pull down the image onto my nodes BEFORE I added that to AWX.

This was done with:
```bash 
docker pull [REGISTRY_URL]
podman pull [REGISTRY_URL]
```

I hope that this helps any of you that is having problems getting their execution environments working. 

Happy automating!