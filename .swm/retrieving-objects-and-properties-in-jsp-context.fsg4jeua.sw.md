---
title: Retrieving Objects and Properties in JSP Context
---
Retrieving an object or its property from the JSP context allows pages to dynamically access data by name and scope. If the requested object or property is not found, a localized error message is generated and stored for error handling. This supports flexible page rendering and user-friendly error reporting.

```mermaid
flowchart TD
  node1["Resolving Beans in JSP Context"]:::HeadingStyle
  click node1 goToHeading "Resolving Beans in JSP Context"
  node1 --> node2{"Is object found?"}
  node2 -->|"No"| node3["Throwing Localized Exceptions for Missing Beans"]:::HeadingStyle
  click node3 goToHeading "Throwing Localized Exceptions for Missing Beans"
  node2 -->|"Yes"| node4{"Is property specified?"}
  node4 -->|"No"| node5["Object or property retrieved"]
  node4 -->|"Yes"| node6["Throwing Exceptions for Property Access Errors"]:::HeadingStyle
  click node6 goToHeading "Throwing Exceptions for Property Access Errors"
  node6 --> node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Resolving Beans in JSP Context

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Find object by 'name' in 'scope'"] --> node2{"Is object found?"}
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:897:901"
  node2 -->|"No"| node3["Storing Exceptions in the Request Scope"]
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:902:914"
  
  node2 -->|"Yes"| node4{"Is 'property' specified?"}
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:916:918"
  node4 -->|"No"| node5["Return the object"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:917:918"
  node4 -->|"Yes"| node6["Storing Exceptions in the Request Scope"]
  

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Fetching Localized Error Messages"
node3:::HeadingStyle
click node3 goToHeading "Storing Exceptions in the Request Scope"
node3:::HeadingStyle
click node6 goToHeading "Preparing Message Arguments for Formatting"
node6:::HeadingStyle
click node6 goToHeading "Storing Exceptions in the Request Scope"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Find object by 'name' in 'scope'"] --> node2{"Is object found?"}
%%   click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:897:901"
%%   node2 -->|"No"| node3["Storing Exceptions in the Request Scope"]
%%   click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:902:914"
%%   
%%   node2 -->|"Yes"| node4{"Is 'property' specified?"}
%%   click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:916:918"
%%   node4 -->|"No"| node5["Return the object"]
%%   click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:917:918"
%%   node4 -->|"Yes"| node6["Storing Exceptions in the Request Scope"]
%%   
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Fetching Localized Error Messages"
%% node3:::HeadingStyle
%% click node3 goToHeading "Storing Exceptions in the Request Scope"
%% node3:::HeadingStyle
%% click node6 goToHeading "Preparing Message Arguments for Formatting"
%% node6:::HeadingStyle
%% click node6 goToHeading "Storing Exceptions in the Request Scope"
%% node6:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="897">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="897:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String property,">`lookup`</SwmToken>, we try to find the bean in the given scope. If it's not there, we prep a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="898:8:8" line-data="        String scope) throws JspException {">`JspException`</SwmToken> and grab a localized error message from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> to make the error clear and consistent.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:3:3" line-data="    public String getMessage(String key, Object arg0) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:12:12" line-data="    public String getMessage(String key, Object arg0) {">`Object`</SwmToken>`)` just forwards to the main overload with a null locale, so we always get a message, even if no locale is set.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Preparing Message Arguments for Formatting

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Request message by key and argument for
a locale"] --> node2{"Is locale provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 -->|"Yes"| node3["Use provided locale"]
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node2 -->|"No"| node3
    node3 --> node4["Find message template for key and locale"]
    click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node4 --> node5{"Does message template exist?"}
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:292:311"
    node5 -->|"Yes"| node6["Format message with argument"]
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    node5 -->|"No"| node7{"Should return null for missing message?"}
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
    node7 -->|"Yes"| node8["Return null"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:302"
    node7 -->|"No"| node9["Return placeholder indicating missing
key"]
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node6 --> node10["Return formatted message"]
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Request message by key and argument for
%% a locale"] --> node2{"Is locale provided?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 -->|"Yes"| node3["Use provided locale"]
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node2 -->|"No"| node3
%%     node3 --> node4["Find message template for key and locale"]
%%     click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node4 --> node5{"Does message template exist?"}
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:292:311"
%%     node5 -->|"Yes"| node6["Format message with argument"]
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     node5 -->|"No"| node7{"Should return null for missing message?"}
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%     node7 -->|"Yes"| node8["Return null"]
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:302"
%%     node7 -->|"No"| node9["Return placeholder indicating missing
%% key"]
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node6 --> node10["Return formatted message"]
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:7:7" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`Locale`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:3:3" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:17:17" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`Object`</SwmToken>`)` just wraps the argument in an array and sends it to the main formatter, so all the formatting logic stays in one place.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:7:7" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`Locale`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:3:3" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:17:17" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`Object`</SwmToken>`[])` does the heavy lifting: it caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> objects for speed, falls back to a default locale if needed, and returns a placeholder if the message is missing. All message formatting goes through here for consistency and performance.

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

## Throwing Localized Exceptions for Missing Beans

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="908">

---

Back in `TagUtils.lookup`, after getting the localized error message, we throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="908:7:7" line-data="                e = new JspException(messages.getMessage(&quot;lookup.bean&quot;, name,">`JspException`</SwmToken> with it. This keeps error reporting clear and localized.

```java
                e = new JspException(messages.getMessage("lookup.bean", name,
                            scope));
            }

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="912">

---

Still in `TagUtils.lookup`, right after throwing the exception, we save it in the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="897:7:7" line-data="    public Object lookup(PageContext pageContext, String name, String property,">`PageContext`</SwmToken>. This way, error handlers or custom error pages can pick it up and show more useful info.

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

## Storing Exceptions in the Request Scope

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="1170">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1170:5:5" line-data="    public void saveException(PageContext pageContext, Throwable exception) {">`saveException`</SwmToken> just puts the exception in the request scope with a known key. This keeps the error tied to the current request, so error pages can find it easily.

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

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="290:7:7" line-data="    public static void setAttribute(PageContext pageContext, String name, Object beanValue)">`setAttribute`</SwmToken> always puts the attribute in the request scope, no matter what. This isn't obvious from the method name, but it keeps exception data isolated to just this request.

```java
    public static void setAttribute(PageContext pageContext, String name, Object beanValue)
        throws JspException {
        pageContext.setAttribute(name, beanValue, PageContext.REQUEST_SCOPE);
    }
```

---

</SwmSnippet>

## Throwing Exceptions for Property Access Errors

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Attempt to retrieve property from bean"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:960"
    node1 --> node2{"What type of error occurred?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:960"
    node2 -->|"Access error"| node3["Save exception and show access error
message with property and bean name"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:925:927"
    node2 -->|"Argument error"| node4["Save exception and show argument error
message with property and bean name"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:928:930"
    node2 -->|"Target error"| node5["Save exception and show target error
message with property and bean name"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:932:940"
    node2 -->|"Method not found"| node6{"Is bean name the default key?"}
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:944:949"
    node6 -->|"Yes"| node7["Use actual class name, save exception,
and show method error message"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:950:958"
    node6 -->|"No"| node8["Save exception and show method error
message with property and bean name"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:942:958"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Attempt to retrieve property from bean"]
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:960"
%%     node1 --> node2{"What type of error occurred?"}
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:960"
%%     node2 -->|"Access error"| node3["Save exception and show access error
%% message with property and bean name"]
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:925:927"
%%     node2 -->|"Argument error"| node4["Save exception and show argument error
%% message with property and bean name"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:928:930"
%%     node2 -->|"Target error"| node5["Save exception and show target error
%% message with property and bean name"]
%%     click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:932:940"
%%     node2 -->|"Method not found"| node6{"Is bean name the default key?"}
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:944:949"
%%     node6 -->|"Yes"| node7["Use actual class name, save exception,
%% and show method error message"]
%%     click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:950:958"
%%     node6 -->|"No"| node8["Save exception and show method error
%% message with property and bean name"]
%%     click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:942:958"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="925">

---

Back in `TagUtils.lookup`, after saving the exception, we grab another localized message for this specific error type and throw a new <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="925:5:5" line-data="            throw new JspException(messages.getMessage(&quot;lookup.access&quot;,">`JspException`</SwmToken> with it. Keeps error reporting precise.

```java
            throw new JspException(messages.getMessage("lookup.access",
                    property, name), e);
        } catch (IllegalArgumentException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="928">

---

In `TagUtils.lookup`, after getting the error message, we save the exception again before throwing it. This keeps the error context available for every failure type.

```java
            saveException(pageContext, e);
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="929">

---

After saving the exception in `TagUtils.lookup`, we fetch a new message tailored to the specific error and throw it. Each error type gets its own message for clarity.

```java
            throw new JspException(messages.getMessage("lookup.argument",
                    property, name), e);
        } catch (InvocationTargetException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="932">

---

In `TagUtils.lookup`, when we hit an <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="931:6:6" line-data="        } catch (InvocationTargetException e) {">`InvocationTargetException`</SwmToken>, we pull out the real cause and save that. This way, error handlers see the actual problem, not just the wrapper.

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

After saving the real cause in `TagUtils.lookup`, we grab a message that includes the property and bean name, then throw the exception. This makes the error output specific and useful.

```java
            throw new JspException(messages.getMessage("lookup.target",
                    property, name), e);
        } catch (NoSuchMethodException e) {
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="942">

---

In `TagUtils.lookup`, we save the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="941:6:6" line-data="        } catch (NoSuchMethodException e) {">`NoSuchMethodException`</SwmToken> just like the others, so every error is logged and available for error handling.

```java
            saveException(pageContext, e);

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="944">

---

At the end of `TagUtils.lookup`, if the method is missing, we check if the bean name is the default key and swap in the actual class name if possible. Then we throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="957:5:5" line-data="            throw new JspException(messages.getMessage(&quot;lookup.method&quot;,">`JspException`</SwmToken> with a message that tells you exactly what method was missing on which bean.

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
