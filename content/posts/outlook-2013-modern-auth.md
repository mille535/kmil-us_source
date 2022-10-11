---
title: "Enable Modern Authentication for Outlook 2013"
date: 2022-10-10T18:23:44-05:00
draft: false
# weight: 1
# aliases: ["/first"]
author: "Kirk"
showToc: false
TocOpen: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.kmil.us/posts/outlook-2013-modern-auth/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
categories: [Windows]
tags: [Office 365, Microsoft 365, Microsoft, Office, Outlook, Powershell, registry]
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
Hopefully your organization has already tested and enabled Modern Authentication for Office 365 as October is the month that Microsoft will be disabling basic authentication. 

A common issue when changing from basic to modern auth is that sometimes the client doesn’t want to switch. A person could certainly setup an entirely new Outlook profile, but it may be easier to use the Windows Registry to force Outlook to use modern auth. 

I had a customer call in recently with this exact issue who is using Outlook 2013 which is also going to be EoL next year (2023). So, I wrote a quick PS script to put into out RMM system to force Outlook to use modern auth and though I’d share.

This script will only work with Outlook 2013 as subsequent versions have different registry names for these settings. Also, please be sure modern auth is already enabled in you 365 tenant prior to making these changes. Eventually I’d like to build this out to detect Outlook versions and make changes based on that but this will do for now. 

```
$RegPath = "HKCU:\Software\Microsoft\Exchange"
$Name = "AlwaysUseMSOAuthForAutoDiscover"
$Value = "1"

New-ItemProperty -Path $RegPath -Name $Name -Value $Value -PropertyType DWORD -Force | Out-Null 

$RegPath = "HKCU:\Software\Microsoft\Office\15.0\Common\Identity"
$Name = "EnableADAL"
$Value = "1"

New-ItemProperty -Path $RegPath -Name $Name -Value $Value -PropertyType DWORD -Force | Out-Null 

$Name = "Version"
$Value = "1"

New-ItemProperty -Path $RegPath -Name $Name -Value $Value -PropertyType DWORD -Force | Out-Null 
```

References
[Deprecation of Basic authentication in Exchange Online](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/deprecation-of-basic-authentication-exchange-online)

[How modern authentication works for Office 2013, Office 2016, and Office 2019 client apps](https://learn.microsoft.com/en-us/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016?view=o365-worldwide)

[Enable Modern authentication for Office 2013 on Windows devices](https://learn.microsoft.com/en-us/microsoft-365/admin/security-and-compliance/enable-modern-authentication?view=o365-worldwide)