# Introduction #

Config file can configure authentication, storage and downstream SCIM servers.

# Location #

Config file must be the checkin file at the moment but it can also read config files from /opt/scimproxy/config

# Encoding #
File must be saved as UTF-8.

# Details #
The auth node defines what type of authentication that the API requires. Only BASIC auth is currently supported. Attributes are username and password.

The storages nodes contains the storages used. You can choose between a dummy storage that stores users in memory and a local MongoDB installation (listening at localhost and port 27017.

The down-stream node defines down stream SCIM servers. All changes made to the local SCIM server will also be sent down to the configured server.

The down-stream server nodes defines what type of authentication that is uses. You can also define primary encoding (JSON or XML) then the version that the server uses. The version is added to the url of the server.

# Examples #

## Minimum ##
```
<?xml version="1.0" encoding="UTF-8" ?>
<config>
	<auth type="basic">
		<username>usr</username>
		<password>pw</password>
	</auth>
	<storages>
		<storage>
			<type>mongodb</type>
		</storage>
	</storages>
</config>
```

## With down stream servers ##

```
<?xml version="1.0" encoding="UTF-8" ?>
<config>
	<auth type="basic">
		<username>usr</username>
		<password>pw</password>
	</auth>
	<storages>
		<storage>
			<type>dummy</type>
		</storage>
	</storages>
	<down-stream>
		<csp>
			<url>http://192.168.10.141:8080</url>
			<preferedEncoding>JSON</preferedEncoding>
			<version>v1</version>
			<auth type="basic">
				<username>usr</username>
				<password>pw</password>
			</auth>
		</csp>
	</down-stream>
</config>
```