# Feature Groups

Feature Groups are a way to logically group related features together within a dataset. This capability can be useful for instance, if you want to group all the "user stories" of sprint in the same release.

## Overview

A Feature Group is a collection of features that share a common context or purpose. By organizing features into groups, you can manage and analyze them more effectively. Feature Groups can help in:

- **Organization**: Keeping related features together makes it easier to navigate and understand the dataset.
- **Analysis**: Analyzing features in groups can provide insights that might be missed when looking at individual features.
- **Collaboration**: Teams can work on specific Feature Groups without interfering with others.
- **Versioning**: Feature Groups can be versioned independently, allowing for better control over changes and updates.

## Examples

### Creating a Feature Group

To create a Feature Group, you can use the following ff4j configuration snippet:

```xml title="ff4j-sample.xml"
<?xml version="1.0" encoding="UTF-8" ?>
<features>
 <!-- Sample Feature Group -->
 <feature-group name="release-2.3">
    <feature uid="users-story1" enable="false" /> 
    <feature uid="users-story2" enable="false" />
 </feature-group>

 <feature uid="featA" enable="true" /> 
 <feature uid="featB" enable="false" />
</features>
```

Initialize FF4J with the above configuration:

```java title="FF4JInitialization.java"
FF4j ff4j = new FF4j("ff4j-sample.xml");
```

### Accessing Features in a Feature Group

You can access and manage features within a Feature Group using the FF4J API:

```java title="AccessFeatureGroupTest.java"
@Test
public void myGroupTest() {
 FF4j ff4j = new FF4j("ff4j-sample.xml");

 // Check features loaded
 assertEquals(4, ff4j.getFeatures().size());
 assertTrue(ff4j.exist("featA")); 
 assertTrue(ff4j.exist("users-story1"));   
 assertTrue(ff4j.getStore().existGroup("release-2.3")); 
 
 // Given 
 assertFalse(ff4j.check("users-story1")); 
 assertFalse(ff4j.check("users-story2"));
 // When 
 ff4j.enableGroup("release-2.3");
 // Then 
 assertTrue(ff4j.check("users-story1")); 
 assertTrue(ff4j.check("users-story2"));
}
```

### Accessing Through Feature Store

```java title="AccessFeatureGroupViaStore.java"
@Test
public void workWithGroupTest() {
  // Given
  FF4j ff4j = new FF4j("ff4j-groups.xml");
  assertTrue(ff4j.exist("featA"));
  // When 
  ff4j.getStore().addToGroup("featA", "new-group");
  // Then
  assertTrue(ff4j.getStore().existGroup("new-group"));
  assertTrue(ff4j.getStore().readAllGroups().contains("new-group"));
  Map<String, Feature> myGroup = ff4j.getStore().readGroup("new-group");
  assertTrue(myGroup.containsKey("featA"));

  // A feature can be in a single group 
  // Here changing => deleting the last element of a group => deleting the group 
  ff4j.getStore().addToGroup("featA", "group2");
  assertFalse(ff4j.getStore().existGroup("new-group"));
}
```

