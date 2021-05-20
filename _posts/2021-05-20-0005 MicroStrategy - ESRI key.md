---
layout: post
title: MicroStrategy - ESRI key
tags:
- Microstrategy
- ESRI
comments: true
---
Dificulty ★☆☆☆☆

### PREREQUISITES

Before configuring cloud-based ESRI maps, obtain a free ESRI Map Key by
[MicroStrategy Download site](https://community.microstrategy.com/s/products)
[!Generate_key] (/img/20210520-0005/Generate_key.png)
or by contacting MicroStrategy tehnical support (Dont forget to provide DSI)

in case of [Mapbox https://www.mapbox.com/](https://www.mapbox.com/)

### MicroStrategy Version 10.6+

First go to  <br />
tomcat - tomcat/webapps/Microstrategy/plugins  <br />
or IIS - C:/Program Files (x86)/MicroStrategy/Web ASPx/plugins

in it create path /ConnectorForMap/WEB-INF/xml/config  <br />
then create file mapConfig.xml  <br />
and copy the folowing and change the key to the one you got in PREREQUISITES stage <br />
{% highlight bash %}
<mc> 
  <ec>
    <apps>
        <key> <![CDATA[XXXXXXXXXXXXXXXXXXXXXXXXXX]]> </key>
    </apps>
  </ec>
</mc>  
{% endhighlight %}
    
if you also have [Mapbox](https://www.mapbox.com/) or [Google map](https://www2.microstrategy.com/producthelp/Current/GISHelp/WebHelp/Lang_1033/Content/Google_Setup.htm) key insert them like

{% highlight bash %}
<mc>
	<!-- ESRI map configuration -->
	<ec>
		<apps>
			<key><![CDATA[XXXXXXXXXXXXXXXXXXXXXXXXXX]]> </key>
		</apps>
	</ec>

	<!-- Google map configuration -->
  
  <gc>
    <mk isPremier=false>Google Maps API key</mk>
  </gc>

	<!-- MapBox configuration -->
	<mbc>
		<tk><![CDATA[XXXXXXXXXXXXXXXXXXXXXXXXXX]]> </key></tk>
	</mbc>
</mc>
{% endhighlight %}

After you have created your file make a copy into 
tomcat/webapps/MicroStrategyLibrary/plugins/

and restart tomcat (and IIS if you use it for web)

### Aditonal read
[Setup for integration with Google Maps](https://www2.microstrategy.com/producthelp/Current/GISHelp/WebHelp/Lang_1033/Content/Google_Setup.htm)  <br />
[ESRI maps for MicroStrategy Web and MicroStrategy Library products](https://community.microstrategy.com/s/article/KB45064-How-to-activate-cloud-based-ESRI-maps-for-MicroStrategy?language=en_US)

### Errors
If you get error  <br />
'error. com.microstrategy.utils.cache.CacheException: Connection timed out: connect'  <br />
[!ESRIConnectionError](/img/20210520-0005/EsriConnectionError.png)

or  <br />
'com.microstrategy.utils.cache.CacheException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target.'  <br />
[!ESRIConnectionError1](/img/20210520-0005/EsriConnectionError1.png)

Then most likly you are using Proxy Server and need to enter detailes in  <br />
http://<font color='red'>localhost:8080</font>/MicroStrategy/servlet/mstrWebAdmin  <br />
[!EsriProxy](/img/20210520-0005/EsriProxy.png)
[For more detailes read KB202342](https://community.microstrategy.com/s/article/KB202342-Support-using-proxy-server-to-send-HTTP-request-to-ESRI?language=en_US)

if you are still experiencing problems make sure that there are no traffic restrictions for the following addresses:

http://www.arcgis.com/home/webmap/viewer.html?url=http://services.arcgis.com/P3ePLMYs2RVChkJx/ArcGIS/rest/services/World_Administrative_Divisions/FeatureServer/0&source=sd

http://services.arcgis.com
 
http://services.arcgisonline.com/arcgis/rest/services 

And make sure that the following ports are open and the following addresses not blocked by a proxy/firewall:

- Ports 443 , 80 , 6080 
- *.arcgis.com, *.esri.com and *.arcgisonline.com
