---
title:  "Core concepts  PowerShell - Workhorse Commands
comments: true
date:   2020-01-05 12:31:23
categories: [howto]
tags: [PowerShell]
---

This is the second part of the blog post series of learning PowerShell.
Before we start on this post there is just one concept that I want to define. That is the term collections.
As previously discussed in the last post PowerShell is object orientated but we not only deal with one object at a time,
we can deal with a lot of objects at a time.

So what are some example of collection?
Lets say we run this command:
```PowerShell
get-childitem "c:\temp" -File
```
and we get back:

```PowerShell
FullName                                Length
--------                                ------
C:\temp\ansiblehosts.txt                   460
C:\temp\cert.cer                          1042
C:\temp\current_servers.csv                309
C:\temp\current_servers.txt                309
C:\temp\GPOResult.xml                   213426
C:\temp\html.html                        89696
C:\temp\IMG_4203.JPG                   3210248
C:\temp\install_pswindows_psmodule.tar    5120
C:\temp\jenkins.crt                       2226
C:\temp\jenkins.key                       3324
```
This list of files is called a 'collection'.


So what do I mean by workhorse commands? These are commands that that wil be used over and over again in your PowerShell career. 70% of the time I have found when scripting in PowerShell,
I have found myself navigating through objects. Filtering through collections(a bunch of objects), so its important to understand the core usage of how they work.

The key commands that we are going to be talking about are:

```Powershell
Select-Object
Where-Object
Sort-Object
Group-Object
Measure-Object
```
There is a lot going on with each of these commands so I am just going to cover the basics in this post on how they work. For further information on certain examples
you can use Get-Help.

## Select-Object
Select-Object is a command that allows you to select properties from an object. 
So going back to our previous command I am going to pipe the results to select-object and see what comes back:

```PowerShell
get-childitem "c:\temp" -File | select-object fullname , length -first 5
```

In my example PowerShell returns back to me the following:

```PowerShell
FullName                    Length
--------                    ------
C:\temp\ansiblehosts.txt       460
C:\temp\cert.cer              1042
C:\temp\current_servers.csv    309
C:\temp\current_servers.txt    309
C:\temp\GPOResult.xml       213426
```

So what happened? We produced a list of files under c:\temp , and then passed the collection to Select-Object. Because we gave two properties, Select-Object then
displayed those properties only only displaying the first 5.

So the question might be: how do I know what properties to return? I cannot see the property that I am looking for....

This is where the Get-Member cmdlet is so invaluable. In the previous cmdlet we can see the properties of the cmdlet by typing Get-Childitem c:\temp | Get-Member -MemberType property 

```PowerShell
   TypeName: System.IO.FileInfo
Name              MemberType Definition
----              ---------- ----------
Attributes        Property   System.IO.FileAttributes Attributes {get;set;}
CreationTime      Property   datetime CreationTime {get;set;}
CreationTimeUtc   Property   datetime CreationTimeUtc {get;set;}
Directory         Property   System.IO.DirectoryInfo Directory {get;}
DirectoryName     Property   string DirectoryName {get;}
Exists            Property   bool Exists {get;}
Extension         Property   string Extension {get;}
FullName          Property   string FullName {get;}
IsReadOnly        Property   bool IsReadOnly {get;set;}
LastAccessTime    Property   datetime LastAccessTime {get;set;}
LastAccessTimeUtc Property   datetime LastAccessTimeUtc {get;set;}
LastWriteTime     Property   datetime LastWriteTime {get;set;}
LastWriteTimeUtc  Property   datetime LastWriteTimeUtc {get;set;}
Length            Property   long Length {get;}
Name              Property   string Name {get;}
```

This is what is returned. All of these properties is what you can pass to Select-Object for the cmdlet Get-Childitem. 
