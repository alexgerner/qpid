# sb-rs-search-app

The project sb-rs-basic-search-app is build by Alexander Gerner on 10/04/2019

Task Specification
===================

This is a simple search application over a set of patient records.
Patient case-insensitive, partial match over the document title and contents regular expression filetering
 
 Implementation comments
========================
 - Rest WS is implemeted.
 - Request is implemented as an object which includes name of the object and a list of objct associated parameters classified by index [0,1..]
 - Request requires use of the %5B and %5D instead of [ and ] corresponntly
 - Response is implemented in Jason format
 - Input Arguments Validation and Error handling logic: @Valid,   @NotEmpty, @ControllerAdvice, RestResponseEntityExceptionHandler,                                                                             ApplicationParametersValidationErrors
 - Generalized Methods: FileToContents, UniqueID, RegexpPrecompileSingleton

Build Environment
==================
Spring Tool Suite 3
Version: 3.9.9.RELEASE
Build Id: 201906181741
OS: Windows 10, v.10.0, x86_64 / win32
Java version: 1.8.0_221

Logs org.slf4j.Logger
======================
\sb-rs-search-app\logs\*

ApplicationpProperties
=========================
\sb-rs-search-app\src\main\resources\applivation.properties

JUnit
===========
Suite: \sb-rs-search-app\src\test\java\com\gernera\app\suites\AllTests.java
Tests: \sb-rs-search-app\src\test\java\com\gernera\app\tests\*

- TestsPatternSearch
- TestsValidateFindIDByRegExp
- TeststsValidateInputParameters.class

Basic Smoke Test Use Cases (Rest Service API)
===============================================
Use Case 1: request failure - no input parameters:
-------------------- 
request: http://localhost:8080/list
response: Wrong Input Parameters: [ Error: Name cannot be null Error: SearchObject cannot be null ]

Use Case 2: request failure - wrong input parameters (missed patient name):
-------------------- 
request: http://localhost:8080/list?searchObject%5B0%5D.patterns=Patient%20Note:::6/20/2010:::&searchObject%5B0%5D.resource=Mary_1.txt&searchObject%5B1%5D.patterns=Patient%20Note:::6/20/2010:::&searchObject%5B1%5D.resource=Mary_2.txt

response: Wrong Input Parameters: [ Error: Name cannot be null ]

Use Case 3: request success - 1 patient 2 resources - dual partual match & no match - Patient Id found
-------------------- 
request: http://localhost:8080/list?name=Mary&searchObject%5B0%5D.patterns=Patient%20Note:::6/20/2010:::&searchObject%5B0%5D.resource=Mary_1.txt&searchObject%5B1%5D.patterns=Patient%20Note:::6/20/2010:::&searchObject%5B1%5D.resource=Mary_2.txt

response: Patient Name:Mary Patient Id:0000003 File: Mary_1.txt {Patient Note=partual match(PATIENT,NOTE), 6/20/2010=no match} File: Mary_2.txt {Patient Note=partual match(PATIENT,NOTE), 6/20/2010=no match}

Use Case 4: request success - 1 patient 2 resources - dual partual match & no match - Patient Id not found 
-------------------- 

request: http://localhost:8080/list?name=Joe TestPerson&searchObject%5B0%5D.patterns=Clinical Note:::4/6/2010:::&searchObject%5B0%5D.resource=Joe_1.txt&searchObject%5B1%5D.patterns=Summary:::7/2/2010:::&searchObject%5B1%5D.resource=Joe_2.txt

response: Patient Name:Joe TestPerson Patient Id: File: Joe_1.txt {Clinical Note=partual match(CLINICAL,NOTE), 4/6/2010=no match} File: Joe_2.txt {Summary=no match, 7/2/2010=no match}

Use Case 5: request success - 1 patient 1 resource -  dual partual match & no match - Patient Id not found 
-------------------- 
request: http://localhost:8080/list?name=Sam TestPerson&searchObject%5B0%5D.patterns=Patient Note:::8/3/2012:::&searchObject%5B0%5D.resource=Sam_1.txt

response: Patient Name:Sam TestPerson Patient Id:7 File: Sam_1.txt {Patient Note=partual match(PATIENT,NOTE), 8/3/2012=no match}

