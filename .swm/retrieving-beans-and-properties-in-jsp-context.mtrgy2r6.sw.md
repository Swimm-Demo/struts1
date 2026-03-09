---
title: Retrieving Beans and Properties in JSP Context
---
This document describes how a named object (bean) and its property can be retrieved from the JSP context to support dynamic content rendering. The flow returns the bean or property value if found, or provides a localized error message if not.

```mermaid
flowchart TD
  node1["Resolving Beans in JSP Context"]:::HeadingStyle
  click node1 goToHeading "Resolving Beans in JSP Context"
  node1 --> node2{"Is the bean found?"}
  node2 -->|"No"| node3["Fetching Localized Error Messages"]:::HeadingStyle
  click node3 goToHeading "Fetching Localized Error Messages"
  node3 --> node4["Throwing Localized Exceptions"]:::HeadingStyle
  click node4 goToHeading "Throwing Localized Exceptions"
  node2 -->|"Yes"| node5{"Is a property requested?"}
  node5 -->|"No"| node6["Retrieval successful"]
  node5 -->|"Yes"| node7{"Can the property be accessed?"}
  node7 -->|"Yes"| node6
  node7 -->|"No"| node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Resolving Beans in JSP Context

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Look up the named object in the
specified context"]
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:897:900"
  node1 --> node2{"Is the object found?"}
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:902:914"
  node2 -->|"No"| node3["Storing Exceptions for JSP Error Handling"]
  
  node2 -->|"Yes"| node4{"Is a property requested?"}
  
  node4 -->|"No"| node5["Throwing Localized Exceptions"]
  
  node4 -->|"Yes"| node6{"Can the property be accessed?"}
  
  node6 -->|"Yes"| node7["Return the property value"]
  click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:922:923"
  node6 -->|"No"| node8["Record error and return message:
property not accessible"]
  click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:924:959"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Fetching Localized Error Messages"
node3:::HeadingStyle
click node3 goToHeading "Storing Exceptions for JSP Error Handling"
node3:::HeadingStyle
click node4 goToHeading "Throwing Localized Exceptions"
node4:::HeadingStyle
click node5 goToHeading "Throwing Localized Exceptions"
node5:::HeadingStyle
click node6 goToHeading "Throwing Exceptions After Saving"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Look up the named object in the
%% specified context"]
%%   click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:897:900"
%%   node1 --> node2{"Is the object found?"}
%%   click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:902:914"
%%   node2 -->|"No"| node3["Storing Exceptions for JSP Error Handling"]
%%   
%%   node2 -->|"Yes"| node4{"Is a property requested?"}
%%   
%%   node4 -->|"No"| node5["Throwing Localized Exceptions"]
%%   
%%   node4 -->|"Yes"| node6{"Can the property be accessed?"}
%%   
%%   node6 -->|"Yes"| node7["Return the property value"]
%%   click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:922:923"
%%   node6 -->|"No"| node8["Record error and return message:
%% property not accessible"]
%%   click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:924:959"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Fetching Localized Error Messages"
%% node3:::HeadingStyle
%% click node3 goToHeading "Storing Exceptions for JSP Error Handling"
%% node3:::HeadingStyle
%% click node4 goToHeading "Throwing Localized Exceptions"
%% node4:::HeadingStyle
%% click node5 goToHeading "Throwing Localized Exceptions"
%% node5:::HeadingStyle
%% click node6 goToHeading "Throwing Exceptions After Saving"
%% node6:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="897">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="897:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String property,">`lookup`</SwmToken>, we try to find a bean in the given JSP context. If it's missing, we prepare to throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="898:8:8" line-data="        String scope) throws JspException {">`JspException`</SwmToken> with a localized error message, which is why we need to fetch the message from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> next.

```java
    public Object lookup(PageContext pageContext, String name, String property,
        String scope) throws JspException {
        // Look up the requested bean, and return if requested
        Object bean = lookup(pageContext, name, scope);

        if (bean == null) {
            JspException e = null;

            if (scope == null) {
                e = new JspException(messages.getMessage("lookup.bean.any", name));
            } else {
```

---

</SwmSnippet>

## Fetching Localized Error Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:15" line-data="    public String getMessage(String key, Object arg0) {">`getMessage(String key, Object arg0)`</SwmToken> just forwards the call to the overload that takes a Locale, so we can handle localization and argument substitution in one place.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Preparing Message Formatting Arguments

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is locale provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node1 -->|"Yes"| node2["Use provided locale"]
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node1 -->|"No"| node3["Use default locale"]
    click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:289:290"
    node2 --> node4["Lookup message template for key and
locale"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:293:300"
    node3 --> node4
    node4 --> node5{"Is message template found?"}
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
    node5 -->|"Yes"| node6["Format message with argument (arg0)"]
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    node5 -->|"No"| node7{"Return null or fallback string?"}
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node7 -->|"Return null"| node8["Return null"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node7 -->|"Fallback"| node9["Return '???key???'"]
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node6 --> node10["Return formatted message"]
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is locale provided?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node1 -->|"Yes"| node2["Use provided locale"]
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node1 -->|"No"| node3["Use default locale"]
%%     click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:289:290"
%%     node2 --> node4["Lookup message template for key and
%% locale"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:293:300"
%%     node3 --> node4
%%     node4 --> node5{"Is message template found?"}
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%     node5 -->|"Yes"| node6["Format message with argument (<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:14:14" line-data="    public String getMessage(String key, Object arg0) {">`arg0`</SwmToken>)"]
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     node5 -->|"No"| node7{"Return null or fallback string?"}
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node7 -->|"Return null"| node8["Return null"]
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node7 -->|"Fallback"| node9["Return '???key???'"]
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node6 --> node10["Return formatted message"]
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:20" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage(Locale locale, String key, Object arg0)`</SwmToken> just wraps the argument in an array and passes it to the main formatting method, so all message formatting goes through the same logic.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:22" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage(Locale locale, String key, Object[] args)`</SwmToken> handles the actual message formatting. It caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> objects for performance, falls back to a default locale if needed, returns a placeholder if the message is missing, and escapes the format string before formatting. No checks on argument validity—just assumes they're right.

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

## Throwing Localized Exceptions

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="908">

---

Back in `TagUtils.lookup`, after getting the localized error message, we throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="908:7:7" line-data="                e = new JspException(messages.getMessage(&quot;lookup.bean&quot;, name,">`JspException`</SwmToken> with it so the error is clear and user-friendly.

```java
                e = new JspException(messages.getMessage("lookup.bean", name,
                            scope));
            }

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="912">

---

Back in `TagUtils.lookup`, after preparing the exception, we save it in the page context so error handlers or JSP error pages can access it.

```java
            saveException(pageContext, e);
            throw e;
        }

        if (property == null) {
            return bean;
        }

        // Locate and return the specified property
        try {
            return PropertyUtils.getProperty(bean, property);
        } catch (IllegalAccessException e) {
            saveException(pageContext, e);
```

---

</SwmSnippet>

## Storing Exceptions for JSP Error Handling

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="1170">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1170:5:5" line-data="    public void saveException(PageContext pageContext, Throwable exception) {">`saveException`</SwmToken> puts the exception into the page context under a standard key, always in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1172:3:3" line-data="            PageContext.REQUEST_SCOPE);">`REQUEST_SCOPE`</SwmToken>, so error pages can pick it up. The actual <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1171:3:3" line-data="        pageContext.setAttribute(Globals.EXCEPTION_KEY, exception,">`setAttribute`</SwmToken> call is handled by a utility method next.

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

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="290:7:7" line-data="    public static void setAttribute(PageContext pageContext, String name, Object beanValue)">`setAttribute`</SwmToken> just wraps <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="292:1:3" line-data="        pageContext.setAttribute(name, beanValue, PageContext.REQUEST_SCOPE);">`pageContext.setAttribute`</SwmToken>, always using <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="292:13:13" line-data="        pageContext.setAttribute(name, beanValue, PageContext.REQUEST_SCOPE);">`REQUEST_SCOPE`</SwmToken>. No checks, no extra logic—just sets the attribute where error handlers expect it.

```java
    public static void setAttribute(PageContext pageContext, String name, Object beanValue)
        throws JspException {
        pageContext.setAttribute(name, beanValue, PageContext.REQUEST_SCOPE);
    }
```

---

</SwmSnippet>

## Throwing Exceptions After Saving

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Attempt to look up value for JSP page"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:960"
    node1 --> node2{"What error occurred?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:960"
    node2 -->|"Access error"| node3["Save exception and report access error
(property, name)"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:927"
    node2 -->|"Argument error"| node4["Save exception and report argument error
(property, name)"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:928:931"
    node2 -->|"Invocation error"| node5["Extract root cause, save exception, and
report invocation error (property, name)"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:932:941"
    node2 -->|"Method not found"| node6{"Is bean name default?"}
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:944:949"
    node6 -->|"Yes"| node7{"Does bean object exist?"}
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:950:954"
    node7 -->|"Yes"| node8["Use bean class name in error message"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:953:954"
    node7 -->|"No"| node9["Use default bean name in error message"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:949:952"
    node8 --> node10["Save exception and report method error
(property, beanName)"]
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:957:959"
    node9 --> node10
    node6 -->|"No"| node10

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Attempt to look up value for JSP page"]
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:960"
%%     node1 --> node2{"What error occurred?"}
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:960"
%%     node2 -->|"Access error"| node3["Save exception and report access error
%% (property, name)"]
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:927"
%%     node2 -->|"Argument error"| node4["Save exception and report argument error
%% (property, name)"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:928:931"
%%     node2 -->|"Invocation error"| node5["Extract root cause, save exception, and
%% report invocation error (property, name)"]
%%     click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:932:941"
%%     node2 -->|"Method not found"| node6{"Is bean name default?"}
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:944:949"
%%     node6 -->|"Yes"| node7{"Does bean object exist?"}
%%     click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:950:954"
%%     node7 -->|"Yes"| node8["Use bean class name in error message"]
%%     click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:953:954"
%%     node7 -->|"No"| node9["Use default bean name in error message"]
%%     click node9 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:949:952"
%%     node8 --> node10["Save exception and report method error
%% (property, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="944:3:3" line-data="            String beanName = name;">`beanName`</SwmToken>)"]
%%     click node10 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:957:959"
%%     node9 --> node10
%%     node6 -->|"No"| node10
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="925">

---

Back in `TagUtils.lookup`, after saving the exception, we throw a new <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="925:5:5" line-data="            throw new JspException(messages.getMessage(&quot;lookup.access&quot;,">`JspException`</SwmToken> with a localized message so the error is both recorded and reported.

```java
            throw new JspException(messages.getMessage("lookup.access",
                    property, name), e);
        } catch (IllegalArgumentException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="928">

---

Back in `TagUtils.lookup`, after another error (like <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="927:6:6" line-data="        } catch (IllegalArgumentException e) {">`IllegalArgumentException`</SwmToken>), we save the exception again so error handlers can access the latest error.

```java
            saveException(pageContext, e);
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="929">

---

Back in `TagUtils.lookup`, after saving the exception, we fetch another localized message for this specific error type and throw it, so the error output is clear and targeted.

```java
            throw new JspException(messages.getMessage("lookup.argument",
                    property, name), e);
        } catch (InvocationTargetException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="932">

---

Back in `TagUtils.lookup`, after catching <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="931:6:6" line-data="        } catch (InvocationTargetException e) {">`InvocationTargetException`</SwmToken>, we save the real cause (target exception) so error handlers get the actual error, not just the wrapper.

```java
            Throwable t = e.getTargetException();

            if (t == null) {
                t = e;
            }

            saveException(pageContext, t);
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="939">

---

Back in `TagUtils.lookup`, after saving the target exception, we fetch a message specific to target errors and throw it, so the error is clear about what failed.

```java
            throw new JspException(messages.getMessage("lookup.target",
                    property, name), e);
        } catch (NoSuchMethodException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="942">

---

Back in `TagUtils.lookup`, after catching <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="941:6:6" line-data="        } catch (NoSuchMethodException e) {">`NoSuchMethodException`</SwmToken>, we save it so error handlers know exactly what method was missing.

```java
            saveException(pageContext, e);

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="944">

---

Back in `TagUtils.lookup`, after saving the exception, we check if the bean name is generic (<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="949:4:6" line-data="            if (Constants.BEAN_KEY.equals(name)) {">`Constants.BEAN_KEY`</SwmToken>). If so, we include the bean's class name in the error message for better debugging, then throw the localized exception.

```java
            String beanName = name;

            // Name defaults to Contants.BEAN_KEY if no name is specified by
            // an input tag. Thus lookup the bean under the key and use
            // its class name for the exception message.
            if (Constants.BEAN_KEY.equals(name)) {
                Object obj = pageContext.findAttribute(Constants.BEAN_KEY);

                if (obj != null) {
                    beanName = obj.getClass().getName();
                }
            }

            throw new JspException(messages.getMessage("lookup.method",
                    property, beanName), e);
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
