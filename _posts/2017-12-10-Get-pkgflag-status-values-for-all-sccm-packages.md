---
layout: post
title: "Script to get PkgFlag status values for all SCCM packages"
author: "Aiden Vaines"
categories: [powershell, sccm]
tags: [powershell, sccm]
image:
  feature: sccm-pkgflags.jpg
---

# Problem
As an extension of the [script to get the binary differential replication status of all sccm packages,]({{ site.github.url }}/powershell/sccm/Get-all-sccm-packages-Binary-Diff.html) when looking for the Binary Differential Replication flag being checked, I thought it would be worth looking for the other values that make up the PkgFlags attribute.

Microsoft provided a list of these [here on MSDN](https://msdn.microsoft.com/library/hh469117.aspx).


| Hexadecimal (Bit) | Description |
|---|:---|
| 0x0100011 (23) | DO_NOT_ENCRYPT_CONTENT_ON_CLOUD. Do not encrypt content on the cloud. This information applies to System Center 2012 Configuration Manager SP1 or later, and System Center 2012 R2 Configuration Manager or later. |
| 0x01000000 (24) | DO_NOT_DOWNLOAD. Do not download the package to branch distribution points, as it will be pre-staged. |
| 0x02000000 (25) | PERSIST_IN_CACHE. Persist the package in the cache. |
| 0x04000000 (26) | USE_BINARY_DELTA_REP. Marks the package to be replicated by distribution manager using binary delta replication. |
| 0x10000000 (28) | NO_PACKAGE. The package does not require distribution points. |
| 0x20000000 (29) | USE_SPECIAL_MIF. This value determines if Configuration Manager uses MIFName, MIFPublisher, and MIFVersion for MIF file status matching. Otherwise, Configuration Manager uses Name, Manufacturer, and Version for status matching. For more information, see the Remarks section later in this topic. |
| 0x40000000 (30) | DISTRIBUTE_ON_DEMAND. The package is allowed to be distributed on demand to branch distribution points. |


# Solution
I extended to the code to support these additional attributes.

It works exactly the same was as [previous version of this routine]({{ site.github.url }}/powershell/sccm/Get-all-sccm-packages-Binary-Diff.html) other than it now checks for the other possibilities that make up “PkgFlags” and adds the extra attributes to the object for each package.


{% highlight powershell %}
    $Status = @()

    get-cmpackage | where-object {$_.SourceSite -eq "UR2"} | foreach {
        $CMPackage = New-Object System.Object

        $CMPackage | Add-Member -type NoteProperty -name Name -value $_.name
        $CMPackage | Add-Member -type NoteProperty -name Manufacturer -value $_.Manufacturer
        $CMPackage | Add-Member -type NoteProperty -name PackageID -value $_.PackageID

        write-host "`tChecking" $_.name "(" $_.PackageId ")"

        #DO_NOT_ENCRYPT_CONTENT_ON_CLOUD/0x0100011 (23)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x0100011 )) {
        $CMPackage | Add-Member -type NoteProperty -name DO_NOT_ENCRYPT_CONTENT_ON_CLOUD -value "True"
        }else{    
        $CMPackage | Add-Member -type NoteProperty -name DO_NOT_ENCRYPT_CONTENT_ON_CLOUD -value "False"
        }

        #DO_NOT_DOWNLOAD/0x01000000 (24)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x01000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name DO_NOT_DOWNLOAD -value "True"  
        }else{
        $CMPackage | Add-Member -type NoteProperty -name DO_NOT_DOWNLOAD -value "False"
        }

        #PERSIST_IN_CACHE/0x02000000 (25)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x02000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name PERSIST_IN_CACHE -value "True"    
        }else{      
        $CMPackage | Add-Member -type NoteProperty -name PERSIST_IN_CACHE -value "False"
        }
        
        #USE_BINARY_DELTA_REP/0x04000000 (26)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x04000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name USE_BINARY_DELTA_REP -value "True"    
        }else{      
        $CMPackage | Add-Member -type NoteProperty -name USE_BINARY_DELTA_REP -value "False"
        }

        #NO_PACKAGE/0x10000000 (28)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x10000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name NO_PACKAGE -value "True"    
        }else{      
        $CMPackage | Add-Member -type NoteProperty -name NO_PACKAGE -value "False"
        }

        #USE_SPECIAL_MIF/0x20000000 (29)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x20000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name USE_SPECIAL_MIF -value "True"    
        }else{      
        $CMPackage | Add-Member -type NoteProperty -name USE_SPECIAL_MIF -value "False"
        }
        
        #DISTRIBUTE_ON_DEMAND/0x40000000 (30)
        if ($_.pkgflags -eq ($_.pkgflags -bor 0x40000000 )) {
        $CMPackage | Add-Member -type NoteProperty -name DISTRIBUTE_ON_DEMAND -value "True"    
        }else{      
        $CMPackage | Add-Member -type NoteProperty -name DISTRIBUTE_ON_DEMAND -value "False"
        }
    
        $Status += $CMPackage
    }#EndFor

    $Status | Out-GridView

{% endhighlight %}
