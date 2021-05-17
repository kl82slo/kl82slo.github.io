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

<br />
<br />
<br />
<br />
<br />
<br /><br />

If you wish to use Workstation to manage standalone filters, facts, attributes, tables, and schema using the Filter Editor, Fact Editor, Attribute Editor, Schema Editor, and access Schema tab to lock, unlock, and reload (update) schema.

Then you need to set up Modeling Service

Warning you need at least version 2021 update 1. If you use 2021 you will get the following error
![2021](/img/20210518_0004/version2021.png)

### Library
The process is the same as in [Single-Sign-On-to-Library](https://kl82slo.github.io/2021/05/16/0003-MicroStrategy-Single-Sign-On-to-Library-with-Trusted-Authentication.html)
and also the secret key that you used there is the same you will need here.

### Modeling servise - No TLS
Go to C:\Program Files (x86)\MicroStrategy\ModelingService\admin

open modelservice.conf.template and save it as modelservice.conf <br />
add line <br />
modelservice.identity_token.secret_key = <font color='red'>secret_key</font>

Open the Windows Service Application, right-click MicroStrategy Modeling Service, and choose Restart.

Chek status in <br />
http://localhost:9500/model/application/health

### Modeling servise - With TLS
Go to C:\Program Files (x86)\MicroStrategy\ModelingService\admin

open modelservice.conf.template and save it as modelservice.conf <br />
add lines <br />
https.port = <font color='red'>modeling_service_https_port_number</font> <br />
modelservice.identity_token.secret_key = <font color='red'>secret_key</font> <br />
modelservice.iserver.tlsEnabled = true <br />
modelservice.trustStore.path= <font color='red'>path/to/trustStore.jks</font> <br />
modelservice.trustStore.passphrase=<font color='red'>passphrase</font>

in tomcat/webapps/MicroStrategyLibrary/WEB-INF/classes/config/configOverride.properties <br />
trustStore.path=<font color='red'>path/to/trustStore.jks</font> <br />
trustStore.passphrase=<font color='red'>passphrase</font> <br />
services.MicroStrategy-Modeling-Service.tlsEnabled = true <br />

Open the Windows Service Application, right-click MicroStrategy Modeling Service, and choose Restart.

Chek status in <br />
http://localhost:<font color='red'>modeling_service_https_port_number</font>/model/application/health

### Aditional read
[Troubleshooting](https://www2.microstrategy.com/producthelp/Current/InstallConfig/en-us/Content/modeling_service_troubleshooting.htm)

