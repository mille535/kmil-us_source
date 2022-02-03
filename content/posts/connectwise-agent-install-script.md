---
title: "ConnectWise Agent Install Script"
date: 2022-02-01T11:58:27-06:00
draft: false
---
One would think working in IT would make me a patient person. Today I found myself tired of waiting for the ConnectWise Automate agents to be deployed for a customer. Normally I push these agent-type installs using group policy which is fine in most cases. 

While there are probably better deployment methods outside of what [ConnectWise recommends](https://docs.connectwise.com/ConnectWise_Automate_Documentation/040/050) I figured I'd put together a down and dirty script to speed things up for this particular install. 

For this particular install the customer has an Active Directory domain so I will be enumerating computer objects from AD and then using PSRemoting to execute commands on the client computers. 

Simply edit the AD query on line 2 to suit the needs of your environment and populate the CW Agent download link on line 8. 

Disclaimer: Use this code at your own risk. 

{{< gist mille535 25419ed2eb3a7c26d4e9c9de5da66b52 >}}

After writing this script I got curios to see what others have been doing and found the links below to interesting scripts.

 - [The Lazy Administrator](https://www.thelazyadministrator.com/2019/04/30/deploy-connectwise-automate-formerly-labtech-agent-remotely-and-quietly-with-powershell/)
 - [LabTech Consulting GitHub](https://github.com/LabtechConsulting/LabTech-Powershell-Module/blob/master/LabTech/Install-LTService.md)

