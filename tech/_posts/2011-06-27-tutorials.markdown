---
author: gvivekcbe
comments: true
date: 2011-06-27 15:08:07+00:00
layout: post
slug: tutorials
title: Powershell
wordpress_id: 166
---

Powershell is coolest thing ever.

**Invoke-expression:**

[sourcecode language="Powershell"]
param($version)
if ($version)
{
$cmd = "C\"+$version+"\bin\Sample.exe "+$args
echo $cmd
Invoke-expression $cmd
}
else
{
G:\\latest\bin\Sample.exe $args
}
[/sourcecode]


### **START-JOB:**


[sourcecode language="Powershell"]
start-job Script.ps1 -ArgumentList $args[0]
[/sourcecode]


### **SORT ITEMS BY DATE AND COPY THEM:**


[sourcecode language="Powershell"]
$path = "\\vivek\"+$args+"*"
$a = get-childItem $path | sort -prop LastWriteTime | select -last 1 | %{$_.fullName}
if ( $args )
{
$destPath = "G:\"+$args
if ((Test-Path -Path $destPath -PathType Container) -ne $True)
{
New-Item $destPath -type directory
}
}
else
{
$destPath = "G:\"
}

echo $a
echo "\n"
echo $destPath
robocopy.exe $a $destPath /E
[/sourcecode]
