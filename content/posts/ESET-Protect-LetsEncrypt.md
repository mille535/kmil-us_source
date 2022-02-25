---
title: "Setup ESET Protect Server to use LetsEncrypt Certificates"
date: 2022-02-23T11:58:27-06:00
# weight: 1
# aliases: ["/first"]
author: "Kirk"
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "ESET Protect version 9.0.10.3 running on Microsoft Windows Server 2019"
canonicalURL: "https://blog.kmil.us/posts/eset-protect-letsencrypt/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
categories: [Let’s Encrypt]
tags: [Windows Server, Certbot, Let’s Encrypt, ESET, Powershell, Apache Tomcat, SSL]
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
Create directories for the ACME challenge to be presented.

{{< highlight powershell >}}
New-Item "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\docbase\.well-known" -ItemType Directory  
{{< /highlight >}}

Create a folder to store our certificate files.

{{< highlight powershell >}}
New-Item "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\acme-ssl" -ItemType Directory  
{{< /highlight >}}

Take a backup of the stock Tomcat configuration file.
{{< highlight powershell >}}
Copy-Item "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml" "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml.orig.bak"
{{< /highlight >}}

Add the following line the the end of the `<Host>` section of Tomcat config file: `C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml`

{{< highlight xml >}}
<Context docBase="C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\docbase\.well-known" path="/.well-known" />
{{< /highlight >}}

Restart the Tomcat Service

{{< highlight powershell >}}
Restart-Service Tomcat9
{{< /highlight >}}

Download and install the latest version of [Certbot](https://certbot.eff.org). At this time the Windows client is still in beta. 

After Certbot has been installed issue the first certificate, replace **protect.contoso.com** with your hostname. 

{{< highlight bat >}}
certbot certonly --key-type=ecdsa --webroot -w "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\docbase" -d protect.contoso.com
{{< /highlight >}}

The command terminal should indicate whether the cert was issues successfully, if errors occurect check the certbot logs. The default location for certbot logs is `C:\Certbot\log`. The newly minted live certs can be found at `C:\Certbot\live`.

Next copy the live `cert.pem`, `chain.pem`, and `privkey.pem` to the `C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\acme-ssl` directory created earlier. Remember to change the domain name in the path from **protect.contoso.com** to the hostname used in the certbot cert request.

{{< highlight powershell >}}
Copy-Item "C:\Certbot\live\protect.contoso.com\cert.pem" "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\acme-ssl\cert.pem" -Force
Copy-Item "C:\Certbot\live\protect.contoso.com\privkey.pem" "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\acme-ssl\privkey.pem" -Force
Copy-Item "C:\Certbot\live\protect.contoso.com\chain.pem" "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\acme-ssl\chain.pem" -Force
{{< /highlight >}}

Open the `C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml` configuration file and comment out or remove the `<Connector>` line that begins with `<Connector server="OtherWebServer" port="443"...` and add connector statements below.

{{< highlight xml >}}
<Connector server="OtherWebServer" port="443" protocol="org.apache.coyote.http11.Http11NioProtocol" maxThreads="150" SSLEnabled="true" secure="true" scheme="https">
    <SSLHostConfig protocols="TLSv1.2,TLSv1.3" honorCipherOrder="true" clientAuth="false" ciphers="TLS_AES_256_GCM_SHA384, TLS_CHACHA20_POLY1305_SHA256, TLS_AES_128_GCM_SHA256, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_DHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256, TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256, TLS_DHE_RSA_WITH_AES_128_GCM_SHA256, TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256, TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256">
            <Certificate certificateFile="acme-ssl\cert.pem" certificateKeyFile="acme-ssl\privkey.pem" certificateChainFile="acme-ssl\chain.pem" />
    </SSLHostConfig>
</Connector>
{{< /highlight >}}

Here is the full and final `server.xml` file for Tomcat. 

{{< gist mille535 20d7894f363d8791844fe71265ec96f3 >}}

Verify that everything is working and, if so, make another backup of the Tomcat `server.xml` in case a future update overwrites the customized configuration file. 

{{< highlight powershell >}}
Copy-Item "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml" "C:\Program Files\Apache Software Foundation\apache-tomcat-9.0.54\conf\server.xml.prod.bak"
{{< /highlight >}}

Certbot should have already setup its own scheduled task to check and renew certificates automatically. I have created the script below to check the local cerbot store and update Tomcat if the live certificates have changed. Add the script below to `c:\scripts\certchk.ps1` and add a daily job in Task Scheduler to run it automatically. **change protect.contoso.com** to the domain for your environment. 

{{< gist mille535 66cadbb5b3f09d402e4054d5c7e107bc >}}

All Done!

{{< figure src="/images/2022/02/tomcatsslreport.png" title="SSL Labs Grade" >}}