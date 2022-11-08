---
title: "Expand RAID array on HPE ESXi host"
date: 2022-10-12T18:23:44-05:00
draft: false
# weight: 1
# aliases: ["/first"]
author: "Kirk"
showToc: false
TocOpen: false
hidemeta: false
comments: false
description: "without reboot, using command line tools"
canonicalURL: "https://blog.kmil.us/posts/hp-esxi-expand-array/"
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
categories: [VMware]
tags: [VMware, ESXi, HPE, RAID, p408i]
#cover:
#    image: "<image path/url>" # image path/url
#    alt: "<alt text>" # alt text
#    caption: "<text>" # display caption under cover
#    relative: false # when using page bundles set this to true
#    hidden: true # only hide on current single page
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---
**WARNING: Before doing any work on a RAID array it’s very important to ensure that there is a current verified backup and that both the controller and array are in a healthy state.**

I will be adding a drive to an existing RAID 5 array. The host is an HPE ProLiant DL380 Gen10 with the Smart Array P408i-a with VMware ESXi 7.0.3 as the operating system. 

To manipulate the array without having to reboot the server I will be using HPE’s Smart Storage Administrator CLI. HPE offers custom ESXi images that include this tool (and more!) which I use on all new server builds. HPE also provides the VIB files to install their tools on existing servers that were set up with the stock VMware ESXi ISO. 

Without further ado, let’s get into it! Start by temporarily enabling SSH on your ESXi host and connect with your favorite SSH client.  

`/opt/smartstorageadmin/ssacli/bin/ssacli ctrl all show status` - Show all controllers in a system (used to get the ctrl slot=0)

[![HPE controller status](1-crtlstatus.png)](1-crtlstatus.png)

`/opt/smartstorageadmin/ssacli/bin/ssacli ctrl slot=0 show config` - Show arrays and status for a given controller. (Used to get the Array A & also take note of initial array size)

[![HPE controller config](2-crtlcfg.png)](2-crtlcfg.png)

`/opt/smartstorageadmin/ssacli/bin/ssacli ctrl slot=0 Array A add drives=allunassigned forced` - Add all unassigned drives to Array A in slot 0

[![Expand command](3-expand.png)](3-expand.png)

`/opt/smartstorageadmin/ssacli/bin/ssacli ctrl slot=0 show config` - take note of transforming 

[![HPE array transform](4-transform.png)](4-transform.png)

`/opt/smartstorageadmin/ssacli/bin/ssacli ctrl slot=0 ld 1 modify size=max forced` - Once transform is complete and status back to OK issue this to max the size.

[![Complete](5-complete.png)](5-complete.png)

All done! Take note of the final size.


References:

[https://www.hpe.com/us/en/servers/hpe-esxi.html](https://www.hpe.com/us/en/servers/hpe-esxi.html)