# Using the core lib #
The core lib provides a simple way to create a SCIM user object from input data or saved data. When the user object has been created it can be manipulated and outputted in desired format (json or xml). further the scim core lib can handle patching and sorting of users.

## Operations ##
A Scim user can be created in two different ways ether a clean user with nothing set or by parsing a user object in xml or son format.
### Create a clean user ###
```
System.out.println("=== Creating User One ===");
ScimUser user1 = new ScimUser();
user1.setId("ASD4F-RFTG5-RF45F");
```

### Create user form json or xml ###
```
System.out.println("=== Creating User Two ===");
String jsonScimUser = "{\"schemas\": [\"urn:scim:schemas:core:user:1.0\"], \"id\": \"005D0000001Az1u\",  \"userName\": \"bjensen@example.com\" }";
ScimUser user2 = new ScimUser(jsonScimUser,  ScimUser.ENCODING_JSON);
```

### Manipulating a ScimUser ###
```
System.out.println("=== Updating User One ===");
user1.setUserName("kalle");
user1.setName(new Name("Karl Andersson", "Karl", "Andersson", null, null, null));
List<PluralType<String>> emails = new ArrayList<PluralType<String>>();
emails.add(new PluralType<String>("kalle@andersson.se", "home", true));
emails.add(new PluralType<String>("kalle@company.com", "work", false));
user1.setEmails(emails);
```

### Reading data from ScimUser ###
```
System.out.println("=== Read data from User One ===");
String userName = user1.getUserName();
Name name = user1.getName();
String givenName = name.getGivenName();
String familyName = user1.getName().getFamilyName();
System.out.println("userName= " + userName);
System.out.println("name.givenName= " + givenName);
System.out.println("name.familyName= " + familyName);
```

### Output encoded user ###
```
System.out.println("=== Encoding the full user ===");
String fullJsonScimUser1 = user1.getUser(ScimUser.ENCODING_JSON);
String fullJsonScimUser2 = user2.getUser(ScimUser.ENCODING_JSON);
System.out.println("==== User 1 ====");
System.out.println(fullJsonScimUser1);
System.out.println("==== User 2 ====");
System.out.println(fullJsonScimUser2);
System.out.println("=== Encoding the partial user ===");
List<String> includeAttributes = new ArrayList<String>();
includeAttributes.add("userName");
String partialJsonScimUser1 = user1.getUser(ScimUser.ENCODING_JSON, includeAttributes);
String partialJsonScimUser2 = user2.getUser(ScimUser.ENCODING_JSON, includeAttributes);
System.out.println("==== User One ====");
System.out.println(partialJsonScimUser1);
System.out.println("==== User Two ====");
System.out.println(partialJsonScimUser2);
```

### Output a list of users ###
```
List<ScimUser> users = new ArrayList<ScimUser>();
users.add(user1);
users.add(user2);
System.out.println("=== Encoding list of full users ===");
IUserEncoder encoder = new JsonEncoder();
String fullJsonUsers = encoder.encode(users);
System.out.println(fullJsonUsers);
System.out.println("=== Encoding list of partial users ===");
String partialJsonUsers = encoder.encode(users, includeAttributes);
System.out.println(partialJsonUsers);
```

### Extensions ###
```
package info.simplecloud.core;

import info.simplecloud.core.coding.handlers.StringHandler;

public class NewExtension {
    private String extensionAttribute;

    @Attribute(codingHandler = StringHandler.class, schemaName = "extensionAttribute")
    public String getExtensionAttribute() {
        return this.extensionAttribute;
    }

    public void setExtensionAttribute(String extensionAttribute) {
        this.extensionAttribute = extensionAttribute;
    }
}


// This is done before any users are created (could be done by configuration too)
System.out.println("=== Setting up extensions ===");
ScimUser.registerExtension(EnterpriseAttributes.class);
ScimUser.registerExtension(NewExtension.class);

// Then one can use the extension in this way
System.out.println("=== Using the enterprise extension ===");
EnterpriseAttributes enterpriseAttributes= user1.getExtension(EnterpriseAttributes.class);
enterpriseAttributes.setEmployeeNumber("ABC-123");
System.out.println(user1.getUser(ScimUser.ENCODING_JSON));
System.out.println("=== Adding a new extension ===");
NewExtension ext = user1.getExtension(NewExtension.class);
ext.setExtensionAttribute("Attribute string");
System.out.println(user1.getUser(ScimUser.ENCODING_JSON));
```

### Sorting ###
To sort a Collection of ScimUsers we create a Comarator that defines on which attribute to sort and in what order to sort.
```
System.out.println("=== Sorting users ascending ===");
ScimUserComparator<ScimUser> ascending = new ScimUserComparator<ScimUser>(ScimUser.ATTRIBUTE_USER_NAME, true);
Collections.sort(users, ascending);
for(ScimUser user: users) {
    System.out.println(user.getUser(ScimUser.ENCODING_JSON, includeAttributes));
}
System.out.println("=== Sorting users descending ===");
ScimUserComparator<ScimUser> descending = new ScimUserComparator<ScimUser>(ScimUser.ATTRIBUTE_USER_NAME, false);
Collections.sort(users, descending);
for(ScimUser user: users) {
    System.out.println(user.getUser(ScimUser.ENCODING_JSON, includeAttributes));
}
```

### Patching ###
```
System.out.println("=== Patching User One and Two ===");
String patch = "{\"userType\": \"Employee\", \"title\": \"Tour Guide\"}";
user1.patch(patch, ScimUser.ENCODING_JSON);
user2.patch(patch, ScimUser.ENCODING_JSON);
```

## Putting it all together ##
```
package info.simplecloud.core;

import info.simplecloud.core.coding.encode.IUserEncoder;
import info.simplecloud.core.coding.encode.JsonEncoder;
import info.simplecloud.core.execeptions.InvalidUser;
import info.simplecloud.core.execeptions.UnknownEncoding;
import info.simplecloud.core.execeptions.UnknownExtension;
import info.simplecloud.core.extensions.EnterpriseAttributes;
import info.simplecloud.core.tools.ScimUserComparator;
import info.simplecloud.core.types.Name;
import info.simplecloud.core.types.PluralType;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class GettinStarted {

    public static void main(String[] args) throws UnknownEncoding, InvalidUser, UnknownExtension {
        System.out.println("=== Setting up extensions ===");
        ScimUser.registerExtension(EnterpriseAttributes.class);
        ScimUser.registerExtension(NewExtension.class);
        
        System.out.println("=== Creating User One ===");
        ScimUser user1 = new ScimUser();
        user1.setId("ASD4F-RFTG5-RF45F");

        System.out.println("=== Creating User Two ===");
        String jsonScimUser = "{\"schemas\": [\"urn:scim:schemas:core:user:1.0\"], \"id\": \"005D0000001Az1u\",  \"userName\": \"bjensen@example.com\" }";
        ScimUser user2 = new ScimUser(jsonScimUser,  ScimUser.ENCODING_JSON);

        System.out.println("=== Updating User One ===");
        user1.setUserName("kalle");
        user1.setName(new Name("Karl Andersson", "Karl", "Andersson", null, null, null));
        List<PluralType<String>> emails = new ArrayList<PluralType<String>>();
        emails.add(new PluralType<String>("kalle@andersson.se", "home", true));
        emails.add(new PluralType<String>("kalle@company.com", "work", false));
        
        System.out.println("=== Read data from User One ===");
        String userName = user1.getUserName();
        Name name = user1.getName();
        String givenName = name.getGivenName();
        String familyName = user1.getName().getFamilyName();
        System.out.println("userName= " + userName);
        System.out.println("name.givenName= " + givenName);
        System.out.println("name.familyName= " + familyName);
        
        System.out.println("=== Patching User One and Two ===");
        String patch = "{\"userType\": \"Employee\", \"title\": \"Tour Guide\"}";
        user1.patch(patch, ScimUser.ENCODING_JSON);
        user2.patch(patch, ScimUser.ENCODING_JSON);
        
        System.out.println("=== Encoding the full user ===");
        String fullJsonScimUser1 = user1.getUser(ScimUser.ENCODING_JSON);
        String fullJsonScimUser2 = user2.getUser(ScimUser.ENCODING_JSON);
        System.out.println("==== User 1 ====");
        System.out.println(fullJsonScimUser1);
        System.out.println("==== User 2 ====");
        System.out.println(fullJsonScimUser2);
        System.out.println("=== Encoding the partial user ===");
        List<String> includeAttributes = new ArrayList<String>();
        includeAttributes.add("userName");
        String partialJsonScimUser1 = user1.getUser(ScimUser.ENCODING_JSON, includeAttributes);
        String partialJsonScimUser2 = user2.getUser(ScimUser.ENCODING_JSON, includeAttributes);
        System.out.println("==== User One ====");
        System.out.println(partialJsonScimUser1);
        System.out.println("==== User Two ====");
        System.out.println(partialJsonScimUser2);
        
        List<ScimUser> users = new ArrayList<ScimUser>();
        users.add(user1);
        users.add(user2);
        System.out.println("=== Encoding list of full users ===");
        IUserEncoder encoder = new JsonEncoder();
        String fullJsonUsers = encoder.encode(users);
        System.out.println(fullJsonUsers);
        System.out.println("=== Encoding list of partial users ===");
        String partialJsonUsers = encoder.encode(users, includeAttributes);
        System.out.println(partialJsonUsers);
        
        System.out.println("=== Sorting users ascending ===");
        ScimUserComparator<ScimUser> ascending = new ScimUserComparator<ScimUser>(ScimUser.ATTRIBUTE_USER_NAME, true);
        Collections.sort(users, ascending);
        for(ScimUser user: users) {
            System.out.println(user.getUser(ScimUser.ENCODING_JSON, includeAttributes));
        }
        System.out.println("=== Sorting users descending ===");
        ScimUserComparator<ScimUser> descending = new ScimUserComparator<ScimUser>(ScimUser.ATTRIBUTE_USER_NAME, false);
        Collections.sort(users, descending);
        for(ScimUser user: users) {
            System.out.println(user.getUser(ScimUser.ENCODING_JSON, includeAttributes));
        }
        
        System.out.println("=== Using the enterprise extension ===");
        EnterpriseAttributes enterpriseAttributes= user1.getExtension(EnterpriseAttributes.class);
        enterpriseAttributes.setEmployeeNumber("ABC-123");
        System.out.println(user1.getUser(ScimUser.ENCODING_JSON));
        System.out.println("=== Adding a new extension ===");
        NewExtension ext = user1.getExtension(NewExtension.class);
        ext.setExtensionAttribute("Attribute string");
        System.out.println(user1.getUser(ScimUser.ENCODING_JSON));  
    }
}
```