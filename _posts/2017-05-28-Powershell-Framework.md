---
layout: post
title: "PowerShell Framework"
downloadname: "Powershell-Template"
downloadlink: "https://github.com/n3rden/Powershell-Template"
author: "Aiden Vaines"
categories: powershell
tags: [powershell, script, lab]
image:
  feature: psh-framework.png
---

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Writing code that can be maintained by multiple people always requires some sort of structure. A lot of the frameworks I have seen are overly complicated for what is still essentially quite small scripts. So I decided to build my own.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">My requirements were for a small, extensible framework I can use for quick scripts, everything I found online was overly complicated. All I needed was a simple framework so all my scripts are a similar format, work in a similar way, can be moved around without too much fuss and can be easily debugged by someone else should they have issues.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">To start off I asked myself what I want;<br /> </span>

  1. <span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;"><strong>Simplicity</strong>; I am not a programmer, I dabble & automate, that&#8217;s it. This needs to be simple to read, reproduce and trace issues.<br /> </span>
  2. <span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;"><strong>Troubleshooting; </strong>Tracing a script that&#8217;s run automatically in the background that failed should be just as easy to trace the fault as if I were stepping through the code. Logging will need to be important<strong><br /> </strong></span>
  3. <span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;"><strong>Modular</strong>; I want to be able to write other modules that I can just slot in when needed. Re-usable parts from other scripts without having to rewrite bits. Things like a module for connecting to Office 365 or a suite of functions I use often.<strong><br /> </strong></span>
  4. <span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;"><strong>Consistency</strong>: Scripts I have written two weeks ago can be difficult follow, one I wrote last year will be unrecognisable. Structure and framework will be important, I should be able to pick up any script and be able to roughly follow it because they all look and feel the same.<br /> </span>

<span style="font-size: 12pt;"><strong><span style="font-family: helvetica, arial, sans-serif;">Here it is:</span></strong></span>
  
 <span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"><a class="github-button" href="https://github.com/n3rden/Powershell-Template" data-size="large">Download Powershell-Template</a></span>
  
 <span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"></span>

<p style="margin-left: 27pt;">
  <span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;">
  <span style="color: black;">Structure for the framework looks like this:<br /> 
  <img src="/assets/img/psh-framework-structure.png" alt="" data-recalc-dims="1" />
  <span style="color: black;"><br /> </span>
</p>

<span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"><img src="https://i2.wp.com/vaines.org/wp-content/uploads/2017/05/052817_1153_PowerShellF2.png?w=640" alt="" align="left" data-recalc-dims="1" /><span style="color: black;">I&#8217;ve tried to keep things separated so they are easy to locate, easy to modify and easy to follow.<br /> </span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The &#8220;Verb-Driver.ps1&#8221; file is the work-horse of the framework, this is the file you run to execute the script or schedule a task to run.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The driver will set up the script environment by calling functions or initializing things.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Firstly the config file will be loaded. The <em>Config.ps1</em> file by default contains the log folder, naming structure and file name format, it should also contain any variables which may be used every time the script is run.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">This could be done with command line arguments, however if every time I run a script I have to enter a particular argument, that&#8217;s a waste of time. If the script runs as a scheduled task, it&#8217;ll be easier to update the config file than update the scheduled task.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Secondly the Logging module is loaded and initialised, this will trigger the log folder to be created and a log file to be created. If the folder and log file already exist the log file will be appended.<br /> </span><img class="" src="/assets/img/psh-framework-flow.png"" alt="" data-recalc-dims="1" />

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Next, any other modules should be loaded (in the template&#8217;s default state the &#8220;Sample-Functions.ps1&#8221;).<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">If any of these three sections are to fail for any reason they script will terminate and to avoid causing any potential damage.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Once the script is initialised and ready to perform custom actions the main script block can be run, safe in the knowledge that modules are loaded, the script is logging and config files are loaded.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The main script block is where the specific code for the project/tasks goes, this may be processing data from the config file, processing CSV files and other data and exporting some sort of result.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">When writing a main script block use the following structure to write messages to the log file.<br /> </span>

<span style="color: #979797; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Log-write -logpath <span style="color: #569cd6;">$Script<span style="color: #979797;">:<span style="color: #72a1ba;">LogPath<span style="color: #979797;"> -linevalue <span style="color: #cb8f76;">&#8220;A message&#8221;<span style="color: black;"><br /> </span></span></span></span></span></span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">In it&#8217;s default state these messages will be written out to console as well as the file. This can be altered in the config file by changing the &#8220;<span style="color: #569cd6;">$Script<span style="color: #979797;">:<span style="color: #72a1ba;">LoggingDebug</span></span><span style="color: black;">&#8221; variable to <span style="color: #569cd6;">$false<span style="color: black;">.<br /> </span></span></span></span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The resultant log file will look something like this:<br /> </span>

<span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"><img src="/assets/img/psh-framework-screen1-log.png" alt="" data-recalc-dims="1" /><span style="color: black;"><br /> </span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The output on the PowerShell console will show the same information<br /> </span>

<span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"><img src="/assets/img/psh-framework-screen2-psh.png" alt="" data-recalc-dims="1" /><span style="color: black;"><br /> </span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">Once the Main script block has completed, any session variables will be cleaned up and the log file finished off.<br /> </span>

<span style="font-family: helvetica, arial, sans-serif; font-size: 12pt;"><img src="/assets/img/psh-framework-screen3-logend.png" alt="" data-recalc-dims="1" /><span style="color: black;"><br /> </span></span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">In future I would like to include some sort of unit testing, perhaps with Pester or something similar but I am still trying to learn this so it might be a while.<br /> </span>

<span style="color: black; font-family: helvetica, arial, sans-serif; font-size: 12pt;">The logging script is cobbled together and cribbed from scripts found online. I want to re-write this to be more efficient for how I use it. I would also like to add a syslog option which would be useful for running scripts on schedule.<br /> </span>
<br>
<span style="color: black; font-size: 12pt; font-family: helvetica, arial, sans-serif;"><em><strong>NOTE:</strong><br /> </em></span><span style="color: black; font-size: 12pt; font-family: helvetica, arial, sans-serif;"><em>PowerShell below version 4 might have issues using the framework<br /> </em></span>
