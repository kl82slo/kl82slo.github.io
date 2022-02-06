---
layout: post
title: MicroStrategy - Force UTF-8 in Distribution Services
tags:
- MicroStrategy
- Distribution Services
- UTF-8
comments: true
---
Dificulty ★★★★☆

#IN PROGRESS


## The error

When directly exporting report to text file it will be exported as 'UTF-8' but when using 'Distribution Services' it will be genereted as 'UCS-2 LE BOM' insted of 'UTF-8'


<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

/* The grid: 2 equal columns that floats next to each other */
.column {
  float: left;
  width: 50%;
  padding: 50px;
  text-align: center;
  font-size: 25px;
  cursor: pointer;
  color: white;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}

</style>
</head>
<body>

<div style="text-align:center">
  <h2>For solution chose OS</h2>
</div>

<!-- Three columns -->
<div class="row">
  <div class="column" onclick="openTab('b1');" style="background:green;">
    Windows
  </div>
  <div class="column" onclick="openTab('b2');" style="background:blue;">
    Linux
  </div>
</div>

<!-- Full-width columns: (hidden by default) -->
<div id="b1" class="containerTab">
  <p>
    
    
    
1) In the Windows Registry, locate the following: HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\MicroStrategy\DSS Server\Castor\

2) Create a DWORD registry key entry "DSNotUseUnicodeForPT" with value = 1 <br /> 
![DSNotUseUnicodeForPT00](/img/20220205_0009/DSNotUseUnicodeForPT00.png)  <br /> 
![DSNotUseUnicodeForPT01](/img/20220205_0009/DSNotUseUnicodeForPT01.png)  <br /> 
![DSNotUseUnicodeForPT01](/img/20220205_0009/DSNotUseUnicodeForPT02.png)  <br /> 
    <br /> 
In the end it shud look like   <br /> 
![DSNotUseUnicodeForPT](/img/20220205_0009/DSNotUseUnicodeForPT.png)

  </p>
</div>

<div id="b2" class="containerTab" >
  <p>
    
    
    
1) In the MSIReg.reg file, locate the following: [HKEY_LOCAL_MACHINE\SOFTWARE\MicroStrategy\DSS Server\Castor] <br /> 

2) Create an entry "DSNotUseUnicodeForPT "=dword:00000001 <br /> 

In the end it shud look like
{% highlight bash %}
"DSHostName"=""
"DSMaxConn"=dword:000003e8
"DSNumAggregateThreads"=dword:00000004
"DSNumDecompressThreads"=dword:00000001
"DSNumDeserializeThreads"=dword:00000004
"DSNumReceiverThreads"=dword:00000001
"DSPort"=dword:00007621
"HomePath"="/var/opt/MicroStrategy/IntelligenceServer"
"IgnoreAllExceptions"=dword:00000000
"MaintenanceModeEnabled"=dword:00000001
"ProcessAffinity"=""
"UseServerOSLocaleinFallback"="0"
"DSNotUseUnicodeForPT "=dword:00000001    
{% endhighlight %}
    
    
    
  </p>
</div>

<p> The Intelligence Server needs to be restarted for the changes to take effect </p>

<script>
function openTab(tabName) {
  var i, x;
  x = document.getElementsByClassName("containerTab");
  for (i = 0; i < x.length; i++) {
    x[i].style.display = "none";
  }
  document.getElementById(tabName).style.display = "block";
}
</script>

</body>


