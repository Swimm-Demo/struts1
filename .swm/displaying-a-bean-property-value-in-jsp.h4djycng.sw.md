---
title: Displaying a bean property value in JSP
---
This document describes how a bean property value is dynamically displayed on a JSP page. The flow checks if the property should be shown, retrieves the bean and property, applies formatting and localization rules, and outputs the value for the user.

```mermaid
flowchart TD
  node1["Evaluating Whether to Output the Bean Property"]:::HeadingStyle
  click node1 goToHeading "Evaluating Whether to Output the Bean Property"
  node1 -->|"Output required"| node2["Resolving Bean and Scope for Lookup"]:::HeadingStyle
  click node2 goToHeading "Resolving Bean and Scope for Lookup"
  node2 --> node3["Retrieving the Bean Property Value"]:::HeadingStyle
  click node3 goToHeading "Retrieving the Bean Property Value"
  node3 -->|"Value present"| node4["Formatting the Output Value"]:::HeadingStyle
  click node4 goToHeading "Formatting the Output Value"
  node4 --> node5["Outputting the Formatted Value"]:::HeadingStyle
  click node5 goToHeading "Outputting the Formatted Value"
  node1 -->|"Skip output"| node5
  node3 -->|"Value missing"| node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Evaluating Whether to Output the Bean Property

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Ignore is true and bean is missing?"}
    node1 -->|"Yes"| node5["Skip output"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:225:230"
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:228:229"
    node1 -->|"No"| node2["Resolving Bean and Scope for Lookup"]
    
    node2 --> node3{"Is property value present?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:236:238"
    node3 -->|"No"| node5
    node3 -->|"Yes"| node4["Formatting the Output Value"]
    
    node4 --> node5["Output value (filtered or as is)"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:243:249"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Resolving Bean and Scope for Lookup"
node2:::HeadingStyle
click node4 goToHeading "Formatting the Output Value"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Ignore is true and bean is missing?"}
%%     node1 -->|"Yes"| node5["Skip output"]
%%     click node1 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:225:230"
%%     click node5 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:228:229"
%%     node1 -->|"No"| node2["Resolving Bean and Scope for Lookup"]
%%     
%%     node2 --> node3{"Is property value present?"}
%%     click node3 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:236:238"
%%     node3 -->|"No"| node5
%%     node3 -->|"Yes"| node4["Formatting the Output Value"]
%%     
%%     node4 --> node5["Output value (filtered or as is)"]
%%     click node5 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:243:249"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Resolving Bean and Scope for Lookup"
%% node2:::HeadingStyle
%% click node4 goToHeading "Formatting the Output Value"
%% node4:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="223">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="223:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we check if the bean should be ignored and, if so, use <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="226:4:4" line-data="            if (TagUtils.getInstance().lookup(pageContext, name, scope)">`TagUtils`</SwmToken> to see if the bean exists in the given scope. If not found, we skip output. Calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="226:4:4" line-data="            if (TagUtils.getInstance().lookup(pageContext, name, scope)">`TagUtils`</SwmToken> here centralizes the lookup logic and handles scope resolution consistently.

```java
    public int doStartTag() throws JspException {
        // Look up the requested bean (if necessary)
        if (ignore) {
            if (TagUtils.getInstance().lookup(pageContext, name, scope)
                == null) {
                return (SKIP_BODY); // Nothing to output
            }
        }

```

---

</SwmSnippet>

## Resolving Bean and Scope for Lookup

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start lookup for value 'name'"] --> node2{"Is a scope specified?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:863:864"
    node2 -->|"No"| node3["Find value in any scope"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:865:866"
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:866:867"
    node2 -->|"Yes"| node4{"Is scope name valid? (request, session,
application, page)"}
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:869:870"
    node4 -->|"Yes"| node5["Find value in specified scope"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:870:871"
    node4 -->|"No"| node6["Return error: Invalid scope"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:814:815"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start lookup for value 'name'"] --> node2{"Is a scope specified?"}
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:863:864"
%%     node2 -->|"No"| node3["Find value in any scope"]
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:865:866"
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:866:867"
%%     node2 -->|"Yes"| node4{"Is scope name valid? (request, session,
%% application, page)"}
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:869:870"
%%     node4 -->|"Yes"| node5["Find value in specified scope"]
%%     click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:870:871"
%%     node4 -->|"No"| node6["Return error: Invalid scope"]
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:814:815"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="863">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`lookup`</SwmToken> handles finding the bean by name, either across all scopes or in a specific one. If a scope is given, it resolves the scope integer using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="870:12:12" line-data="            return pageContext.getAttribute(name, instance.getScope(scopeName));">`getScope`</SwmToken>. This lets us abstract bean retrieval and handle errors in one place.

```java
    public Object lookup(PageContext pageContext, String name, String scopeName)
        throws JspException {
        if (scopeName == null) {
            return pageContext.findAttribute(name);
        }

        try {
            return pageContext.getAttribute(name, instance.getScope(scopeName));
        } catch (JspException e) {
            saveException(pageContext, e);
            throw e;
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="809">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="809:5:5" line-data="    public int getScope(String scopeName)">`getScope`</SwmToken> converts the scope name to lowercase and looks it up in a map. If not found, it throws a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="810:3:3" line-data="        throws JspException {">`JspException`</SwmToken> with a localized message. This ensures only valid, recognized scopes are used and errors are explicit.

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

## Retrieving the Bean Property Value

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Look up property value from page context"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:232:234"
    node1 --> node2{"Is value present?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:236:238"
    node2 -->|"Yes"| node3["Format value for output"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:240:241"
    node3 --> node4["Output formatted value"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:240:241"
    node2 -->|"No"| node5["Skip output and end"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:237:238"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Look up property value from page context"]
%%     click node1 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:232:234"
%%     node1 --> node2{"Is value present?"}
%%     click node2 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:236:238"
%%     node2 -->|"Yes"| node3["Format value for output"]
%%     click node3 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:240:241"
%%     node3 --> node4["Output formatted value"]
%%     click node4 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:240:241"
%%     node2 -->|"No"| node5["Skip output and end"]
%%     click node5 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:237:238"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="232">

---

Back in WriteTag.doStartTag, after getting the bean, we use <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="234:1:1" line-data="            TagUtils.getInstance().lookup(pageContext, name, property, scope);">`TagUtils`</SwmToken> again to fetch the property value. If it's null, we skip output. This keeps the tag from rendering anything if the property doesn't exist.

```java
        // Look up the requested property value
        Object value =
            TagUtils.getInstance().lookup(pageContext, name, property, scope);

        if (value == null) {
            return (SKIP_BODY); // Nothing to output
        }

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="897">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="897:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String property,">`lookup`</SwmToken> (with property) first finds the bean, then retrieves the property using reflection. If the property is missing or inaccessible, it throws a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="898:8:8" line-data="        String scope) throws JspException {">`JspException`</SwmToken> with a localized message. Special handling is included for the default bean key.

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
                e = new JspException(messages.getMessage("lookup.bean", name,
                            scope));
            }

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
            throw new JspException(messages.getMessage("lookup.access",
                    property, name), e);
        } catch (IllegalArgumentException e) {
            saveException(pageContext, e);
            throw new JspException(messages.getMessage("lookup.argument",
                    property, name), e);
        } catch (InvocationTargetException e) {
            Throwable t = e.getTargetException();

            if (t == null) {
                t = e;
            }

            saveException(pageContext, t);
            throw new JspException(messages.getMessage("lookup.target",
                    property, name), e);
        } catch (NoSuchMethodException e) {
            saveException(pageContext, e);

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

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="240">

---

Back in WriteTag.doStartTag, after getting the property value, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="241:7:7" line-data="        String output = formatValue(value);">`formatValue`</SwmToken> to convert it to a properly formatted string. This step handles locale and formatting rules before anything is written out.

```java
        // Convert value to the String with some formatting
        String output = formatValue(value);

```

---

</SwmSnippet>

## Formatting the Output Value

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="291">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="291:5:5" line-data="    protected String formatValue(Object valueToFormat)">`formatValue`</SwmToken>, we prep for formatting by getting the user's locale using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="296:1:1" line-data="            TagUtils.getInstance().getUserLocale(pageContext, this.localeKey);">`TagUtils`</SwmToken>. This is needed to apply the right number or date formats for the user's region.

```java
    protected String formatValue(Object valueToFormat)
        throws JspException {
        Format format = null;
        Object value = valueToFormat;
        Locale locale =
            TagUtils.getInstance().getUserLocale(pageContext, this.localeKey);
```

---

</SwmSnippet>

### Determining the User's Locale

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["User requests a page"] --> node2{"Is there a session?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:830:833"
    node2 -->|"Yes"| node3{"Is a user-specific locale set in
session?"}
    click node2 openCode "core/src/main/java/org/apache/struts/util/RequestUtils.java:301:309"
    node2 -->|"No"| node5["Use browser's language (Accept-Language)
or server default"]
    click node5 openCode "core/src/main/java/org/apache/struts/util/RequestUtils.java:314:315"
    node3 -->|"Yes"| node4["Use user's session locale"]
    click node3 openCode "core/src/main/java/org/apache/struts/util/RequestUtils.java:309:310"
    node3 -->|"No"| node5
    node4 --> node6["Show page in user's language"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/RequestUtils.java:317:318"
    node5 --> node6
    click node6 openCode "core/src/main/java/org/apache/struts/util/RequestUtils.java:317:318"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["User requests a page"] --> node2{"Is there a session?"}
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:830:833"
%%     node2 -->|"Yes"| node3{"Is a user-specific locale set in
%% session?"}
%%     click node2 openCode "<SwmPath>[core/…/util/RequestUtils.java](core/src/main/java/org/apache/struts/util/RequestUtils.java)</SwmPath>:301:309"
%%     node2 -->|"No"| node5["Use browser's language (<SwmToken path="core/src/main/java/org/apache/struts/util/RequestUtils.java" pos="313:11:13" line-data="            // Returns Locale based on Accept-Language header or the server default">`Accept-Language`</SwmToken>)
%% or server default"]
%%     click node5 openCode "<SwmPath>[core/…/util/RequestUtils.java](core/src/main/java/org/apache/struts/util/RequestUtils.java)</SwmPath>:314:315"
%%     node3 -->|"Yes"| node4["Use user's session locale"]
%%     click node3 openCode "<SwmPath>[core/…/util/RequestUtils.java](core/src/main/java/org/apache/struts/util/RequestUtils.java)</SwmPath>:309:310"
%%     node3 -->|"No"| node5
%%     node4 --> node6["Show page in user's language"]
%%     click node4 openCode "<SwmPath>[core/…/util/RequestUtils.java](core/src/main/java/org/apache/struts/util/RequestUtils.java)</SwmPath>:317:318"
%%     node5 --> node6
%%     click node6 openCode "<SwmPath>[core/…/util/RequestUtils.java](core/src/main/java/org/apache/struts/util/RequestUtils.java)</SwmPath>:317:318"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="830">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="830:5:5" line-data="    public Locale getUserLocale(PageContext pageContext, String locale) {">`getUserLocale`</SwmToken> in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="226:4:4" line-data="            if (TagUtils.getInstance().lookup(pageContext, name, scope)">`TagUtils`</SwmToken> just passes the request and locale key to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="831:3:3" line-data="        return RequestUtils.getUserLocale((HttpServletRequest) pageContext">`RequestUtils`</SwmToken>, which handles the actual lookup. This keeps locale logic in one place and supports both session and request-based locale detection.

```java
    public Locale getUserLocale(PageContext pageContext, String locale) {
        return RequestUtils.getUserLocale((HttpServletRequest) pageContext
            .getRequest(), locale);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/RequestUtils.java" line="299">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/RequestUtils.java" pos="299:7:7" line-data="    public static Locale getUserLocale(HttpServletRequest request, String locale) {">`getUserLocale`</SwmToken> in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="831:3:3" line-data="        return RequestUtils.getUserLocale((HttpServletRequest) pageContext">`RequestUtils`</SwmToken> checks for a Locale in the session using a key (defaulting to <SwmToken path="core/src/main/java/org/apache/struts/util/RequestUtils.java" pos="304:5:7" line-data="            locale = Globals.LOCALE_KEY;">`Globals.LOCALE_KEY`</SwmToken>). If not found, it falls back to the request's locale, which is usually set by the browser's language settings.

```java
    public static Locale getUserLocale(HttpServletRequest request, String locale) {
        Locale userLocale = null;
        HttpSession session = request.getSession(false);

        if (locale == null) {
            locale = Globals.LOCALE_KEY;
        }

        // Only check session if sessions are enabled
        if (session != null) {
            userLocale = (Locale) session.getAttribute(locale);
        }

        if (userLocale == null) {
            // Returns Locale based on Accept-Language header or the server default
            userLocale = request.getLocale();
        }

        return userLocale;
    }
```

---

</SwmSnippet>

### Applying Formatting Rules to the Value

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is value a String?"}
  node1 -->|"Yes"| node2["Return value as-is"]
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:301:303"
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:302:302"
  node1 -->|"No"| node3{"Is value a Number?"}
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:315:352"
  node3 -->|"Yes"| node4{"Is a number format pattern available?
(formatKey or type)"}
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:316:331"
  node4 -->|"Yes"| node5["Format value using number pattern"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:333:343"
  node5 --> node12["Return formatted value"]
  click node12 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:375:375"
  node4 -->|"No"| node13["Return value as string"]
  click node13 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:377:378"
  node3 -->|"No"| node7{"Is value a Date?"}
  click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:352:371"
  node7 -->|"Yes"| node8{"Is a date format pattern available?
(formatKey or type)"}
  click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:353:366"
  node8 -->|"Yes"| node9["Format value using date pattern"]
  click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java:368:370"
  node9 --> node12
  node8 -->|"No"| node13
  node7 -->|"No"| node13
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is value a String?"}
%%   node1 -->|"Yes"| node2["Return value as-is"]
%%   click node1 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:301:303"
%%   click node2 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:302:302"
%%   node1 -->|"No"| node3{"Is value a Number?"}
%%   click node3 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:315:352"
%%   node3 -->|"Yes"| node4{"Is a number format pattern available?
%% (<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="305:3:3" line-data="            // formatKey.">`formatKey`</SwmToken> or type)"}
%%   click node4 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:316:331"
%%   node4 -->|"Yes"| node5["Format value using number pattern"]
%%   click node5 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:333:343"
%%   node5 --> node12["Return formatted value"]
%%   click node12 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:375:375"
%%   node4 -->|"No"| node13["Return value as string"]
%%   click node13 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:377:378"
%%   node3 -->|"No"| node7{"Is value a Date?"}
%%   click node7 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:352:371"
%%   node7 -->|"Yes"| node8{"Is a date format pattern available?
%% (<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="305:3:3" line-data="            // formatKey.">`formatKey`</SwmToken> or type)"}
%%   click node8 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:353:366"
%%   node8 -->|"Yes"| node9["Format value using date pattern"]
%%   click node9 openCode "<SwmPath>[taglib/…/bean/WriteTag.java](taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java)</SwmPath>:368:370"
%%   node9 --> node12
%%   node8 -->|"No"| node13
%%   node7 -->|"No"| node13
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="297">

---

Back in WriteTag.formatValue, we branch based on the value type: strings are returned directly, numbers and dates get formatted using patterns (from resources or defaults) and the user's locale. If formatting fails, we throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" pos="344:1:1" line-data="                        JspException ex =">`JspException`</SwmToken>.

```java
        boolean formatStrFromResources = false;
        String formatString = formatStr;

        // Return String object as is.
        if (value instanceof java.lang.String) {
            return (String) value;
        } else {
            // Try to retrieve format string from resources by the key from
            // formatKey.
            if ((formatString == null) && (formatKey != null)) {
                formatString = retrieveFormatString(this.formatKey);

                if (formatString != null) {
                    formatStrFromResources = true;
                }
            }

            // Prepare format object for numeric values.
            if (value instanceof Number) {
                if (formatString == null) {
                    if ((value instanceof Byte) || (value instanceof Short)
                        || (value instanceof Integer)
                        || (value instanceof Long)
                        || (value instanceof BigInteger)) {
                        formatString = retrieveFormatString(INT_FORMAT_KEY);
                    } else if ((value instanceof Float)
                        || (value instanceof Double)
                        || (value instanceof BigDecimal)) {
                        formatString = retrieveFormatString(FLOAT_FORMAT_KEY);
                    }

                    if (formatString != null) {
                        formatStrFromResources = true;
                    }
                }

                if (formatString != null) {
                    try {
                        format = NumberFormat.getNumberInstance(locale);

                        if (formatStrFromResources) {
                            ((DecimalFormat) format).applyLocalizedPattern(
                                formatString);
                        } else {
                            ((DecimalFormat) format).applyPattern(formatString);
                        }
                    } catch (IllegalArgumentException e) {
                        JspException ex =
                            new JspException(messages.getMessage(
                                    "write.format", formatString), e);

                        TagUtils.getInstance().saveException(pageContext, ex);
                        throw ex;
                    }
                }
            } else if (value instanceof java.util.Date) {
                if (formatString == null) {
                    if (value instanceof java.sql.Timestamp) {
                        formatString =
                            retrieveFormatString(SQL_TIMESTAMP_FORMAT_KEY);
                    } else if (value instanceof java.sql.Date) {
                        formatString =
                            retrieveFormatString(SQL_DATE_FORMAT_KEY);
                    } else if (value instanceof java.sql.Time) {
                        formatString =
                            retrieveFormatString(SQL_TIME_FORMAT_KEY);
                    } else if (value instanceof java.util.Date) {
                        formatString = retrieveFormatString(DATE_FORMAT_KEY);
                    }
                }

                if (formatString != null) {
                    format = new SimpleDateFormat(formatString, locale);
                }
            }
        }

        if (format != null) {
            return format.format(value);
        } else {
            return value.toString();
        }
    }
```

---

</SwmSnippet>

## Outputting the Formatted Value

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/bean/WriteTag.java" line="243">

---

Back in WriteTag.doStartTag, after formatting, we write the output to the page. If filtering is enabled, we escape HTML characters to prevent XSS; otherwise, we write the raw string. Then we finish the tag processing.

```java
        // Print this property value to our output writer, suitably filtered
        if (filter) {
            TagUtils.getInstance().write(pageContext,
                TagUtils.getInstance().filter(output));
        } else {
            TagUtils.getInstance().write(pageContext, output);
        }

        // Continue processing this page
        return (SKIP_BODY);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
