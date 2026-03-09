---
title: Routing and Dispatching Business Actions
---
This document describes how requests are routed to the correct business action method and how the outcome is returned. When a request is received, the flow determines which action to perform, resolves the appropriate method, and executes it. If the action is missing or undefined, a localized error message is returned.

```mermaid
flowchart TD
  node1["Resolving and Routing Action Methods"]:::HeadingStyle
  click node1 goToHeading "Resolving and Routing Action Methods"
  node1 --> node2{"Is action name specified and valid?"}
  node2 -->|"No"| node3{"Is default action available?"}
  node3 -->|"No"| node4["Handling Undefined Actions"]:::HeadingStyle
  click node4 goToHeading "Handling Undefined Actions"
  node3 -->|"Yes"| node5["Resolving Action Method via Reflection"]:::HeadingStyle
  click node5 goToHeading "Resolving Action Method via Reflection"
  node2 -->|"Yes"| node5
  node5 --> node6{"Is method available?"}
  node6 -->|"No"| node7["Handling Missing Methods Securely"]:::HeadingStyle
  click node7 goToHeading "Handling Missing Methods Securely"
  node6 -->|"Yes"| node8["Executing the Method and Returning Outcome"]:::HeadingStyle
  click node8 goToHeading "Executing the Method and Returning Outcome"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Resolving and Routing Action Methods

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Determine requested business action from
request context"]
  click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:115:120"
  node1 --> node2{"Is action name specified?"}
  click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:118:120"
  node2 -->|"No"| node3{"Is default action available?"}
  click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:119:120"
  node2 -->|"Yes"| node4{"Is action name valid?"}
  click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:124:125"
  node3 -->|"No"| node5["Handling Undefined Actions"]
  
  node3 -->|"Yes"| node4
  node4 -->|"No"| node5
  node4 -->|"Yes"| node6["Caching and Resolving Method Objects"]
  
  node6 --> node7["Return result to user"]
  click node7 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:147:148"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node5 goToHeading "Handling Undefined Actions"
node5:::HeadingStyle
click node6 goToHeading "Caching and Resolving Method Objects"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Determine requested business action from
%% request context"]
%%   click node1 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:115:120"
%%   node1 --> node2{"Is action name specified?"}
%%   click node2 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:118:120"
%%   node2 -->|"No"| node3{"Is default action available?"}
%%   click node3 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:119:120"
%%   node2 -->|"Yes"| node4{"Is action name valid?"}
%%   click node4 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:124:125"
%%   node3 -->|"No"| node5["Handling Undefined Actions"]
%%   
%%   node3 -->|"Yes"| node4
%%   node4 -->|"No"| node5
%%   node4 -->|"Yes"| node6["Caching and Resolving Method Objects"]
%%   
%%   node6 --> node7["Return result to user"]
%%   click node7 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:147:148"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node5 goToHeading "Handling Undefined Actions"
%% node5:::HeadingStyle
%% click node6 goToHeading "Caching and Resolving Method Objects"
%% node6:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="115">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="115:5:5" line-data="    public Object dispatch(ActionContext context) throws Exception {">`dispatch`</SwmToken>, we try to get the method name from the context, fallback to a default if needed, and if there's still nothing, we call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="125:3:3" line-data="            return unspecified(context);">`unspecified`</SwmToken> to handle cases where the action isn't defined. This avoids running random or missing actions.

```java
    public Object dispatch(ActionContext context) throws Exception {
        // Resolve the method name; fallback to default if necessary
        String methodName = resolveMethodName(context);
        if ((methodName == null) || "".equals(methodName)) {
            methodName = getDefaultMethodName();
        }

        // Ensure there is a specified method name to invoke.
        // This may be null if the user hacks the query string.
        if (methodName == null) {
            return unspecified(context);
        }

```

---

</SwmSnippet>

## Handling Undefined Actions

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="313">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="313:5:5" line-data="    protected Object unspecified(ActionContext context) throws Exception {">`unspecified`</SwmToken>, we grab a localized error message using the action path and a constant key. This message is logged and used for the exception, so users and logs get clear info about what action was missing.

```java
    protected Object unspecified(ActionContext context) throws Exception {
        ActionConfig config = context.getActionConfig();
        String msg = messages.getMessage(MSG_KEY_UNSPECIFIED, config.getPath());
        log.error(msg);
```

---

</SwmSnippet>

### Retrieving Localized Error Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> here just calls the overload with null Locale, so we always get a message using the default language if none is specified.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

### Formatting Localized Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Request message for key and argument"] --> node2{"Is locale provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 -->|"Yes"| node3["Use provided locale"]
    node2 -->|"No"| node4["Use default locale"]
    node3 --> node5{"Is message format available for key and
locale?"}
    node4 --> node5
    node5 -->|"Yes"| node6["Format message with argument"]
    node5 -->|"No"| node7{"Does message exist for key and locale?"}
    node7 -->|"Yes"| node8["Prepare message format"]
    node8 --> node6
    node7 -->|"No"| node9{"Should missing message return null?"}
    node9 -->|"Yes"| node10["Return null"]
    node9 -->|"No"| node11["Return placeholder message"]
    node6 --> node12["Return formatted message"]
    click node12 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Request message for key and argument"] --> node2{"Is locale provided?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 -->|"Yes"| node3["Use provided locale"]
%%     node2 -->|"No"| node4["Use default locale"]
%%     node3 --> node5{"Is message format available for key and
%% locale?"}
%%     node4 --> node5
%%     node5 -->|"Yes"| node6["Format message with argument"]
%%     node5 -->|"No"| node7{"Does message exist for key and locale?"}
%%     node7 -->|"Yes"| node8["Prepare message format"]
%%     node8 --> node6
%%     node7 -->|"No"| node9{"Should missing message return null?"}
%%     node9 -->|"Yes"| node10["Return null"]
%%     node9 -->|"No"| node11["Return placeholder message"]
%%     node6 --> node12["Return formatted message"]
%%     click node12 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> here wraps the argument in an array and passes it to the next overload, so we can format messages with multiple parameters if needed.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> here handles locale fallback, caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> objects for speed, escapes format strings, and returns either a formatted message or a placeholder if the key is missing.

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

### Throwing Localized Exceptions

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="317">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="125:3:3" line-data="            return unspecified(context);">`unspecified`</SwmToken>, after getting the localized message, we log it and throw an <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="317:5:5" line-data="        throw new IllegalStateException(msg);">`IllegalStateException`</SwmToken>. This stops the flow for undefined actions and gives clear info in logs and errors.

```java
        throw new IllegalStateException(msg);
    }
```

---

</SwmSnippet>

## Resolving Action Method via Reflection

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="128">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="128:13:13" line-data="        // Identify the method object to dispatch">`dispatch`</SwmToken>, after handling unspecified cases, we use reflection to get the Method object for the resolved name. This lets us dynamically call the right action method.

```java
        // Identify the method object to dispatch
        Method method;
        try {
            method = getMethod(context, methodName);
```

---

</SwmSnippet>

## Caching and Resolving Method Objects

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="203">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="203:7:7" line-data="    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`getMethod`</SwmToken> checks the cache for a Method object using the class and method name. If it's not cached, it calls <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> to find it and then stores it for next time.

```java
    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        synchronized (methods) {
            // Key the method based on the class-method combination
            StringBuffer keyBuf = new StringBuffer(100);
            keyBuf.append(context.getAction().getClass().getName());
            keyBuf.append(":");
            keyBuf.append(methodName);
            String key = keyBuf.toString();

            Method method = (Method) methods.get(key);

            if (method == null) {
                method = resolveMethod(context, methodName);
                methods.put(key, method);
            }

            return method;
        }
    }
```

---

</SwmSnippet>

## Delegating Method Resolution

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="288">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="288:3:3" line-data="    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken> just hands off the resolution to the <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="289:3:3" line-data="        return methodResolver.resolveMethod(context, methodName);">`methodResolver`</SwmToken>, so we can support different strategies for finding the right method depending on context.

```java
    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        return methodResolver.resolveMethod(context, methodName);
    }
```

---

</SwmSnippet>

## Multi-Strategy Method Lookup

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Try to resolve method using superclass"]
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:112:114"
    node1 --> node2{"Superclass resolves method?"}
    node2 -->|"Yes"| node3["Return method from superclass"]
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:113:113"
    node2 -->|"No"| node4{"Is context a servlet action context and
method accepts servlet context?"}
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:114:116"
    node4 -->|"Yes"| node5["Return servlet-context method"]
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:119:123"
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:122:122"
    node4 -->|"No"| node6["Return classic method"]
    click node6 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:129:129"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Try to resolve method using superclass"]
%%     click node1 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:112:114"
%%     node1 --> node2{"Superclass resolves method?"}
%%     node2 -->|"Yes"| node3["Return method from superclass"]
%%     click node3 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:113:113"
%%     node2 -->|"No"| node4{"Is context a servlet action context and
%% method accepts servlet context?"}
%%     click node2 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:114:116"
%%     node4 -->|"Yes"| node5["Return servlet-context method"]
%%     click node4 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:119:123"
%%     click node5 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:122:122"
%%     node4 -->|"No"| node6["Return classic method"]
%%     click node6 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:129:129"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="110">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="110:5:5" line-data="    public Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken>, we first try the superclass, then check for methods that accept <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="67:6:6" line-data="                return buildClassicArguments((ServletActionContext) context);">`ServletActionContext`</SwmToken>, and finally fall back to classic argument resolution. This covers different action method signatures.

```java
    public Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        // First try to resolve anything the superclass supports
        try {
            return super.resolveMethod(context, methodName);
        } catch (NoSuchMethodException e) {
            // continue
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="118">

---

After returning from <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken>, we check if the context is a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> and try to find a method with that parameter. This is how servlet-specific actions are handled.

```java
        // Can the method accept the servlet action context?
        if (context instanceof ServletActionContext) {
            try {
                Class actionClass = context.getAction().getClass();
                return actionClass.getMethod(methodName, new Class[] { ServletActionContext.class });
            } catch (NoSuchMethodException e) {
                // continue
            }
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="128">

---

After checking for servlet-specific methods, we fall back to <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="129:3:3" line-data="        return resolveClassicMethod(context, methodName);">`resolveClassicMethod`</SwmToken> to handle legacy action signatures. This keeps compatibility with older action classes.

```java
        // Lastly, try the classical argument listing
        return resolveClassicMethod(context, methodName);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="105">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="105:7:7" line-data="    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveClassicMethod`</SwmToken> uses <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="107:10:10" line-data="        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);">`CLASSIC_EXECUTE_SIGNATURE`</SwmToken> to find the method by name and expected parameter types. This is how classic action methods are resolved with reflection.

```java
    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        Class actionClass = context.getAction().getClass();
        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);
    }
```

---

</SwmSnippet>

## Handling Missing Methods Securely

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Attempt to invoke method for action"] --> node2{"Is requested method available?"}
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:132:148"
    node2 -->|"No"| node3["Log error: missing method (methodName,
path)"]
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:132:148"
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:133:136"
    node3 --> node4["Throw user-friendly exception (only
action path shown)"]
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:140:143"
    node2 -->|"Yes"| node5["Invoke method and return result"]
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:146:147"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Attempt to invoke method for action"] --> node2{"Is requested method available?"}
%%     click node1 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:132:148"
%%     node2 -->|"No"| node3["Log error: missing method (<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="117:3:3" line-data="        String methodName = resolveMethodName(context);">`methodName`</SwmToken>,
%% path)"]
%%     click node2 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:132:148"
%%     click node3 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:133:136"
%%     node3 --> node4["Throw user-friendly exception (only
%% action path shown)"]
%%     click node4 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:140:143"
%%     node2 -->|"Yes"| node5["Invoke method and return result"]
%%     click node5 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:146:147"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="132">

---

After getting the method, if it's missing, we log the error with the method name but use <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="26:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> to create a sanitized user-facing message. This avoids exposing method names and prevents XSS issues.

```java
        } catch (NoSuchMethodException e) {
            // The log message reveals the offending method name...
            String path = context.getActionConfig().getPath();
            String message = messages.getMessage(MSG_KEY_MISSING_METHOD_LOG, path, methodName);
            log.error(message, e);

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="138">

---

After getting the sanitized message from <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="26:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken>, we throw a new <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="141:1:1" line-data="            NoSuchMethodException e2 = new NoSuchMethodException(userMsg);">`NoSuchMethodException`</SwmToken> with that message and set the original as the cause. This keeps debugging info for devs but hides details from users.

```java
            // ...but the exception thrown does not
            // See r383718 (XSS vulnerability)
            String userMsg = messages.getMessage(MSG_KEY_MISSING_METHOD, path);
            NoSuchMethodException e2 = new NoSuchMethodException(userMsg);
            e2.initCause(e);
            throw e2;
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="146">

---

After handling errors and resolving the method, we call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="147:3:3" line-data="        return dispatchMethod(context, method, methodName);">`dispatchMethod`</SwmToken> to actually invoke it and return the result. This is where the action logic runs.

```java
        // Invoke the named method and return its result
        return dispatchMethod(context, method, methodName);
    }
```

---

</SwmSnippet>

# Preparing Method Invocation

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="160">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="160:7:7" line-data="    protected final Object dispatchMethod(ActionContext context, Method method, String name) throws Exception {">`dispatchMethod`</SwmToken>, we grab the action, path, and build the arguments for the method. This sets up everything needed for the actual invocation.

```java
    protected final Object dispatchMethod(ActionContext context, Method method, String name) throws Exception {
        Action target = context.getAction();
        String path = context.getActionConfig().getPath();
        Object[] args = buildMethodArguments(context, method);
```

---

</SwmSnippet>

## Building Arguments for Action Methods

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="107">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="107:5:5" line-data="    Object[] buildMethodArguments(ActionContext context, Method method) {">`buildMethodArguments`</SwmToken> hands off argument building to the <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="108:9:9" line-data="        Object[] args = methodResolver.buildArguments(context, method);">`methodResolver`</SwmToken>, so we can support different method signatures and context types.

```java
    Object[] buildMethodArguments(ActionContext context, Method method) {
        Object[] args = methodResolver.buildArguments(context, method);
```

---

</SwmSnippet>

### Resolver Logic for Method Arguments

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Determine arguments for method
invocation"]
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:61:62"
    node1 --> node2{"Are default arguments available?"}
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:63:63"
    node2 -->|"Yes"| node3["Return default arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:72:73"
    node2 -->|"No"| node4{"Does method require 4 parameters?
(parameterTypes.length == 4)"}
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:65:70"
    node4 -->|"Yes (4 parameters)"| node5["Return classic arguments"]
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:67:67"
    node4 -->|"No (not 4 parameters)"| node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Determine arguments for method
%% invocation"]
%%     click node1 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:61:62"
%%     node1 --> node2{"Are default arguments available?"}
%%     click node2 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:63:63"
%%     node2 -->|"Yes"| node3["Return default arguments"]
%%     click node3 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:72:73"
%%     node2 -->|"No"| node4{"Does method require 4 parameters?
%% (<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="65:4:6" line-data="            switch (parameterTypes.length) {">`parameterTypes.length`</SwmToken> == 4)"}
%%     click node4 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:65:70"
%%     node4 -->|"Yes (4 parameters)"| node5["Return classic arguments"]
%%     click node5 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:67:67"
%%     node4 -->|"No (not 4 parameters)"| node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="61">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="61:7:7" line-data="    public Object[] buildArguments(ActionContext context, Method method) {">`buildArguments`</SwmToken> checks if the method has 4 parameters and, if so, calls <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="67:3:3" line-data="                return buildClassicArguments((ServletActionContext) context);">`buildClassicArguments`</SwmToken> with a cast context. Otherwise, it uses the superclass or returns what it got.

```java
    public Object[] buildArguments(ActionContext context, Method method) {
        Object[] args = super.buildArguments(context, method);
        if (args == null) {
            Class[] parameterTypes = method.getParameterTypes();
            switch (parameterTypes.length) {
            case 4:
                return buildClassicArguments((ServletActionContext) context);
            default:
                break;
            }
        }
        return args;
    }
```

---

</SwmSnippet>

### Building Classic Action Arguments

See <SwmLink doc-title="Preparing Action Method Parameters">[Preparing Action Method Parameters](/.swm/preparing-action-method-parameters.4w2cbx9c.sw.md)</SwmLink>

### Validating Built Arguments

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="109">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="107:5:5" line-data="    Object[] buildMethodArguments(ActionContext context, Method method) {">`buildMethodArguments`</SwmToken>, if the resolver returns null, we throw an exception with the method signature. This catches unsupported methods right away.

```java
        if (args == null) {
            throw new IllegalStateException("Unsupported method signature: " + method.toString());
        }
        return args;
    }
```

---

</SwmSnippet>

## Invoking the Action Method

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="164">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="147:3:3" line-data="        return dispatchMethod(context, method, methodName);">`dispatchMethod`</SwmToken>, after building arguments, we call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="164:3:3" line-data="        return invoke(target, method, args, path);">`invoke`</SwmToken> to run the method and return its result. This is where the action logic actually executes.

```java
        return invoke(target, method, args, path);
    }
```

---

</SwmSnippet>

# Executing the Method and Returning Outcome

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="234">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="234:7:7" line-data="    protected final Object invoke(Object target, Method method, Object[] args, String path) throws Exception {">`invoke`</SwmToken>, we use reflection to call the method on the target with the built arguments. The result is captured for return.

```java
    protected final Object invoke(Object target, Method method, Object[] args, String path) throws Exception {
        try {
            Object retval = method.invoke(target, args);
```

---

</SwmSnippet>

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" line="356">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="356:5:5" line-data="    public Object invoke(FacesContext context, Object params[]) {">`invoke`</SwmToken> in <SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="39:4:4" line-data="public class CommandLinkTag extends AbstractFacesTag {">`CommandLinkTag`</SwmToken> just returns <SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="357:4:6" line-data="        return (this.outcome);">`this.outcome`</SwmToken> and ignores the context and params. The result is fixed by the object's state, not the inputs.

```java
    public Object invoke(FacesContext context, Object params[]) {
        return (this.outcome);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="237">

---

Back in AbstractDispatcher.invoke, after running the action method, we check for void returns, then handle exceptions. If there's an error, we call MessageResources.getMessage to fetch a localized error message for logging and exception text. This keeps error output consistent and localizable, instead of hardcoding strings.

```java
            if (method.getReturnType() == void.class) {
                retval = void.class;
            }
            return retval;
        } catch (IllegalAccessException e) {
            String message = messages.getMessage(MSG_KEY_DISPATCH_ERROR, path);
            log.error(message + ":" + method.getName(), e);
            throw e;
        } catch (InvocationTargetException e) {
            // Rethrow the target exception if possible so that the
            // exception handling machinery can deal with it
            Throwable t = e.getTargetException();
            if (t instanceof Exception) {
                throw (Exception) t;
            } else {
                String message = messages.getMessage(MSG_KEY_DISPATCH_ERROR, path);
                log.error(message + ":" + method.getName(), e);
                throw new Exception(t);
            }
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
