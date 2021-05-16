---
layout: post
title: MicroStrategy Single Sign-On to Library with Trusted Authentication
tags:
- Microstrategy
- Dossier
- Web
- Trusted Authentication
- Permissions
comments: true
---
Dificulty ★☆☆☆☆

### Introduction 
When you are browsing MicroStrategy web and you want to open Library. You will be asked for credentials unles you set up Trusted Authentication. This is done through 'Secret Key' whitch has to be at least 5 charecters long.
 
### Library
First go to
http://localhost:8080/MicroStrategyLibrary/admin <br />
![Login](/img/20210516_0003/Login.png)

use the password in Default_accounts.txt <br />
![Password](/img/20210516_0003/Password.png) <br />

and enter user mstr and password that is located under 'Tomcat'
![PassEnterBlured](/img/20210516_0003/PassEnterBlured.png)

I Library go to 'Library Server' and insert Secret Key
![SecretKeyLibrary](/img/20210516_0003/SecretKeyLibrary.png)

And click 'Save'

### Microstrategy web
Go to http://localhost:8080/MicroStrategy/servlet/mstrWebAdmin

For login use the same password as in Library

Under security
![WebSecurity](/img/20210516_0003/WebSecurity.png)

You will find input for 'Secret Key'
![WebSecretKey](/img/20210516_0003/WebSecretKey.png)




