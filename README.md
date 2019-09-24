# qpid The project sb-rs-basic-search-app is build by Alexander Gerner on 09/24/2019

Task Specification
===================

Simple Patient Search Starter Project

This is a simple search application over a set of patient records.
You can clone this repo or provision an instant dev environment via http://beta.codenvy.com/f?id=qu9bnf9n1h40whho

## Your Mission

In src/main/java you'll find the starter backend code and in src/main/webapp you'll find the start UI code.
If you start up the application, you'll see a basic search application. It's a simple ember app that hits
an endpoint in src/main/java/com/qpidhealth/qpid/search/services/PatientService.

Take an hour or two and work on some basic improvements to this application, including, but not limited to:
 
 * Making the PatientService filter the results it returns by a case-insensitive, partial match over the document title and contents.
 
 * Cleaning up the code using better object-oriented design and improving readability.
 
 * Adding a couple unit tests around your search code.
  


If you have HTML/JavaScript experience, also work on some of the following:
  
 * Improve the result rendering in the UI with better styling of snippet results.
  
 * Update the search interface to debounce calls to the backend
  
 * Perform highlighting of the search term in the text results
 
 * Come up with and add a new feature to the UI, this can be anything your creativity can dream up.




Implementation comments
========================
 - Code is refactored to meet the requiremnts.
 - Rest WS is implemeted.
 - Request  implement as a list of parameters classified by index [0,1..]
 - Request requires use of the %5B and %5D instead of [ and ] corresponntly
 - Response is implemented following standard industry WS response practice.  

Build Environment
==================
Spring Tool Suite 3
Version: 3.9.9.RELEASE
Build Id: 201906181741
OS: Windows 10, v.10.0, x86_64 / win32
Java version: 1.8.0_221

Logs
=====
\sb-rs-basic-search-app\logs\*

ApplicationpProperties
=========================
\sb-rs-basic-search-app\src\main\resources\appplivation.properties

JUnit
===========
Suite: \sb-rs-basic-search-app\src\test\java\com\qpidhealth\qpid\app\suites\AllTests.java
Tests: \sb-rs-basic-search-app\src\test\java\comqpidhealth\qpid\app\tests\*

- TestsPatternSearch
- TestsValidateFindIDByRegExp
- TeststsValidateInputParameters.class

Basic Smoke Test Use Cases (Rest Service API)
===============================================
Use Case 1: request failure - no input parameters:
-------------------- 
request: http://localhost:8080/list
response: Please provide Input Parameters. e.g: http://localhost:8080/list?patient%5B0%5D.name=***&patient%5B0%5D.patterns=***:::***:::&patient%5B0%5D.resource=***.txt

Use Case 2: request failure - wrong input parameters (missed patient name):
-------------------- 
request: http://localhost:8080/list?patient%5B0%5D.patterns=Patient Note:::6/20/2010:::&patient%5B0%5D.resource=Mary_1.txt&patient%5B1%5D.patterns=Patient Note:::6/20/2010:::&patient%5B1%5D.resource=Mary_2.txt
response: Please provide Input Parameters. e.g: http://localhost:8080/list?patient%5B0%5D.name=***&patient%5B0%5D.patterns=***:::***:::&patient%5B0%5D.resource=***.txt

Use Case 3: request success - 1 patient 2 resources - dual partual match & no match - Patient Id found
-------------------- 
request: http://localhost:8080/list?patient%5B0%5D.name=Mary TestPerson&patient%5B0%5D.patterns=Patient Note:::6/20/2010:::&patient%5B0%5D.resource=Mary_1.txt&patient%5B1%5D.patterns=Patient Note:::6/20/2010:::&patient%5B1%5D.resource=Mary_2.txt
response: Patient Name:Mary TestPerson Patient Id:0000003 {Mary_1.txt={Patient Note=partual match(PATIENT,NOTE), 6/20/2010=no match}, Mary_2.txt={Patient Note=partual match(PATIENT,NOTE), 6/20/2010=no match}}

Use Case 4: request success - 1 patient 2 resources - dual partual match & no match - Patient Id not found 
-------------------- 
request: http://localhost:8080/list?patient%5B0%5D.name=Joe TestPerson&patient%5B0%5D.patterns=Clinical Note:::4/6/2010:::&patient%5B0%5D.resource=Joe_1.txt&patient%5B1%5D.patterns=Summary:::7/2/2010:::&patient%5B1%5D.resource=Joe_2.txt
response:Patient Name:Joe TestPerson Patient Id: {Joe_1.txt={Clinical Note=partual match(CLINICAL,NOTE), 4/6/2010=no match}, Joe_2.txt={Summary=no match, 7/2/2010=no match}}

Use Case 5: request success - 1 patient 1 resource -  dual partual match & no match - Patient Id not found 
-------------------- 
request: http://localhost:8080/list?patient%5B0%5D.name=Sam TestPerson&patient%5B0%5D.patterns=Patient Note:::8/3/2012:::&patient%5B0%5D.resource=Sam_1.txt
response:Patient Name:Sam TestPerson Patient Id:7 {Sam_1.txt={Patient Note=partual match(PATIENT,NOTE), 8/3/2012=no match}}

Use Case 5: request success - 1 patient 1 resource -  dual partual match & match - Patient Id not found 
-------------------- 
request: http://localhost:8080/list?patient%5B0%5D.name=Sam TestPerson&patient%5B0%5D.patterns=Patient Note:::7/5/2011:::&patient%5B0%5D.resource=Sam_1.txt
response:Patient Name:Sam TestPerson Patient Id:7 {Sam_1.txt={Patient Note=partual match(PATIENT,NOTE), 7/5/2011=match}}
