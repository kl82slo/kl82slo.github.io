---
layout: post
title: MicroStrategy Translation - PDF Export
tags:
- Microstrategy
- Export Engine
comments: true
---
Dificulty ★★★★☆

# IN DEVELOPMENT

Microstrategy transactions are separated in multiple categories
- Interface for WEB 
- Interface for Library (In version 2021 not yet available)
- Help links 
- Names of Metadata objects
- Report data language
- **PDF Export**

### ERROR
1. Set new language in WEB 

2. Set the 'Number and Date Format' to 'Germany'

3. Create dossier and use automatic lines like 'Average' or 'Max'.

4. Export to PDF

5. PDF will export Average as 'Durchschnitt'


### SOLUTION
1. Stop PDF Exporter in MicroStrategy Service Manager.

2. Copy PDFExporterService.jar file to another location (e.g. folder Document in demo) so that it can be open directly.

3. Open PDFExporterService.jar directly by 7-Zip(right click -> 7-Zip -> Open archive)

4. Navigate to target folder in 7-Zip: \PDFExporterService.jar\BOOT-INF\classes\descriptors\

5. Add customer's MessageBundle file (in this case MessagesBundle_sl) 

6. Add line to locales.xml 

```html
<locale 
        locale-id="1060" 
        language="sl" 
        country="SL" 
        desc="Slovenscina" 
        desc-id="" 
        char-set="UTF-8" 
        char-set-excel="UnicodeLittle"/>
```

7. Replace the PDFExporterService.jar file in installation folder by the updated one.

8. Start PDF Exporter in MicroStrategy Service Manager.



