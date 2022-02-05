---
layout: post
title: Qlik - Limit Acces To Data
tags:
- Microstrategy
- Distribution Services
- UTF-8
comments: true
---
Dificulty ★★★★☆

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

.containerTab {
  padding: 20px;
  color: white;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}

/* Closable button inside the container tab */
.closebtn {
  float: right;
  color: white;
  font-size: 35px;
  cursor: pointer;
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
<div id="b1" class="containerTab" style="display:none;background:green">
  <span onclick="this.parentElement.style.display='none'" class="closebtn">&times;</span>
  <p>In the Windows Registry, locate the following: HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\MicroStrategy\DSS Server\Castor\

Create a DWORD registry key entry "DSNotUseUnicodeForPT" with value = 1</p>
</div>

<div id="b2" class="containerTab" style="display:none;background:blue">
  <span onclick="this.parentElement.style.display='none'" class="closebtn">&times;</span>
  <p>In the MSIReg.reg file, locate the following: [HKEY_LOCAL_MACHINE\SOFTWARE\MicroStrategy\DSS Server\Castor]
  
Create an entry "DSNotUseUnicodeForPT "=dword:00000001</p>
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
</html> 
