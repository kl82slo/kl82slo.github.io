---
layout: post
title: Manually trigger compliance check in MicroStrategy License Manager
tags:
- Microstrategy
- Compliance check
- License Manager
comments: true
---
Dificulty ★☆☆☆☆

![License_manager1](/img/20210620_0007/License_manager1.png)

and you have already fixed the issue but the error remains. (pictures are from different error) <br />
![compliance](/img/20210620_0007/compliance_hidden.png)

![compliance1](/img/20210620_0007/compliance1.png)

Then open 'Licence manager' <br />
![Open_licence_manager](/img/20210620_0007/Open_licence_manager.png)

Go to 'Audit' <by /> 
Double click Intelligence and log into server <br /> 
and click 'Compliance Check' <be />
![Open_licence_manager](/img/20210620_0007/Compliance_Check_hiden.png)

If you get notification like <br />
![Open_licence_manager](/img/20210620_0007/Licence_correct.png)
Then all you have to do is restart WEB server (IIS or tomcat) <br />

If not and you don't know which privilege you need to remove check <br />
[Privileges by License Type](https://www2.microstrategy.com/producthelp/current/Workstation/WebHelp/Lang_1033/Content/privileges_by_license_type.htm)
