---
layout: post
title: "Template Module | Connect-AD"
downloadname: "Template module | Connect-AD"
downloadlink: "https://github.com/n3rden/Powershell-Template-Modules/tree/master/Connect-AD"
author: "Aiden Vaines"
categories: powershell
tags: [powershell, script, pstemplate, module]
image:
  feature: psh-tmp-adconnect.jpeg
---
One of the most common things I use Powershell for requires the ActiveDirectory module. In its self, this isnt an issue, I know I have it installed and that it will import automatically. The problem comes when sending or transfering the script to someone else.

If the device running the script doesnt have some modules installed or available a script will fall-over.

This script simply puts some belts and braces around importing the ActiveDirectory module inline with the [Powershell Template](http://vaines.org/powershell/Powershell-Framework.html) 

 
# Setup
* Create a project using the [Powershell Template](http://vaines.org/powershell/Powershell-Framework.html) 
* Clone the [Connect-AD1.ps1](https://github.com/n3rden/Powershell-Template-Modules/blob/master/Connect-AD/Connect-AD.ps1) to the "Modules" folder of the template
*  When the "Driver.ps1" is run, it will dot-source the Connect-AD functions and check for an RSAT installation then attempt to import the ActiveDirectory module


# How does it work?
The script will check for an RSAT installation


{% mermaid %}

graph LR
    A{ActiveDirectory Imported?}
        A -->|Yes| C[Done]
        A -->|No| D{Is RSAT Installed?}
            D-->|Yes| F[Try Import Module]
            D -->|No| I[!error, RSAT not installed]
                F -->|Success|G[Done]
                F-->|Fail|H[Abort script]
           I --> H
{% endmermaid %}

The logs produced should look like this
```
*************************************************************
Started logging at [11/10/2017 15:46:01].
*************************************************************
*************************************************************

[11/10/2017 15:46:01] Loading Module: Connect-AD
[11/10/2017 15:46:01] 	Importing AD Modules
[11/10/2017 15:46:01] Checking for ActiveDirectory Module
[11/10/2017 15:46:01] 		ActiveDirectory module is already loaded
...
```