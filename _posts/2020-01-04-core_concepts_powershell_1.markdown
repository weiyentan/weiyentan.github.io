---
title:  "Core Concepts of Powershell: Where do I Start??"
comments: true
date:   2020-01-04 12:31:23
categories: [learning]
tags: [powershell]
---

I am part of a PowerShell telegram group for Linux users called [PowershellonLinux](https://t.me/PowershellOnLinux) on Telegram. I am a windows engineer by heart so I was curious to find out why linux people will be interested in PowerShell. What I discovered is for another post but  one of the conversations that we had was discussing how to do basic 101 things with PowerShell. 

After much discussion one of the member's suggested that I consolidate that into a series of blog posts. I did touch this on my archived blog but I feel that this can be revisited.

This will be a few part mini post series. This post will be focused on the basics to get started with PowerShell. 

I started learning PowerShell and went under the virtual grand tutelage of [@concentrateddon](https://twitter.com/concentrateddon) through video tutorials of CBT nuggets at the time which was in 2015. This is by far the best training materials that I have started with in my Powershell learning career. 

Commands in PowerShell are called cmdlets. According to [@jsnover](https://twitter.com/jsnover) There is some trivia behind this. That is because cmdlets is unique. If you look on any search engine and you type in cmdlet the results that you get returned back is Powershell. 

For Microsoft that is instant SEO! 

PowerShell is a command line shell, very much the same as Bash in Linux or DOS yet at the same time can form the basis of a very powerful scripting language.

PowerShell follows a verb-noun structure. There are approved verbs that is 'approved'. These aid in the discovery of commands as you shall see in the post below.

When people start learning PowerShell I would suggest that there are three commands that people start to learn to get them started.

```
Get-Command
Get-Help
Get-Member
```

### Get-Command
Get-Command is used  for searching for a command. Because of the approved verbs we can easily search for commands that suit what we need. Take for example finding a command that can find active directory users. We can type in the PowerShell console:

```
Get-command get-*user -module active*
```

This returns:

```

CommandType     Name                                               Version    Source                       
-----------     ----                                               -------    ------                       
Cmdlet          Get-ADUser                                         1.0.0.0    activedirectory
```

So what happened there? We asked PowerShell to look for any commands to list active directory users. Notice how versatile the search can be. We were not quite sure what the full command was but because of the verb-noun structure we know that when we could pass in parameters to the command to filter what we want. PowerShell understand wildcarding for values in the parameter. NB. Parameters can be discovered by typing - after a command and pressing tab. PowerShell will cycle through the available options if it recognizes the command. If it doesn't the cmdlet doesnt exist. 

Some people have said that PowerShell is too verbose, too wordy. The previous example really shows one of the reason why. Discoverability! It can be easier to discover commands. In order to get the list of approved verbs we can type Get-Verb.

### Get-Help
 Once we have found the command that we want how can we get information on how to use it?

Get-Help is your answer. Remember to run update-help to get the latest help files.

Running the command can be as simple as:

```
Get-Help get-aduser

```

This returns back help. We can also search for help online:

```
get-help get-aduser -online

```

### Get-Member
PowerShell is object oriented. This means that wnen I run a command it outputs an object. I can see the properties  and methods that are associated with the object. I'll be discussing more in detail in regards to this in a  later post but I'll go over the basics here.

#### Properties
Properties describe the object. If you have learnt other  languages like Python this is also commonly known as attributes.

For example if I type:

```
get-childitem c:\windows\temp | get-member -Type Properties
```

PowerShell will return 

```


   TypeName: System.IO.DirectoryInfo

Name              MemberType     Definition                                                                            
----              ----------     ----------                                                                            
LinkType          CodeProperty   System.String LinkType{get=GetLinkType;}                                              
Mode              CodeProperty   System.String Mode{get=Mode;}                                                         
Target            CodeProperty   System.Collections.Generic.IEnumerable`1[[System.String, mscorlib, Version=4.0.0.0,...
PSChildName       NoteProperty   string PSChildName=appInsights-nodeAIF-444c3af9-8e69-4462-ab49-4191e6ad1916           
PSDrive           NoteProperty   PSDriveInfo PSDrive=C                                                                 
PSIsContainer     NoteProperty   bool PSIsContainer=True                                                               
PSParentPath      NoteProperty   string PSParentPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\weiyentan\AppDat...
PSPath            NoteProperty   string PSPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\weiyentan\AppData\Loca...
PSProvider        NoteProperty   ProviderInfo PSProvider=Microsoft.PowerShell.Core\FileSystem                          
Attributes        Property       System.IO.FileAttributes Attributes {get;set;}                                        
CreationTime      Property       datetime CreationTime {get;set;}                                                      
CreationTimeUtc   Property       datetime CreationTimeUtc {get;set;}                                                   
Exists            Property       bool Exists {get;}                                                                    
Extension         Property       string Extension {get;}                                                               
FullName          Property       string FullName {get;}                                                                
LastAccessTime    Property       datetime LastAccessTime {get;set;}                                                    
LastAccessTimeUtc Property       datetime LastAccessTimeUtc {get;set;}                                                 
LastWriteTime     Property       datetime LastWriteTime {get;set;}                                                     
LastWriteTimeUtc  Property       datetime LastWriteTimeUtc {get;set;}                                                  
Name              Property       string Name {get;}                                                                    
Parent            Property       System.IO.DirectoryInfo Parent {get;}                                                 
Root              Property       System.IO.DirectoryInfo Root {get;}                                                   
BaseName          ScriptProperty System.Object BaseName {get=$this.Name;}                                              


   TypeName: System.IO.FileInfo

Name              MemberType     Definition                                                                            
----              ----------     ----------                                                                            
LinkType          CodeProperty   System.String LinkType{get=GetLinkType;}                                              
Mode              CodeProperty   System.String Mode{get=Mode;}                                                         
Target            CodeProperty   System.Collections.Generic.IEnumerable`1[[System.String, mscorlib, Version=4.0.0.0,...
PSChildName       NoteProperty   string PSChildName=18e190413af045db88dfbd29609eb877.db.ses                            
PSDrive           NoteProperty   PSDriveInfo PSDrive=C                                                                 
PSIsContainer     NoteProperty   bool PSIsContainer=False                                                              
PSParentPath      NoteProperty   string PSParentPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\weiyentan\AppDat...
PSPath            NoteProperty   string PSPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\weiyentan\AppData\Loca...
PSProvider        NoteProperty   ProviderInfo PSProvider=Microsoft.PowerShell.Core\FileSystem                          
Attributes        Property       System.IO.FileAttributes Attributes {get;set;}                                        
CreationTime      Property       datetime CreationTime {get;set;}                                                      
CreationTimeUtc   Property       datetime CreationTimeUtc {get;set;}                                                   
Directory         Property       System.IO.DirectoryInfo Directory {get;}                                              
DirectoryName     Property       string DirectoryName {get;}                                                           
Exists            Property       bool Exists {get;}                                                                    
Extension         Property       string Extension {get;}                                                               
FullName          Property       string FullName {get;}                                                                
IsReadOnly        Property       bool IsReadOnly {get;set;}                                                            
LastAccessTime    Property       datetime LastAccessTime {get;set;}                                                    
LastAccessTimeUtc Property       datetime LastAccessTimeUtc {get;set;}                                                 
LastWriteTime     Property       datetime LastWriteTime {get;set;}                                                     
LastWriteTimeUtc  Property       datetime LastWriteTimeUtc {get;set;}                                                  
Length            Property       long Length {get;}                                                                    
Name              Property       string Name {get;}                                                                    
BaseName          ScriptProperty System.Object BaseName {get=if ($this.Extension.Length -gt 0){$this.Name.Remove($th...
VersionInfo       ScriptProperty System.Object VersionInfo {get=[System.Diagnostics.FileVersionInfo]::GetVersionInfo...
```
Lo and behold, here are the properties of the object for get-childitem.

So lets examine what we just did. We asked PowerShell to retrieve the items in the path
```
get-childitem c:\windows\temp
```
We then passed that information down the pipeline to Get-Member with the membertype of properties
```
get-childitem c:\windows\temp | get-member -Type Properties
```

which gave us the properties for the object. However if you look at what came back, they were of two type of objects.

One was a directory:
```
TypeName: System.IO.DirectoryInfo
```
and the second was a file type:
System.IO.FileInfo


Say we want to get to these properties and see the fullname and lastwrittentime  of the first two objects (items) in that directory? We can do the following:

```
get-childitem c:\windows\temp | select-object fullname, lastwrittentime -first 2
```

and we get back this:

```
FullName                                                                                       LastWriteTime        
--------                                                                                       -------------        
C:\Users\weiyentan\AppData\Local\Temp\appInsights-nodeAIF-444c3af9-8e69-4462-ab49-4191e6ad1916 4/01/2020 11:55:35 PM
C:\Users\weiyentan\AppData\Local\Temp\chrome_drag10112_1246353191                              4/01/2020 10:55:22 AM
```

#### Methods
Methods are the doing things that you can perform against the object. 

Lets take the previous example of querying the c:\windows\temp directory but this time we ask for the methods that we use against that object.

```
get-childitem c:\windows\temp | Get-Member -MemberType methods
```

we get back:

```


   TypeName: System.IO.DirectoryInfo

Name                      MemberType Definition                                                                                                
----                      ---------- ----------                                                                                                
Create                    Method     void Create(), void Create(System.Security.AccessControl.DirectorySecurity directorySecurity)             
CreateObjRef              Method     System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)                                           
CreateSubdirectory        Method     System.IO.DirectoryInfo CreateSubdirectory(string path), System.IO.DirectoryInfo CreateSubdirectory(str...
Delete                    Method     void Delete(), void Delete(bool recursive)                                                                
EnumerateDirectories      Method     System.Collections.Generic.IEnumerable[System.IO.DirectoryInfo] EnumerateDirectories(), System.Collecti...
EnumerateFiles            Method     System.Collections.Generic.IEnumerable[System.IO.FileInfo] EnumerateFiles(), System.Collections.Generic...
EnumerateFileSystemInfos  Method     System.Collections.Generic.IEnumerable[System.IO.FileSystemInfo] EnumerateFileSystemInfos(), System.Col...
Equals                    Method     bool Equals(System.Object obj)                                                                            
GetAccessControl          Method     System.Security.AccessControl.DirectorySecurity GetAccessControl(), System.Security.AccessControl.Direc...
GetDirectories            Method     System.IO.DirectoryInfo[] GetDirectories(), System.IO.DirectoryInfo[] GetDirectories(string searchPatte...
GetFiles                  Method     System.IO.FileInfo[] GetFiles(string searchPattern), System.IO.FileInfo[] GetFiles(string searchPattern...
GetFileSystemInfos        Method     System.IO.FileSystemInfo[] GetFileSystemInfos(string searchPattern), System.IO.FileSystemInfo[] GetFile...
GetHashCode               Method     int GetHashCode()                                                                                         
GetLifetimeService        Method     System.Object GetLifetimeService()                                                                        
GetObjectData             Method     void GetObjectData(System.Runtime.Serialization.SerializationInfo info, System.Runtime.Serialization.St...
GetType                   Method     type GetType()                                                                                            
InitializeLifetimeService Method     System.Object InitializeLifetimeService()                                                                 
MoveTo                    Method     void MoveTo(string destDirName)                                                                           
Refresh                   Method     void Refresh()                                                                                            
SetAccessControl          Method     void SetAccessControl(System.Security.AccessControl.DirectorySecurity directorySecurity)                  
ToString                  Method     string ToString()                                                                                         


   TypeName: System.IO.FileInfo

Name                      MemberType Definition                                                                                                
----                      ---------- ----------                                                                                                
AppendText                Method     System.IO.StreamWriter AppendText()                                                                       
CopyTo                    Method     System.IO.FileInfo CopyTo(string destFileName), System.IO.FileInfo CopyTo(string destFileName, bool ove...
Create                    Method     System.IO.FileStream Create()                                                                             
CreateObjRef              Method     System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)                                           
CreateText                Method     System.IO.StreamWriter CreateText()                                                                       
Decrypt                   Method     void Decrypt()                                                                                            
Delete                    Method     void Delete()                                                                                             
Encrypt                   Method     void Encrypt()                                                                                            
Equals                    Method     bool Equals(System.Object obj)                                                                            
GetAccessControl          Method     System.Security.AccessControl.FileSecurity GetAccessControl(), System.Security.AccessControl.FileSecuri...
GetHashCode               Method     int GetHashCode()                                                                                         
GetLifetimeService        Method     System.Object GetLifetimeService()                                                                        
GetObjectData             Method     void GetObjectData(System.Runtime.Serialization.SerializationInfo info, System.Runtime.Serialization.St...
GetType                   Method     type GetType()                                                                                            
InitializeLifetimeService Method     System.Object InitializeLifetimeService()                                                                 
MoveTo                    Method     void MoveTo(string destFileName)                                                                          
Open                      Method     System.IO.FileStream Open(System.IO.FileMode mode), System.IO.FileStream Open(System.IO.FileMode mode, ...
OpenRead                  Method     System.IO.FileStream OpenRead()                                                                           
OpenText                  Method     System.IO.StreamReader OpenText()                                                                         
OpenWrite                 Method     System.IO.FileStream OpenWrite()                                                                          
Refresh                   Method     void Refresh()                                                                                            
Replace                   Method     System.IO.FileInfo Replace(string destinationFileName, string destinationBackupFileName), System.IO.Fil...
SetAccessControl          Method     void SetAccessControl(System.Security.AccessControl.FileSecurity fileSecurity)                            
ToString                  Method     string ToString()    
```

In this case lets say we want to delete the files in that directory using methods.

We can do this like so:
```
(get-childitem c:\windows\temp -File | select -first 4).delete()
```
This will get the files in c:\windows\temp and the first five items. Then I created an object of the results by wrapping () around them. These are called parentheticals.

Then I executed the delete method against the group of objects.

Note that running the methods against files in this particular way is a bit redundant because there is a better native way of doing it and that is:
```
get-childitem c:\windows\temp -File | select -first 4 | remove-item
```
I'll go over the reasons why in a later post.

I hope this gives you an idea on what to learn first. 
If you have any further questions feel free to post that in the comments below and I will answer when I have the time.


In the next post I will be discussing more on how to navigate through objects and the commands associated with them.
