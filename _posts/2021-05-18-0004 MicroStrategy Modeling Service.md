---
layout: post
title: Workstation - Modeling Service
tags:
- Microstrategy
- Modeling Service
- Workstation
comments: true
---
Dificulty ★★☆☆☆




# Post in progress

If you wish to use Workstation to manage standalone filters, facts, attributes, tables, and schema using the Filter Editor, Fact Editor, Attribute Editor, Schema Editor, and access Schema tab to lock, unlock, and reload (update) schema from the main Workstation window.

Then you need to set up Modeling Service

Worning you need at least vesrion 2021 update 1. If you use 2021 you will get the folowing error
[!2021](/img/20210518_0004/version2021.png)

### Library
The proces is the same as in [Single-Sign-On-to-Library](https://kl82slo.github.io/2021/05/16/0003-MicroStrategy-Single-Sign-On-to-Library-with-Trusted-Authentication.html)
and also the secret key that you used there is the same you will need here.

### Modeling servise - No TLS
Go to C:\Program Files (x86)\MicroStrategy\ModelingService\admin

open modelservice.conf.template and save it as modelservice.conf
add line 
modelservice.identity_token.secret_key = <font color='red'>secret_key</font>

Open the Windows Service Application, right-click MicroStrategy Modeling Service, and choose Restart.

Chek status in 
http://localhost:9500/model/application/health

< hide
### Modeling servise - With TLS
Go to C:\Program Files (x86)\MicroStrategy\ModelingService\admin

open modelservice.conf.template and save it as modelservice.conf
add lines
https.port = <font color='red'>modeling_service_https_port_number</font>
modelservice.identity_token.secret_key = <font color='red'>secret_key</font>
modelservice.iserver.tlsEnabled = true
modelservice.trustStore.path= <font color='red'>path/to/trustStore.jks</font>
modelservice.trustStore.passphrase=<font color='red'>passphrase</font>

in tomcat/webapps/MicroStrategyLibrary/WEB-INF/classes/config/configOverride.properties
trustStore.path=<font color='red'>path/to/trustStore.jks</font>
trustStore.passphrase=<font color='red'>passphrase</font>
services.MicroStrategy-Modeling-Service.tlsEnabled = true

Open the Windows Service Application, right-click MicroStrategy Modeling Service, and choose Restart.

Chek status in 
http://localhost:<font color='red'>modeling_service_https_port_number</font>/model/application/health

/hide>

Aditional read
[Troubleshooting](https://www2.microstrategy.com/producthelp/Current/InstallConfig/en-us/Content/modeling_service_troubleshooting.htm)

