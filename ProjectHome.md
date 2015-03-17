# SCIM #
The System for Cross-domain Identity Management (SCIM) specification is under development under IETF.

Our intention is to release scimproxy as a production quality, plugin based, SCIM Service Provider that can lift user information to the cloud.

Project is developed by Erik Wahlström, Samuel Erdtman and Daniel Lindau and the development of scimproxy is sponsored by [Technology Nexus](http://www.nexussafe.com/).

## Core ##
Download the core jar [here](http://code.google.com/p/scimproxy/source/browse/trunk/ScimCore/target/scimcore.jar). See WorkingWithScimCore for example code.

Example:
```
String jsonScimUser = "{\"schemas\": [\"urn:scim:schemas:core:user:1.0\"], \"id\": \"005D0000001Az1u\",  \"userName\": \"bjensen@example.com\" }";
ScimUser user = new ScimUser(jsonScimUser,  ScimUser.ENCODING_JSON);
```

## Proxy ##

A scimproxy binary will soon be uploaded for testing. Right now, just download the project and run maven jetty:run to startup the scimproxy. See [GettingStarted](GettingStarted.md) and [Configuration](Configuration.md) for more information.

## Viewer ##

The proxy includes a SCIM user viewer that lists, deletes, edits and batch import users. Access it on running server. If a downstream or upstream server is configured you can use the viewer to browse and edit there users to.
`<server>:<port>/Viewer/List`

## Compliance tests ##

scimproxy is used to build the official SCIM compliance tests found on http://www.simplecloud.info. You can find the code here http://code.google.com/p/scimproxy/source/browse/#svn%2Ftrunk%2Fscimcompliance. It's still in beta and we plan to add and improve tests. Feedback and comments are more then welcome.

## Interops ##

Read more about scimproxy and the SCIM interop at IIW [here](https://www.pingidentity.com/blogs/pingtalk/index.cfm/2011/10/20/SCIM-interop-shows-specification-coming-to-life).

Read more about scimproxy and th SCIM interop at IETF Paris
[here](http://code.google.com/p/scim/wiki/FirstInteropEvent).