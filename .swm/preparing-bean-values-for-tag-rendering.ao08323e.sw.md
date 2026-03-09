---
title: Preparing Bean Values for Tag Rendering
---
This document describes how a bean property value is retrieved and prepared for use in tag rendering. The flow takes a bean name, property, and scope as input, and produces a processed value for dynamic tag insertion.

# Extracting Bean Value for Tag Insertion

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" line="653">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="653:5:5" line-data="    protected TagHandler processBean(">`processBean`</SwmToken>, we grab the bean value using <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="660:1:3" line-data="            TagUtils.getRealValueFromBean(">`TagUtils.getRealValueFromBean`</SwmToken>. If it's missing, we bail out with an exception. We call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="660:1:1" line-data="            TagUtils.getRealValueFromBean(">`TagUtils`</SwmToken> next because that's where the actual lookup logic lives, so we delegate the retrieval there.

```java
    protected TagHandler processBean(
        String beanName,
        String beanProperty,
        String beanScope)
        throws JspException {

        Object beanValue =
            TagUtils.getRealValueFromBean(
                beanName,
                beanProperty,
                beanScope,
                pageContext);

        if (beanValue == null) {
            throw new JspException(
                "Error - Tag Insert : No value defined for bean '"
                    + beanName
                    + "' with property '"
                    + beanProperty
                    + "' in scope '"
                    + beanScope
                    + "'.");
        }

```

---

</SwmSnippet>

## Resolving Bean Property Value

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="195">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="195:7:7" line-data="    public static Object getRealValueFromBean(">`getRealValueFromBean`</SwmToken>, we start by pulling the bean from the specified scope using <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="204:7:7" line-data="            Object bean = retrieveBean(beanName, beanScope, pageContext);">`retrieveBean`</SwmToken>. We need to do this because the bean could live in different places depending on how the app is set up.

```java
    public static Object getRealValueFromBean(
        String beanName,
        String beanProperty,
        String beanScope,
        PageContext pageContext)
        throws JspException {

        try {
            Object realValue;
            Object bean = retrieveBean(beanName, beanScope, pageContext);
```

---

</SwmSnippet>

### Locating Bean in Context

See <SwmLink doc-title="Retrieving a Bean by Name and Scope">[Retrieving a Bean by Name and Scope](/.swm/retrieving-a-bean-by-name-and-scope.6mei8h8n.sw.md)</SwmLink>

### Extracting Property or Returning Bean

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="205">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="660:3:3" line-data="            TagUtils.getRealValueFromBean(">`getRealValueFromBean`</SwmToken>, after retrieving the bean, we either extract the property using <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="206:5:5" line-data="                realValue = PropertyUtils.getProperty(bean, beanProperty);">`PropertyUtils`</SwmToken> or just return the bean itself if no property is given. Exceptions are thrown if property access fails.

```java
            if (bean != null && beanProperty != null) {
                realValue = PropertyUtils.getProperty(bean, beanProperty);
            } else {
                realValue = bean; // value can be null
            }
            return realValue;

        } catch (NoSuchMethodException ex) {
            throw new JspException(
                "Error - component.PutAttributeTag : Error while retrieving value from bean '"
                    + beanName
                    + "' with property '"
                    + beanProperty
                    + "' in scope '"
                    + beanScope
                    + "'. (exception : "
                    + ex.getMessage(), ex);

        } catch (InvocationTargetException ex) {
            throw new JspException(
                "Error - component.PutAttributeTag : Error while retrieving value from bean '"
                    + beanName
                    + "' with property '"
                    + beanProperty
                    + "' in scope '"
                    + beanScope
                    + "'. (exception : "
                    + ex.getMessage(), ex);

        } catch (IllegalAccessException ex) {
            throw new JspException(
                "Error - component.PutAttributeTag : Error while retrieving value from bean '"
                    + beanName
                    + "' with property '"
                    + beanProperty
                    + "' in scope '"
                    + beanScope
                    + "'. (exception : "
                    + ex.getMessage(), ex);
        }
    }
```

---

</SwmSnippet>

## Processing Retrieved Bean Value

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" line="677">

---

After getting the bean value from <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="660:1:1" line-data="            TagUtils.getRealValueFromBean(">`TagUtils`</SwmToken>, <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="653:5:5" line-data="    protected TagHandler processBean(">`processBean`</SwmToken> hands it off to <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="677:3:3" line-data="        return processObjectValue(beanValue);">`processObjectValue`</SwmToken>. This step deals with any extra handling or conversion needed before the tag uses the value.

```java
        return processObjectValue(beanValue);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
