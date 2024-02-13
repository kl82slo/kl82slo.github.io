---
layout: post
title: MicroStrategy Which DSN us used for metadata conection
tags:
- Microstrategy
- metadata
comments: true
---
Dificulty ★☆☆☆☆

### INTRO
Sometimes you don't know which metadata conection is currently configured. 

## WINDOWS 
open registry and go to
HKEY_LOCAL_MACHINE \ SOFTWARE \ WOW6432Node \ MicroStrategy \ Datasources \ Castor Server
DSN in under 'Location'
[Windows link](https://community.microstrategy.com/s/article/How-to-determine-which-DSN-is-being-used-by-the-Intelligence-Server-on-a-64-bit-Windows-machine?language=en_US)https://community.microstrategy.com/s/article/How-to-determine-which-DSN-is-being-used-by-the-Intelligence-Server-on-a-64-bit-Windows-machine?language=en_US) <br />

or
you can run 'metadata.ps1' in this zip that will create 'output.txt' with necessary data. 
<a href="/img/20240213_0016/metadata.zip">download</a>


## Linux
execute
cat /var/opt/MicroStrategy/MSIReg.reg | grep -i dsn=
DSN in under 'Location'
[Linux link](https://community.microstrategy.com/s/article/KB47411-How-to-identify-the-DSN-used-by-MicroStrategy?language=en_US&_gl=1*goa1bm*_ga*MjEwODY0NTMwMS4xNzA1NjcyNzE3*_ga_0C9LVNZBZY*MTcwNTkyODgzMi40LjEuMTcwNTkyOTcwMi4wLjAuMA..) <br />








