---
title: MySQL - Manual - SQL Syntax - 13.1.17 - CREATE SPATIAL REFERENCE SYSTEM 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE SPATIAL REFERENCE SYSTEM
---

MySQL `CREATE SPATIAL REFERENCE SYSTEM` 语法

<!--more-->

```
CREATE OR REPLACE SPATIAL REFERENCE SYSTEM
    srid srs_attribute ...

CREATE SPATIAL REFERENCE SYSTEM
    [IF NOT EXISTS]
    srid srs_attribute ...

srs_attribute: {
    NAME 'srs_name'
    | DEFINITION 'definition'
    | ORGANIZATION 'org_name' IDENTIFIED BY org_id
    | DESCRIPTION 'description'
}

srid, org_id: 32-bit unsigned integer
```

This statement creates a spatial reference system (SRS) definition and stores it in the data dictionary. The
definition can be inspected using the INFORMATION_SCHEMA ST_SPATIAL_REFERENCE_SYSTEMS table.
This statement requires the SUPER privilege.

If neither OR REPLACE nor IF NOT EXISTS is specified, an error occurs if an SRS definition with the
SRID value already exists.

With CREATE OR REPLACE syntax, any existing SRS definition with the same SRID value is replaced,
unless the SRID value is used by some column in an existing table. In that case, an error occurs. For
example:
```
mysql> CREATE OR REPLACE SPATIAL REFERENCE SYSTEM 4326 ...;
ERROR 3716 (SR005): Can't modify SRID 4326. There is at
least one column depending on it.
```

To identify which column or columns use the SRID, use this query:
```
SELECT * FROM INFORMATION_SCHEMA.ST_GEOMETRY_COLUMNS WHERE SRS_ID=4326;
```

With CREATE ... IF NOT EXISTS syntax, any existing SRS definition with the same SRID value
causes the new definition to be ignored and a warning occurs.

SRID values must be in the range of 32-bit unsigned integers, with these restrictions:
* SRID 0 is a valid SRID but cannot be used with CREATE SPATIAL REFERENCE SYSTEM.
* If the value is in a reserved SRID range, a warning occurs. Reserved ranges are [0, 32767] (reserved by
EPSG), [60,000,000, 69,999,999] (reserved by EPSG), and [2,000,000,000, 2,147,483,647] (reserved by
MySQL). EPSG stands for the European Petroleum Survey Group.
* Users should not create SRSs with SRIDs in the reserved ranges. Doing so runs the risk that the SRIDs
will conflict with future SRS definitions distributed with MySQL, with the result that the new system-
provided SRSs are not installed for MySQL upgrades or that the user-defined SRSs are overwritten.

Attributes for the statement must satisfy these conditions:
* Attributes can be given in any order, but no attribute can be given more than once.
* The NAME and DEFINITION attributes are mandatory.
* The NAME srs_name attribute value must be unique. The combination of the ORGANIZATION
org_name and org_id attribute values must be unique.
* The NAME srs_name attribute value and ORGANIZATION org_name attribute value cannot be empty or
begin or end with whitespace.
* String values in attribute specifications cannot contain control characters, including newline.
* The following table shows the maximum lengths for string attribute values.

    Table 13.5 CREATE SPATIAL REFERENCE SYSTEM Attribute Lengths
    Attribute       Maximum Length (characters)
    NAME            80
    DEFINITION      4096
    ORGANIZATION    256
    DESCRIPTION     2048

Here is an example CREATE SPATIAL REFERENCE SYSTEM statement. The DEFINITION value is
reformatted across multiple lines for readability. (For the statement to be legal, the value actually must be
given on a single line.)
```
CREATE SPATIAL REFERENCE SYSTEM 4120
NAME 'Greek'
ORGANIZATION 'EPSG' IDENTIFIED BY 4120
DEFINITION
    'GEOGCS["Greek",DATUM["Greek",SPHEROID["Bessel 1841",
    6377397.155,299.1528128,AUTHORITY["EPSG","7004"]],
    AUTHORITY["EPSG","6120"]],PRIMEM["Greenwich",0,
    AUTHORITY["EPSG","8901"]],UNIT["degree",0.017453292519943278,
    AUTHORITY["EPSG","9122"]],AXIS["Lat",NORTH],AXIS["Lon",EAST],
    AUTHORITY["EPSG","4120"]]';
```

The grammar for SRS definitions is based on the grammar defined in OpenGIS Implementation
Specification: Coordinate Transformation Services, Revision 1.00, OGC 01-009, January 12, 2001, Section
7.2. This specification is available at http://www.opengeospatial.org/standards/ct.

MySQL incorporates these changes to the specification:
* Only the `<horz cs>` production rule is implemented (that is, geographic and projected SRSs).
* There is an optional, nonstandard `<authority>` clause for `<parameter>`. This makes it possible to
recognize projection parameters by authority instead of name.
* SRS definitions may not contain newlines.

