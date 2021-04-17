---
title:  "Ansible - PowerShell on Linux using Legacy modules and helper functions "
comments: true
date:   2021-04-17 19:34:23
categories: [ansible]
tags: [howto]
---

Ansible at its core is essentially executing code in a specific way to a machine. The scripts that are written are to a standard that makes it idempotent. The concept is quite simple and once you do it once or twice it is actually easy to understand how to make scripts idempotent. However this is another topic on its own.

A while back I watched a Youtube video at a PowerShell summit called  ["Ansible for the Windows Admin"](https://youtu.be/ZI20Y10OKd0?t=4812) by [Jeremy Murrah](https://twitter.com/JeremyMurrah). At this part of the segment he mentioned that we can use PowerShell on Linux but if we wanted to write a module we could ln powershell to the  the /user/bin/pwsh ... although we found out recently that did not work.

I thought about this and thought I would park it until now. I read this [issue response](https://github.com/ansible/ansible/issues/48881#issuecomment-440481672) in regards to a problem with PowerShell escaping a character and decided to revisit .
Here is what I found:

 Ansible essentially can run any script as long as you declare Json output. However You stil have to give it the shell to execute using a shebang.

 Module_Utils does not work (and that means the legacy ones). Ansible Windows Modules uses #Requires and the moment I used it started to look for PowerShell. I tried using ln method but it came up with other errors so I did not bother to try and go further down this path. What this means is that any shared and or custom funtion cannot be included in the module_utils.

 However there is  good news in all of this. All the relevant functions that are needed does work that are necessary for module development does work. These include:

 * Parse-Args
 * Fail-Json
 * Exit-Json
 * Get-AnsibleParams
 * Set-Attr
 *  Convert-Boolean

Jeremy Murrah did mention that one of the functions was bugged and upon further investgation found out which cmdlet was failing. It was coming up with errors something along the lines of extra information provided.  This was Parse-Args which brings in the arguments. 

So I inspected the code:
```
Function Parse-Args($arguments, $supports_check_mode = $false)
{
	$params = New-Object psobject
	If ($arguments.Length -gt 0)
	{
		$params = Get-Content $arguments[0] | ConvertFrom-Json
	}
	Else
	{
		$params = $complex_args
	}
	$check_mode = Get-AnsibleParam -obj $params -name "_ansible_check_mode" -type "bool" -default $false
	If ($check_mode -and -not $supports_check_mode)
	{
		Exit-Json @{
			skipped = $true
			changed = $false
			msg	    = "remote module does not support check mode"
		}
	}
	return $params
}
```

Upon closer inspection I got drawn to the way that it was outputting to the pipeline. And as we all know using return in the pipeline for objectscan bring a lot of disasters.

So I changed the code to look like this:

```
Function Parse-Args($arguments, $supports_check_mode = $false)
{
	$params = New-Object psobject
	If ($arguments.Length -gt 0)
	{
		$params = Get-Content $arguments[0] | ConvertFrom-Json
	}
	Else
	{
		$params = $complex_args
	}
	$check_mode = Get-AnsibleParam -obj $params -name "_ansible_check_mode" -type "bool" -default $false
	If ($check_mode -and -not $supports_check_mode)
	{
		Exit-Json @{
			skipped = $true
			changed = $false
			msg	    = "remote module does not support check mode"
		}
	}
	Write-Output $params
}
```
When i ran the playbook again I got the result I wanted. I tested failifempty parameter in the Get-AnsibleParam and it worked. So I am surmising that check_mode will work too. It's a pity that module_utils don't work the way that they should but that shouldnt stop anyone from automating using PowerShell on Linux with the native Ansible PowerShell cmdlets.

I hope this has helped someone who wants to try and use PowerShell to automate on Linux. The full code of the library file can be found [here](https://raw.githubusercontent.com/weiyentan/ansible_helper_functions/main/testoutput.ps1).

Happy Automating!