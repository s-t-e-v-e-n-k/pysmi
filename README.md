# SNMP MIB Compiler

[![Become a Sponsor](https://img.shields.io/badge/Become%20a%20Sponsor-lextudio-orange.svg?style=for-readme)](https://github.com/sponsors/lextudio)
[![PyPI](https://img.shields.io/pypi/v/pysmi.svg)](https://pypi.org/project/pysmi)
[![PyPI Downloads](https://img.shields.io/pypi/dd/pysmi)](https://pypi.python.org/pypi/pysmi/)
[![Python Versions](https://img.shields.io/pypi/pyversions/pysmi.svg)](https://pypi.org/project/pysmi/)
[![GitHub license](https://img.shields.io/badge/license-BSD-blue.svg)](https://raw.githubusercontent.com/lextudio/pysmi/master/LICENSE.rst)

PySMI is a pure-Python implementation of
[SNMP SMI](https://en.wikipedia.org/wiki/Management_information_base) MIB parser.
This tool is designed to turn ASN.1 MIBs into various formats. As of this moment,
JSON and [PySNMP](https://github.com/lextudio/pysnmp) modules can be generated
from ASN.1 MIBs.

## Features

* Understands SMIv1, SMIv2 and de-facto SMI dialects
* Turns MIBs into PySNMP classes and JSON documents
* Maintains an index of MIB objects over many MIB modules
* Automatically pulls ASN.1 MIBs from local directories, ZIP archives,
  and HTTP servers
* 100% Python, works with Python 3.8+

Rendered PySMI documentation can be found at [PySMI site](https://www.pysnmp.com/pysmi).

## How to use PySMI

If you are using PySNMP, you might never notice PySMI presence - PySNMP
calls PySMI for MIB download and compilation behind the scenes (you can
still can do that manually by invoking *mibdump* tool).

To turn ASN.1 MIB into a JSON document, call *mibdump* tool like this:

``` bash
$ mibdump --generate-mib-texts  --destination-format json IF-MIB
Source MIB repositories: file:///usr/share/snmp/mibs, https://mibs.pysnmp.com/asn1/@mib@
Borrow missing/failed MIBs from: https://mibs.pysnmp.com/json/fulltexts/@mib@
Existing/compiled MIB locations:
Compiled MIBs destination directory: .
MIBs excluded from code generation: RFC-1212, RFC-1215, RFC1065-SMI, RFC1155-SMI,
RFC1158-MIB, RFC1213-MIB, SNMPv2-CONF, SNMPv2-SMI, SNMPv2-TC, SNMPv2-TM
MIBs to compile: IF-MIB
Destination format: json
Parser grammar cache directory: not used
Also compile all relevant MIBs: yes
Rebuild MIBs regardless of age: yes
Do not create/update MIBs: no
Byte-compile Python modules: no (optimization level no)
Ignore compilation errors: no
Generate OID->MIB index: no
Generate texts in MIBs: yes
Keep original texts layout: no
Try various filenames while searching for MIB module: yes
Created/updated MIBs: IANAifType-MIB, IF-MIB, SNMPv2-MIB
Pre-compiled MIBs borrowed:
Up to date MIBs: SNMPv2-CONF, SNMPv2-SMI, SNMPv2-TC
Missing source MIBs:
Ignored MIBs:
Failed MIBs:
```

JSON document build from
[IF-MIB module](https://mibs.pysnmp.com/asn1/IF-MIB)
would hold information such as:

``` json
   {
      "ifMIB": {
          "name": "ifMIB",
          "oid": "1.3.6.1.2.1.31",
          "class": "moduleidentity",
          "revisions": [
            "2007-02-15 00:00",
            "1996-02-28 21:55",
            "1993-11-08 21:55"
          ]
        },
      // ...
      "ifTestTable": {
        "name": "ifTestTable",
        "oid": "1.3.6.1.2.1.31.1.3",
        "nodetype": "table",
        "class": "objecttype",
        "maxaccess": "not-accessible"
      },
      "ifTestEntry": {
        "name": "ifTestEntry",
        "oid": "1.3.6.1.2.1.31.1.3.1",
        "nodetype": "row",
        "class": "objecttype",
        "maxaccess": "not-accessible",
        "augmention": {
          "name": "ifTestEntry",
          "module": "IF-MIB",
          "object": "ifEntry"
        }
      },
      "ifTestId": {
        "name": "ifTestId",
        "oid": "1.3.6.1.2.1.31.1.3.1.1",
        "nodetype": "column",
        "class": "objecttype",
        "syntax": {
          "type": "TestAndIncr",
          "class": "type"
        },
        "maxaccess": "read-write"
      },
      // ...
   }
```

In general, converted MIBs capture all aspects of original (ASN.1) MIB contents
and layout. The snippet above is just a partial example, but here is the
complete [IF-MIB.json](https://mibs.pysnmp.com/json/fulltexts/IF-MIB.json)
file.

Besides one-to-one MIB conversion, PySMI library can produce JSON index to
facilitate fast MIB information lookup across large collection of MIB files.

For example, JSON index for
[IP-MIB.json](https://mibs.pysnmp.com/json/asn1/IP-MIB),
[TCP-MIB.json](https://mibs.pysnmp.com/json/asn1/TCP-MIB) and
[UDP-MIB.json](https://mibs.pysnmp.com/json/asn1/UDP-MIB)
modules would keep information like this:

``` json
   {
      "compliance": {
         "1.3.6.1.2.1.48.2.1.1": [
           "IP-MIB"
         ],
         "1.3.6.1.2.1.49.2.1.1": [
           "TCP-MIB"
         ],
         "1.3.6.1.2.1.50.2.1.1": [
           "UDP-MIB"
         ]
      },
      "identity": {
          "1.3.6.1.2.1.48": [
            "IP-MIB"
          ],
          "1.3.6.1.2.1.49": [
            "TCP-MIB"
          ],
          "1.3.6.1.2.1.50": [
            "UDP-MIB"
          ]
      },
      "oids": {
          "1.3.6.1.2.1.4": [
            "IP-MIB"
          ],
          "1.3.6.1.2.1.5": [
            "IP-MIB"
          ],
          "1.3.6.1.2.1.6": [
            "TCP-MIB"
          ],
          "1.3.6.1.2.1.7": [
            "UDP-MIB"
          ],
          "1.3.6.1.2.1.49": [
            "TCP-MIB"
          ],
          "1.3.6.1.2.1.50": [
            "UDP-MIB"
          ]
      }
   }
```

With this example, *compliance* and *identity* keys point to
*MODULE-COMPLIANCE* and *MODULE-IDENTITY* MIB objects, *oids*
list top-level OIDs branches defined in MIB modules. Full index
build over thousands of MIBs could be seen
[here](https://mibs.pysnmp.com/json/index.json).

The PySMI library can automatically fetch required MIBs from HTTP sites
or local directories. You could configure any MIB source available to you (including
[https://mibs.pysnmp.com/asn1/](https://mibs.pysnmp.com/asn1/)) for that purpose.

## How to get PySMI

The pysmi package is distributed under terms and conditions of 2-clause
BSD [license](https://www.pysnmp.com/pysmi/license.html). Source code is freely
available as a GitHub [repo](https://github.com/lextudio/pysmi).

You could `pip install pysmi` or download it from [PyPI](https://pypi.org/project/pysmi/).

If something does not work as expected,
[open an issue](https://github.com/lextudio/pysnmp/issues) at GitHub or
post your question [on Stack Overflow](https://stackoverflow.com/questions/ask).

Copyright (c) 2015-2020, [Ilya Etingof](mailto:etingof@gmail.com).
Copyright (c) 2022-2024, [LeXtudio Inc.](mailto:support@lextudio.com).
All rights reserved.
