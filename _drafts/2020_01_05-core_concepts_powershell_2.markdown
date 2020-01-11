---
title:  "Core concepts  PowerShell - Workhorse Commands
comments: true
date:   2020-01-05 12:31:23
categories: [howto]
tags: [PowerShell]
---

This is the second part of the blog post series of learning PowerShell.
Before we start on this post there is are two  concepts that I want to define. The first is the term collections.
As previously discussed in the last post PowerShell is object orientated but we not only deal with one object at a time,
we can deal with a lot of objects at a time.

So what is an example of collection?
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

The second is parameters. I covered briefly in the last post. But I am going to discuss it further. in additional to a cmdlet, cmdlets come with parameters.   You can consider them like the switches in other command interpreters like DOS or bash in Linux. The description on how they are used in Get-Help.

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
There is a lot going on with each of these commands so I am just going to cover the basics in this post on how they work. For further information on certain examples you can use Get-Help -full .

### Select-Object
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
displayed those properties  only displaying the first 5.

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

This is what is returned. All of these properties is what you can pass to Select-Object for the cmdlet Get-Childitem. You can also use the unique parameter to limit down duplicate values from a property.  

Another example that will work:

```PowerShell
get-childitem "c:\temp" -File | select-object fullname , length , LastWritetime, LastAccessTime, -Last 5
```

### Where-Object
The cmdlet Where-Object is used for 'filtering'. Ever asked those questions where you might say I only want to see those files that have been written to in the last 10 days? Or I only want to see items with a certain criteria.

In order to make this query PowerShell uses what they call by 'comparison operators' . In pseudo code it will look like this:

Find all the files in the temp directory that is older than 3 days.
In PowerShell it will look like this:
```PowerShell
Get-ChildItem $env:temp -Recurse | Where-Object {$_.lastaccesstime -lt ((Get-date).adddays(-3)}
```

The full list can be found by going to:
```PowerShell
Get-Help about_Comparison_Operators
```

Here is the list:

| Comparison Operator | Description |
| --------------------|------------ |
|   -eq               | This means equal. get-childitem $env:temp | where-object {$_extension -eq .xml}|
|                     | This finds all the files in the $env:temp directory that has the extension xml. One thing to note this absolute. You cannot use wild cards here. If you want to use wildcards you have to use -like|
|   -ne               | This is the opposite of -eq.   get-childitem $env:temp | where-object {$_.extension -ne .xml}|
|                     | This finds all the files in the $env:temp directory that does not have  extension xml. One thing to note this absolute. You cannot use wild cards here. If you want to use wildcards you have to use -like|
|   -gt               | This means greater than . Get-ChildItem $env:temp -Recurse | Where-Object {$_.lastaccesstime -gt ((Get-date).adddays(-3)} |
|                     | These gets the files that have been accessed three days ago. (Get-Date) is an object and we use the method of adddays to add -3 days. PowerShell knows how to use this with  the comparison operators.|
|   -ge               | Greater than or Equals to. This works in much the same way as -gt but includes the value compared against.
|   -lt               | Less than .Its used the same as gt but in reverse.
|   -le               |  Less than or equals to .Its used the same as gt but in reverse.     |
|   -Like             |  Like is similar to -eq but you can use wild cards with like .  
|                     | get-childitem $env:temp | where-object {$_.fullname  -like "*listservers*" }|
|   -NotLike          | Notlike is the opposite of like but you can use it in the same way as like. It will exclude the terms that you put in. |
|   -Match            | This uses regex to match  get-childitem -recurse | where-object {$_.fullname  -match "tables"} |
|                     | This  searches the file listing with get-childitem and finds files with the regex 'tables'|
|   -NotMatch         | This is the opposite of match and removes the  objects that matches the regex. 
|   -Contains         | This expression relates to whether or not an item exists in a collection of items. The idea is that you provide a collection of objects and then detects whether the item exists in the collection
|                     |  ((get-childitem -recurse).name) -contains 'tables.scss' This extrapolates the properties of name in the current directory and detects whether it contains the file tables.scss. If it exists it will show a ||                     |  boolean true or false
|   -NotContains      | This is the opposite of -contains 
|   -In               | This is the same  as contains but its the opposite way of writing. 'tables.scss' -in ((get-childitem -recurse).name) |
|   -NotIn            | Opposite of -In |
|   -Replace          | Replaces a text. 'tables.scss' -replace 'tables.scss', 'test'|
|                     | This will replace the text tables.scss with the word test. |

You can join expressions together with -and or -or.

This is one example of using -and.

```PowerShell
Get-ChildItem $env:temp -Recurse | Where-Object {$_.lastaccesstime -gt (Get-date).adddays(-1) -and $_.extension -like "*.exe*"}
```

### Sort-Group
This cmdlet is used to sort objects. The basic of using this command is like:

```PowerShell
Get-ChildItem $env:temp -Recurse | Sort-Object -property name 
Get-ChildItem $env:temp -Recurse | Sort-Object -property name  -Descending
```

This will sort the collection by the property 'name' and the second line sorts it by descending order.


### Group-Object

This cmdlet will group what you pass it a collection. You name a property that you want to group Example is below:

```PowerShell
Get-ChildItem $env:temp -Recurse | Group-Object extension
```


### Measure-Object
This cmdlet measures the collection that you are passing it over the pipeline.  

```PowerShell 
Get-ChildItem $env:temp -Recurse | Measure-Object
```

This is the result:

```
Count    : 317
Average  : 
Sum      : 
Maximum  : 
Minimum  : 
Property : 
```

## Piecing it all together
So now we have explained how to use these cmdlets at a basic level the question is how can we use these cmdlets together? Before we explore this there are some things that we got to cover. That is filter left and format right.

There is another command that there starts Format*. We will discuss this cmdlet later but for now remember "Filter Left , Format Right"
Let's see an example of how to piece it together:

```PowerShell
Get-ChildItem $env:temp -Recurse | Where-Object {$_.lastaccesstime -lt (Get-date).adddays(-3)}| Sort-Object -property fullname | Select-Object -property fullname , lastaccessdate, extension -first 5 
```

So here is the breakdown of what is happening. We produced a set of objects through Get-Childitem and then we pass that across the pipeline to Where-Object. The object is then passed to within the script block where it is evaluated to see whether it matches the condition in the script block. The object that do not match the comparison will get dropped and the remaining object then get passed across the pipeline to sort-object with the property fullname. The objects then pass down the cmdlet Select-Object where the only properties kept is fullname, lastaccessdate, extension.

```
FullName                                                                                       LastAccessTime         Extension
--------                                                                                       --------------         ---------
C:\Users\weiyentan\AppData\Local\Temp\{0A07A017-27B9-4092-8E64-1A43528BAD74} - OProcSessId.dat 6/01/2020 7:33:17 PM   .dat     
C:\Users\weiyentan\AppData\Local\Temp\{3F44DF31-C470-4A78-9282-F5CBD809B72B} - OProcSessId.dat 22/12/2019 11:07:01 PM .dat     
C:\Users\weiyentan\AppData\Local\Temp\{72FDE0E5-5FB6-4C75-A20D-9BD3E32F0996}.png               6/01/2020 7:16:19 PM   .png     
C:\Users\weiyentan\AppData\Local\Temp\{7BB95D4C-695C-442A-A9E6-2678005C61D6} - OProcSessId.dat 23/12/2019 2:22:26 PM  .dat     
C:\Users\weiyentan\AppData\Local\Temp\{823C1E9F-2BC6-446D-89B4-1FD179F62200} - OProcSessId.dat 18/12/2019 7:39:46 PM  .dat     
```

So why is it important to filter left? Lets see what happens.

```PowerShell
Get-ChildItem $env:temp -Recurse | Select  -property fullname , extension -first 5 | Where-Object {$_.lastaccesstime -lt (Get-date).adddays(-3)}
```

 We are  creating a list of objects and then we select only fullname , lastaccessdate, extension and then pass that to where-object. And thats where it all balks because it can't find the property lastaccesstime

These cmdlets are what you are going to use most of the time. Once you understand how to dissect objects and navigate and filter it will make your journey learning PowerShell a lot easier. Happy PowerShelling!