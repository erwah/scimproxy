# Introduction #

This page contains examples of how we see the usage of the scimproxy projecy. Some of the examples is implemented already and some will be, and some my change as time goes. However this page is created to give you an idea of where and how to use the scimproxy project, both the scimproxy and the scimcore, can be used to solve you specific use-cases.

# scimcore #
This is a library that is meant to give an abstraction over the SCIM core specification you will have simple getters and setters, You will work with one object and then encode it as json or xml depending on the situation. Further it contains help for sorting and other common SCIM operations, for more details see WorkingWithScimCore

# scimproxy #
This project is a servlet that can be deployed in any servlet container. It uses the ScimCore to implement the SCIM Rest Api. The scimproxy creates one interface (SCIM Rest Api) towards different backend user storages (ldap, database, etc.). Further it can help to keep users in different locations synchronized by pushing reading, and receiving changes.

# Scenarios #

## Assumptions ##
Users do SAML, OpenID or similar login towards idp coupled to the local directory and then gets directed to the Cloud Service Provider (CSP).

## Synchronizing users with time trigger ##
This scenario is for you that have a local directory with applications using and updating it, but no have means/interest to change these applications. However you wishes to use a CSP that needs these local users.
In this case you setup the scimproxy towards your local directory, you configure which attributes you wish to synchronize with the CSP, and then you configure how often the users needs to be synchronized.

![http://scimproxy.googlecode.com/svn/wiki/TimeTrigger.jpg](http://scimproxy.googlecode.com/svn/wiki/TimeTrigger.jpg)

If the cloud service provider does not supply a SCIM Rest Api interface you could always ask him to setup a scimproxy.

## Synchronizing users with on-demand trigger ##
In this scenario you have a bit more control over the applications that updates the local directory. Further you do not want to wait for the time trigger to hit before the changes propagate to the CSP.
In this case you could in the application after updating a user do a post to the trigger servlet at the scimproxy and it would read the user from local directory and update it at the CSP.
Similarly if you do not want to wait for the time trigger to hit for users updates to be downloaded from the cloud service you could setup an account for the Cloud service provider on your scimproxy and he would be able to directly push changes down to you.

![http://scimproxy.googlecode.com/svn/wiki/OnDemandTrigger.jpg](http://scimproxy.googlecode.com/svn/wiki/OnDemandTrigger.jpg)

## Working with the scimcore ##
If you are creating a new application and you wish to be independent of the storage type. Then you could chose to use the scimcore user object internally and put the scimproxy between you and any storage.
In normal cases there exists be a plug-in for your specific storage, if not you could always create a new one, that works towards the storage you have. If you are missing attributes on the scimcore objects you look for implemented extensions that might fulfill your needs, if non exists you create a new extension with desired attributes.
By accessing the user storage through the scimproxy users can be synchronized with cloud service on-demand, changes propagate directly and no extra work has to be done.

![http://scimproxy.googlecode.com/svn/wiki/UserStorage.jpg](http://scimproxy.googlecode.com/svn/wiki/UserStorage.jpg)

## On-demand provisioning through REST ##
You are using a cloud service that does not always need the user data and therefor does not want to store it but in some situations it needs an address or similar.
In this case you could exposes your directory through the scimproxy by setting up an account for the CSP to fetch user data when needed. Of course you limit the data returned to the cloud service provider on a need to know basis.

![http://scimproxy.googlecode.com/svn/wiki/OnDemandRest.jpg](http://scimproxy.googlecode.com/svn/wiki/OnDemandRest.jpg)

## On-demand provisioning through SAML ##
In this case you use the scimcore for internal user representation and you federate identities towards a CSP. The CSP requests user data on-demand through SAML requests. in the SAML requests the CSP adds SCIM assertion requests. When receiver such a request you ask the scimcore user object to augment the response with requested data by passing the scimcore user the SAML request and a unsigned response the scimcore user augments the response and it can be signed and sent to the CSP with desired information.

![http://scimproxy.googlecode.com/svn/wiki/OnDemandSAML.jpg](http://scimproxy.googlecode.com/svn/wiki/OnDemandSAML.jpg)