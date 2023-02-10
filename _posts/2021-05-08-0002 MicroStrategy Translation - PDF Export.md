---
layout: post
title: MicroStrategy Translation - PDF Export
tags:
- Microstrategy
- Export Engine
comments: true
---
Dificulty ★★★★☆

Microstrategy transactions are separated in multiple categories
- Interface for WEB 
- Interface for Library (In version 2021 not yet available)
- Help links 
- Names of Metadata objects
- Report data language
- **PDF Export**

### INTRO
If you use added transactions you may come across an error where transactions on PDF export are not working correctly. Here is a quide what to do in this situation.

### ERROR
1. Set new language in WEB 

2. Set the 'Number and Date Format' to 'Germany'

3. Create dossier and use automatic lines like 'Average' or 'Max'.

4. Export to PDF

5. PDF will export Average as 'Durchschnitt'


### SOLUTION
1. Stop PDF Exporter in MicroStrategy Service Manager.

2. Open location "C:\Program Files (x86)\MicroStrategy\Export"

3. Copy PDFExporterService.jar file to another location (e.g. folder Document in demo) so that it can be open directly.

4. Create backup to another location.

5. Open previusly created copy of PDFExporterService.jar with 7-Zip(right click -> 7-Zip -> Open archive)

6. Navigate to target folder in 7-Zip: \PDFExporterService.jar\BOOT-INF\classes\descriptors\

7. Add customer's MessageBundle file (in this case MessagesBundle_sl) 

8. Add line to locales.xml 

    _Change the values as peer your locale settings._
    
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

9. Replace the PDFExporterService.jar file in installation folder by the updated one.

10. Start PDF Exporter in MicroStrategy Service Manager.
