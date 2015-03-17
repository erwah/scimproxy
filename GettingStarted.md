# Introduction #
The ScimProxy and ScimCore are maven projects and some steps has to be done before running.


## Build from commandline ##
Download the code to a new directory:
```
$ svn checkout http://scimproxy.googlecode.com/svn/trunk/ scimproxy 
```
Go into the ScimCore directory:
```
$ cd scimproxy/ScimCore/
```
Generate XML beans:
```
$ mvn xmlbeans:xmlbeans
```
To install a JAR file in your local maven repro. It can then be used by other maven projects as a depencency:
```
$ mvn install
```
Go into the ScimProxy directory:
```
$ cd scimproxy/scimproxy/
```
Run server:
```
$ mvn compile test jetty:run
```
Browse to server at http://localhost:8080

## How to run from eclipse on Ubuntu ##
**Ubuntu 10.10 32bit** Sun Java6 JDK
**sudo apt-get install maven2** Subversion
**Eclipse Classic
  * m2e - http://download.eclipse.org/technology/m2e/milestones/1.0
  * subclipse - http://subclipse.tigris.org/update_1.6.x**

Download all code to a new directory:
```
svn checkout https://scimproxy.googlecode.com/svn/trunk/ scimproxy --username <username>
```


**Import ScimCore project into Eclipse.**
Import -> Maven -> Existing Maven Project
Browse to <checkout location>/scimproxy/ScimCore

Let eclipse download everything that it needs.

Make sure that it uses java 1.6 instead of 1.5.

Right click on scimcore root object. "Run as" -> "Maven build". Change "Goals" to "clean install".

First time all dependencies will be downloaded to compile, build and package the jar.

Refresh project. All should be well.

**Import ScimProxy project into Eclipse.**
Import -> Maven -> Existing Maven Project
Browse to /Users/erwah/dev/scimproxy/scimproxy

Make sure that it uses java 1.6 instead of 1.5.

Right click on scimproxy root object. "Run as" -> "Maven build". Change "Goals" to "clean install jetty:run".

Point your browser to http://localhost:8080/

## How to debug in eclipse ##

  * Create a new "External Tools Configuration" -> Select "Program". Click the "New launch configuration" button.
```
Name: scimproxy remote debugger
Locations: /home/erwah/Desktop/apache-maven-3.0.3/bin/mvn
Working Directory: ${workspace_loc:/scimproxy}
Arguments: clean install jetty:run
```

  * Under the "Environment" tab.
```
Create new variable.
Name: MAVEN_OPTS
Value: -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,address=5005,server=y,suspend=n
```

Run the external tool. Jetty will be started as usual.

  * Create a new "Debug Configurations..." -> Select "Remote Java Application" Click the "New launch configuration" button.
```
Name: scimproxy remote debugger
Port: 5005
```
Connect the remote debugger to jetty. Set a break point and run one of the CURL commands.Getting started with projects