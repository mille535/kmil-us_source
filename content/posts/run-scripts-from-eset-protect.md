---
title: "Run Powershell scripts from ESET Protect"
date: 2022-02-24T06:58:27-06:00
# weight: 1
# aliases: ["/first"]
author: "Kirk"
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Using client tasks on managed clients"
canonicalURL: "https://blog.kmil.us/posts/run-scripts-from-eset-protect/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
categories: [ESET]
tags: [ESET, Powershell]
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
Admittedly this one is a bit of a hack, but I thought it was kind of fun. ESET Protect has the facility to run commands on remote computers. From what I have found is that on Windows it will not natively run Powershell cmdlets, only the basic cmd type stuff. I know that most of what can be done in Powershell can be done in cmd but in this day and age it just feels wrong. There are obviously many different use cases for this but for my proof of concept I just created a basic script to install VLC player just to see if it would work. 

Essentially what it boils down to is ECHO’ing your Powershell script out to a .PS1 file and then calling that script using `powershell.exe`. Below is my example script that installs VLC. 

```cmd
ECHO [String]$downloadlink = 'https://mirrors.ocf.berkeley.edu/videolan-ftp/vlc/3.0.16/win64/vlc-3.0.16-win64.exe' > %temp%\Install-VLC.ps1
ECHO [string]$OutFile = 'vlc-3.0.16-win64.exe' >> %temp%\Install-VLC.ps1
ECHO [string]$instargs = '/L=1033 /S' >> %temp%\Install-VLC.ps1
ECHO $OutPath = $env:TMP >> %temp%\Install-VLC.ps1
ECHO Invoke-WebRequest -Uri $downloadlink -OutFile $OutPath\$OutFile -ErrorAction SilentlyContinue >> %temp%\Install-VLC.ps1
ECHO Start-Process -NoNewWindow -FilePath $OutPath\$OutFile -ArgumentList $instargs -Wait -ErrorAction SilentlyContinue >> %temp%\Install-VLC.ps1

%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -NoProfile -NoLogo -NonInteractive -ExecutionPolicy Bypass -File %temp%\Install-VLC.ps1
```

From the ESET Protect interface click on `Tasks`, then `New`, and finally `Client Task`. For Task Category choose `Operating System` and for Task select `Run Command` and then click `Continue`.

{{< figure src="/images/2022/02/esets1.png" >}}

On the next page paste your cleverly disguised Powershell script into the `Command line to run` box. 

{{< figure src="/images/2022/02/esets2.png" >}}

From there you handle the task like any other on ESET Protect creating a trigger and monitoring from the `Tasks` section. 