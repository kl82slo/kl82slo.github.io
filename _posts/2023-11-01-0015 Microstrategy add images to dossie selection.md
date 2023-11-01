---
layout: post
title: Microstrategy add images to dossie covers
tags:
- Microstrategy
- Dossie
comments: true
---
Dificulty ★★☆☆☆

### INTRO
If you want to add pictures to dossie covers collection the solution is not as seamless as you would think.

#### How to do it
Let's say we want to try adding <br />
https://www.pinterest.com/pin/249879479318637219/ <br />
https://www.pinterest.com/pin/634515034970940369/ <br />

to <br />
![Cover](/img/20231101_0015/s1.png)

First add [images](https://github.com/kl82slo/kl82slo.github.io/blob/main/img/20231101_0015/Dossie_Cover.zip) folder to tomcat <br />(you can use other locations)
C:\Program Files (x86)\Common Files\MicroStrategy\Tomcat\apache-tomcat-9.0.80\webapps\Images\Dossie_Cover

Download extra images into that folder <br />
(In this case renamed to 60.gif and 61.jfif)

Test if you can access current images <br />
http://<font color='red'>localhost</font>/Images/Dossie_Cover/23.jpg

For <font color='blue'>Microstrategy web</font> change file (backup first) <br />
C:\Program Files (x86)\Common Files\MicroStrategy\Tomcat\apache-tomcat-9.0.80\webapps\MicroStrategy\javascript\bundles\html5-vi.js <br />
and find H="https://demo.microstrategy.com/MicroStrategy/images/Coverpages/16-9/" <br />
change it to H="http://<font color='red'>localhost</font>/Images/Dossie_Cover/"

and add new images after P=[  <br />
example <br />
H="https://<font color='red'>localhost</font>/MicroStrategy/Images/Dossie_Cover/",P=["60.gif","61.jfif","22.jpg","23.jpg", <br />
![Cover2](/img/20231101_0015/s2.png)

for <font color='blue'>Libray</font> the same can be done in file <br />
C:\Program Files (x86)\Common Files\MicroStrategy\Tomcat\apache-tomcat-9.0.80\webapps\MicroStrategyLibrary\javascript\bundles\mojo-dossier.js

<font color='red'>!note you do not have to restart tomcat in order for this to work but clearing cache or going incognito is a must.</font>




