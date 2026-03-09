---
title: Attribute Lookup in JSP Pages
---
This document explains how attribute values are retrieved for use in JSP pages. Attributes can be accessed from a specific scope or by searching all available scopes, supporting flexible page rendering. The input is a name and an optional scope, and the output is the attribute value or an error if the scope is invalid.

```mermaid
flowchart TD
  node1["Resolving Attribute by Scope"]:::HeadingStyle
  click node1 goToHeading "Resolving Attribute by Scope"
  node1 --> node2{"Is a specific scope provided?"}
  node2 -->|"No"| node3["Attribute value retrieved"]
  node2 -->|"Yes"| node4["Mapping Scope Name to Constant"]:::HeadingStyle
  click node4 goToHeading "Mapping Scope Name to Constant"
  node4 --> node5{"Is scope name valid?"}
  node5 -->|"Yes"| node3
  node5 -->|"No"| node6["Handling Scope Lookup Errors"]:::HeadingStyle
  click node6 goToHeading "Handling Scope Lookup Errors"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Resolving Attribute by Scope

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start lookup for value by name"]
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:863:864"
  node1 --> node2{"Is a specific scope provided?"}
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:865:865"
  node2 -->|"No"| node3["Find value in all available scopes"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:866:867"
  node2 -->|"Yes"| node4["Mapping Scope Name to Constant"]
  
  node4 --> node5["Value retrieved for use in page"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:870:875"
  node3 --> node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node4 goToHeading "Mapping Scope Name to Constant"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start lookup for value by name"]
%%   click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:863:864"
%%   node1 --> node2{"Is a specific scope provided?"}
%%   click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:865:865"
%%   node2 -->|"No"| node3["Find value in all available scopes"]
%%   click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:866:867"
%%   node2 -->|"Yes"| node4["Mapping Scope Name to Constant"]
%%   
%%   node4 --> node5["Value retrieved for use in page"]
%%   click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:870:875"
%%   node3 --> node5
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node4 goToHeading "Mapping Scope Name to Constant"
%% node4:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="863">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`lookup`</SwmToken>, we check if <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:19:19" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`scopeName`</SwmToken> is null to decide whether to search all scopes or a specific one. If <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:19:19" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`scopeName`</SwmToken> is provided, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="870:10:12" line-data="            return pageContext.getAttribute(name, instance.getScope(scopeName));">`instance.getScope`</SwmToken> to translate it to the integer constant required by the JSP API. This lets us fetch the attribute from the exact scope requested. The function assumes <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:19:19" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`scopeName`</SwmToken> is valid—if not, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="870:12:12" line-data="            return pageContext.getAttribute(name, instance.getScope(scopeName));">`getScope`</SwmToken> will throw.

```java
    public Object lookup(PageContext pageContext, String name, String scopeName)
        throws JspException {
        if (scopeName == null) {
            return pageContext.findAttribute(name);
        }

        try {
            return pageContext.getAttribute(name, instance.getScope(scopeName));
```

---

</SwmSnippet>

## Mapping Scope Name to Constant

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="809">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="809:5:5" line-data="    public int getScope(String scopeName)">`getScope`</SwmToken> looks up the integer constant for a given scope name in a map. If the name isn't found, it throws a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="810:3:3" line-data="        throws JspException {">`JspException`</SwmToken>, using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="814:7:9" line-data="            throw new JspException(messages.getMessage(&quot;lookup.scope&quot;, scope));">`messages.getMessage`</SwmToken> to build the error message. This is where we jump to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> to get the error text.

```java
    public int getScope(String scopeName)
        throws JspException {
        Integer scope = (Integer) scopes.get(scopeName.toLowerCase());

        if (scope == null) {
            throw new JspException(messages.getMessage("lookup.scope", scope));
        }

        return scope.intValue();
    }
```

---

</SwmSnippet>

## Building Error Message (Single Arg)

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> with a key and one argument just calls the main <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> method with a null Locale and wraps the argument in an array. This keeps all the formatting logic in one place.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Formatting and Caching Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Request localized message for key and
arguments"] --> node2{"Is locale provided?"}
  click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
  node2 -->|"Yes"| node3["Use provided locale"]
  node2 -->|"No"| node4["Use default locale"]
  click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node3 --> node5{"Is message format cached for (locale,
key)?"}
  node4 --> node5
  click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:295:297"
  node5 -->|"Yes"| node6["Format message with arguments"]
  node5 -->|"No"| node7{"Is message string available for (locale,
key)?"}
  click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
  click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:299:301"
  node7 -->|"Yes"| node8["Escape, create, and cache message format"]
  click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:305:308"
  node8 --> node6
  node7 -->|"No"| node9{"Should return null?"}
  click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
  node9 -->|"Yes"| node10["Return null"]
  click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:302"
  node9 -->|"No"| node11["Return fallback string: ???locale.key???"]
  click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:303:303"
  node6 --> node12["Return formatted message"]
  click node12 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Request localized message for key and
%% arguments"] --> node2{"Is locale provided?"}
%%   click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%   node2 -->|"Yes"| node3["Use provided locale"]
%%   node2 -->|"No"| node4["Use default locale"]
%%   click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node3 --> node5{"Is message format cached for (locale,
%% key)?"}
%%   node4 --> node5
%%   click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:295:297"
%%   node5 -->|"Yes"| node6["Format message with arguments"]
%%   node5 -->|"No"| node7{"Is message string available for (locale,
%% key)?"}
%%   click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%   click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:299:301"
%%   node7 -->|"Yes"| node8["Escape, create, and cache message format"]
%%   click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:305:308"
%%   node8 --> node6
%%   node7 -->|"No"| node9{"Should return null?"}
%%   click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%   node9 -->|"Yes"| node10["Return null"]
%%   click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:302"
%%   node9 -->|"No"| node11["Return fallback string: ???locale.key???"]
%%   click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:303:303"
%%   node6 --> node12["Return formatted message"]
%%   click node12 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> (with Locale, key, and args) handles message formatting and caching. It checks the cache for a <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken>, creates and stores one if missing, and formats the message. If the message isn't found, it returns a placeholder or null. The cache is synchronized for thread safety.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> (Locale, key, Object\[\]) is where the actual formatting and caching happens. It synchronizes on the cache, checks for an existing <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken>, creates and stores one if missing, and formats the message. If the message string is missing, it returns a placeholder or null. The escape method is used before creating <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> to handle special cases.

```java
    public String getMessage(Locale locale, String key, Object[] args) {
        // Cache MessageFormat instances as they are accessed
        if (locale == null) {
            locale = defaultLocale;
        }

        MessageFormat format = null;
        String formatKey = messageKey(locale, key);

        synchronized (formats) {
            format = (MessageFormat) formats.get(formatKey);

            if (format == null) {
                String formatString = getMessage(locale, key);

                if (formatString == null) {
                    return returnNull ? null : ("???" + formatKey + "???");
                }

                format = new MessageFormat(escape(formatString));
                format.setLocale(locale);
                formats.put(formatKey, format);
            }
        }

        return format.format(args);
    }
```

---

</SwmSnippet>

## Handling Scope Lookup Errors

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Attempt to look up value for JSP page"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:871:875"
    node1 --> node2{"Did an exception occur?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:871:875"
    node2 -->|"No"| node3["Return lookup result"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:871:875"
    node3 --> node5["End"]
    node2 -->|"Yes"| node4["Record error for page display and
rethrow"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:872:874"
    node4 --> node5["End"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:875:875"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Attempt to look up value for JSP page"]
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:871:875"
%%     node1 --> node2{"Did an exception occur?"}
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:871:875"
%%     node2 -->|"No"| node3["Return lookup result"]
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:871:875"
%%     node3 --> node5["End"]
%%     node2 -->|"Yes"| node4["Record error for page display and
%% rethrow"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:872:874"
%%     node4 --> node5["End"]
%%     click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:875:875"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="871">

---

Back in TagUtils.lookup, after returning from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="809:5:5" line-data="    public int getScope(String scopeName)">`getScope`</SwmToken>, if a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="871:6:6" line-data="        } catch (JspException e) {">`JspException`</SwmToken> is thrown, we catch it, store it in the request scope with <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="872:1:1" line-data="            saveException(pageContext, e);">`saveException`</SwmToken>, and then rethrow. This makes the exception available for error handling downstream.

```java
        } catch (JspException e) {
            saveException(pageContext, e);
            throw e;
        }
    }
```

---

</SwmSnippet>

# Storing Exception in Request Scope

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="1170">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1170:5:5" line-data="    public void saveException(PageContext pageContext, Throwable exception) {">`saveException`</SwmToken> puts the exception into the request scope using a fixed key. This is done by calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1171:3:3" line-data="        pageContext.setAttribute(Globals.EXCEPTION_KEY, exception,">`setAttribute`</SwmToken> with the exception and the key, so error handlers can find it during the request.

```java
    public void saveException(PageContext pageContext, Throwable exception) {
        pageContext.setAttribute(Globals.EXCEPTION_KEY, exception,
            PageContext.REQUEST_SCOPE);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="290">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="290:7:7" line-data="    public static void setAttribute(PageContext pageContext, String name, Object beanValue)">`setAttribute`</SwmToken> is just a wrapper that always puts the attribute in request scope. No extra logic—just makes sure the attribute is always accessible for the current request.

```java
    public static void setAttribute(PageContext pageContext, String name, Object beanValue)
        throws JspException {
        pageContext.setAttribute(name, beanValue, PageContext.REQUEST_SCOPE);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
