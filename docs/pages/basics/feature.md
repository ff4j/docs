### Definition

The [`Feature`](https://github.com/clun/ff4j/blob/master/ff4j-core/src/main/java/org/ff4j/core/Feature.java) term is used to represent functionality or treatment in an application. 
It is identified by an unique identifier (uid). 

A Feature represents a business logic that can potentially crosses every layer 
of applications from user interfaces to data access. Therefore, to implement 
a feature toggle mechanism, we must help you in each layer as shown with the 
picture on the right.

![pic](../../img/fig-01-feature.png)

Feature toggle,_or feature flag_, purpose is to evaluate the state of features at runtime to drive the execution of your code. You can change the state of features at runtime (enable and disable, toggle `ON/OFF`). 

Each feature is associated as a flag. 
Given a feature with identifier `f1` we want to write in the code:

```java
if (ff4j.check("f1")) {
  System.out.println("The feature 'f1' is 'ON'");
} else {
  System.out.println("The feature 'f1' is 'OFF'");    
}
```

### `Feature` Object

With the library `FF4J`, a feature is an object with multiple fields, not only identifier and state. You can also define:
- A text **description** to explain the purpose
- An _optional_ **groupName** to toggle multiple features at once (see [`FeatureGroup`](#group))
- An _optional_ **set of permissions** to implement RBAC access. (see [`Permissions`](#permissions-and-security))
- An _optional_ **flipping strategy** to implement your predicates (see [`FlippingStrategy`](#flipping-strategy))
- A **key/value map** named `customProperties` to create some context

*Samples Codes*
```java
Feature f1 = new Feature("f1");
Feature f2 = new Feature("f2", false, "sample description");

// Illstrating Permissions
Set < String > permission = new HashSet<String>();
permission.add("BETA-TESTER");
permission.add("VIP");
Feature f3 = new Feature("f3", false, "sample description", "GROUP_1", permission);
        
// Create custom properties
Feature f4 = new Feature("f4");
f4.addProperty(new PropertyString("p1", "v1"));
f4.addProperty(new PropertyDouble("pie", Math.PI));
f4.addProperty(new PropertyInt("myAge", 12));

// Working with ReleaseDate Flipping Strategy
Feature f5 = new Feature("f5");
Calendar nextReleaseDate = Calendar.getInstance();
nextReleaseDate.set(Calendar.MONTH, Calendar.SEPTEMBER);
nextReleaseDate.set(Calendar.DAY_OF_MONTH, 1);
f5.setFlippingStrategy(new ReleaseDateFlipStrategy(nextReleaseDate.getTime()));

// Working with DarkLaunch Flipping Strategy
Feature f6 = new Feature("f6");
f6.setFlippingStrategy(new DarkLaunchStrategy(0.2d));        

// Working with White List Flipping Strategy
Feature f7 = new Feature("f7");
f7.setFlippingStrategy(new WhiteListStrategy("localhost"));
```

It is not likely that you do have to create the `Features` objects on your own (except for tests). They will be stored in the FeatureStore and purpose is really to `check` the status at runtime. You may want to create them through the webUI or configuration files.

```
                                                       ,------------.                                                       
                                                       |Serializable|                                                       
                                                       |------------|                                                       
                                                       |------------|                                                       
                                                       `------------'                                                       
                                                              |                                                             
                                                              |                                                             
,--------------------------------------------------------------------------------------------------------------------------.
|Feature                                                                                                                   |
|--------------------------------------------------------------------------------------------------------------------------|
|-long serialVersionUID                                                                                                    |
|-String uid                                                                                                               |
|-boolean enable                                                                                                           |
|-String description                                                                                                       |
|-String group                                                                                                             |
|-Set<String> permissions                                                                                                  |
|-FlippingStrategy flippingStrategy                                                                                        |
|-Map<String,Property<?>> customProperties                                                                                 |
|--------------------------------------------------------------------------------------------------------------------------|
|+Feature(String uid)                                                                                                      |
|+Feature(String uid, boolean penable)                                                                                     |
|+Feature(String uid, boolean penable, String pdescription)                                                                |
|+Feature(String uid, boolean penable, String pdescription, String group)                                                  |
|+Feature(String uid, boolean penable, String pdescription, String group, Collection<String> auths)                        |
|+Feature(String uid, boolean penable, String pdescription, String group, Collection<String> auths, FlippingStrategy strat)|
|+Feature(Feature f)                                                                                                       |
|+String toString()                                                                                                        |
|+String toJson()                                                                                                          |
|+Feature fromJson(String jsonString)                                                                                      |
|+void enable()                                                                                                            |
|+void disable()                                                                                                           |
|+void toggle()                                                                                                            |
|+String getUid()                                                                                                          |
|+void setUid(String uid)                                                                                                  |
|+boolean isEnable()                                                                                                       |
|+void setEnable(boolean enable)                                                                                           |
|+String getDescription()                                                                                                  |
|+void setDescription(String description)                                                                                  |
|+FlippingStrategy getFlippingStrategy()                                                                                   |
|+void setFlippingStrategy(FlippingStrategy flippingStrategy)                                                              |
|+String getGroup()                                                                                                        |
|+void setGroup(String group)                                                                                              |
|+Set<String> getPermissions()                                                                                             |
|+void setPermissions(Set<String> permissions)                                                                             |
|+Property<T> getProperty(String propId)                                                                                   |
|+void addProperty(Property<T> props)                                                                                      |
|+Map<String,Property<?>> getCustomProperties()                                                                            |
|+void setCustomProperties(Map<String,Property<?>> customProperties)                                                       |
`--------------------------------------------------------------------------------------------------------------------------'

```