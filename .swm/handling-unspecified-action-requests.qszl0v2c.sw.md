---
title: Handling Unspecified Action Requests
---
This document explains how the system processes requests when no specific action method is provided. The flow determines the appropriate method to invoke using multiple resolution strategies, enabling flexible event dispatching. If a suitable method is found, it is invoked and the result is returned; otherwise, a localized error message is generated and an error is returned.

```mermaid
flowchart TD
  node1["Handling Unspecified Event Dispatch"]:::HeadingStyle
  click node1 goToHeading "Handling Unspecified Event Dispatch"
  node1 --> node2["Retrieving and Caching Action Methods"]:::HeadingStyle
  click node2 goToHeading "Retrieving and Caching Action Methods"
  node2 --> node3{"Is suitable method found?"}
  node3 -->|"Yes"| node4["Dispatching to the Action Method"]:::HeadingStyle
  click node4 goToHeading "Dispatching to the Action Method"
  node3 -->|"No"| node5["Handling Missing Action Methods"]:::HeadingStyle
  click node5 goToHeading "Handling Missing Action Methods"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Handling Unspecified Event Dispatch

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Receive request with unspecified action"]
  click node1 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:139:145"
  node1 --> node2["Retrieving and Caching Action Methods"]
  
  node2 --> node3{"Is default handler found?"}
  click node3 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:147:148"
  node3 -->|"Yes"| node4["Dispatch to default handler method"]
  click node4 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:157:158"
  node3 -->|"No"| node5["Fetching Localized Error Messages"]
  
  node5 --> node6["Log error with action parameter"]
  click node6 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:152:153"
  node6 --> node7["Formatting Localized Messages"]
  
  node7 --> node8["Throw error to indicate missing handler"]
  click node8 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:154:155"
  node4 --> node9["Action handled by default method"]
  click node9 openCode "extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java:157:158"
  node8 --> node9
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Retrieving and Caching Action Methods"
node2:::HeadingStyle
click node5 goToHeading "Fetching Localized Error Messages"
node5:::HeadingStyle
click node7 goToHeading "Formatting Localized Messages"
node7:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Receive request with unspecified action"]
%%   click node1 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:139:145"
%%   node1 --> node2["Retrieving and Caching Action Methods"]
%%   
%%   node2 --> node3{"Is default handler found?"}
%%   click node3 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:147:148"
%%   node3 -->|"Yes"| node4["Dispatch to default handler method"]
%%   click node4 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:157:158"
%%   node3 -->|"No"| node5["Fetching Localized Error Messages"]
%%   
%%   node5 --> node6["Log error with action parameter"]
%%   click node6 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:152:153"
%%   node6 --> node7["Formatting Localized Messages"]
%%   
%%   node7 --> node8["Throw error to indicate missing handler"]
%%   click node8 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:154:155"
%%   node4 --> node9["Action handled by default method"]
%%   click node9 openCode "<SwmPath>[extras/…/actions/EventActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java)</SwmPath>:157:158"
%%   node8 --> node9
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Retrieving and Caching Action Methods"
%% node2:::HeadingStyle
%% click node5 goToHeading "Fetching Localized Error Messages"
%% node5:::HeadingStyle
%% click node7 goToHeading "Formatting Localized Messages"
%% node7:::HeadingStyle
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" line="139">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="139:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, we start by setting up to look for a method named 'unspecified' and try to retrieve it. We need to call ActionDispatcher.getMethod next to actually fetch the Method object using reflection, so we can invoke it if found.

```java
    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        // Identify if there is an "unspecified" method to be dispatched to
        String name = "unspecified";
        Method method = null;

        try {
            method = getMethod(name);
```

---

</SwmSnippet>

## Retrieving and Caching Action Methods

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="410">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="410:5:5" line-data="    protected Method getMethod(String name)">`getMethod`</SwmToken> checks the cache for the method, and if it's not there, uses reflection to find it and then caches it. We call AbstractDispatcher.getMethod next because it handles more complex method lookup scenarios, including context-aware keys.

```java
    protected Method getMethod(String name)
        throws NoSuchMethodException {
        synchronized (methods) {
            Method method = (Method) methods.get(name);

            if (method == null) {
                method = clazz.getMethod(name, types);
                methods.put(name, method);
            }

            return (method);
        }
    }
```

---

</SwmSnippet>

## Context-Aware Method Lookup

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="203">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="203:7:7" line-data="    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`getMethod`</SwmToken> here builds a cache key from the action class and method name, checks for a cached Method, and if not found, calls <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> to find it. We call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> next to handle the actual lookup logic, including fallback strategies.

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

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="288:3:3" line-data="    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken> just hands off the lookup to <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="289:3:3" line-data="        return methodResolver.resolveMethod(context, methodName);">`methodResolver`</SwmToken>, which could use different strategies depending on context. We call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="49:4:4" line-data="public class ServletMethodResolver extends AbstractMethodResolver {">`ServletMethodResolver`</SwmToken> next because it can handle servlet-specific cases and fallback logic.

```java
    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        return methodResolver.resolveMethod(context, methodName);
    }
```

---

</SwmSnippet>

## Multi-Strategy Method Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Attempt to resolve method using standard
logic (superclass)"] --> node2{"Was method found?"}
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:112:114"
    node2 -->|"Yes"| node3["Return resolved method"]
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:113:113"
    node2 -->|"No"| node4{"Is context a ServletActionContext and
does method accept servlet context?"}
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:119:126"
    node4 -->|"Yes"| node5["Return servlet-specific method"]
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:121:122"
    node4 -->|"No"| node6["Return method using classic signature
(fallback)"]
    click node6 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:129:129"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Attempt to resolve method using standard
%% logic (superclass)"] --> node2{"Was method found?"}
%%     click node1 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:112:114"
%%     node2 -->|"Yes"| node3["Return resolved method"]
%%     click node2 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:113:113"
%%     node2 -->|"No"| node4{"Is context a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> and
%% does method accept servlet context?"}
%%     click node4 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:119:126"
%%     node4 -->|"Yes"| node5["Return servlet-specific method"]
%%     click node5 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:121:122"
%%     node4 -->|"No"| node6["Return method using classic signature
%% (fallback)"]
%%     click node6 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:129:129"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="110">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="110:5:5" line-data="    public Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken>, we first try the superclass's method resolution, then check for a ServletActionContext-specific method, and finally fall back to classic signature lookup. We call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken> next if the superclass strategy succeeds.

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

After returning from <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken>, we check if the context is a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> and try to find a method that takes this as a parameter. If not found, we keep going to classic method resolution.

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

After trying the other strategies, we finally call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="129:3:3" line-data="        return resolveClassicMethod(context, methodName);">`resolveClassicMethod`</SwmToken> to look up the method with the classic signature. This ensures we cover legacy action implementations.

```java
        // Lastly, try the classical argument listing
        return resolveClassicMethod(context, methodName);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="105">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="105:7:7" line-data="    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveClassicMethod`</SwmToken> just looks up the method with the classic signature in the action class. If found, we return it; otherwise, we throw. We call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken> next if we need to propagate the result or handle errors.

```java
    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        Class actionClass = context.getAction().getClass();
        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);
    }
```

---

</SwmSnippet>

## Handling Missing Action Methods

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" line="148">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="139:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, if the method isn't found, we fetch an error message from <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken>, log it, and throw a <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="154:5:5" line-data="            throw new ServletException(message, e);">`ServletException`</SwmToken>. We call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> next to get the localized error string.

```java
        } catch (NoSuchMethodException e) {
            String message =
                messages.getMessage("event.parameter", mapping.getPath());

            LOG.error(message + " " + mapping.getParameter());

            throw new ServletException(message, e);
        }

```

---

</SwmSnippet>

## Fetching Localized Error Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> here just delegates to the overloaded version with a null locale, so we use the default. We call the next overload to handle argument substitution.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Formatting Localized Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Request message for key and locale"] --> node2{"Is locale provided?"}
  click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
  node2 -->|"No"| node3["Use default locale"]
  click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node2 -->|"Yes"| node4["Use provided locale"]
  click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node3 --> node5["Find message template for key and locale"]
  node4 --> node5
  click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:293:300"
  node5 --> node6{"Is message template found?"}
  click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
  node6 -->|"Yes"| node7["Format message with arguments"]
  click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
  node6 -->|"No"| node8{"returnNull?"}
  click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
  node8 -->|"Yes"| node9["Return null"]
  click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
  node8 -->|"No"| node10["Return placeholder with key and locale"]
  click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
  node7 --> node11["Return formatted message"]
  click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Request message for key and locale"] --> node2{"Is locale provided?"}
%%   click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%   node2 -->|"No"| node3["Use default locale"]
%%   click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node2 -->|"Yes"| node4["Use provided locale"]
%%   click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node3 --> node5["Find message template for key and locale"]
%%   node4 --> node5
%%   click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:293:300"
%%   node5 --> node6{"Is message template found?"}
%%   click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%   node6 -->|"Yes"| node7["Format message with arguments"]
%%   click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%   node6 -->|"No"| node8{"<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="302:3:3" line-data="                    return returnNull ? null : (&quot;???&quot; + formatKey + &quot;???&quot;);">`returnNull`</SwmToken>?"}
%%   click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%   node8 -->|"Yes"| node9["Return null"]
%%   click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%   node8 -->|"No"| node10["Return placeholder with key and locale"]
%%   click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%   node7 --> node11["Return formatted message"]
%%   click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> here wraps the argument in an array and calls the next overload, which handles formatting with <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken>. We call the next overload to actually format the message.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> here handles message formatting and caching. If the message isn't found, it returns a fallback string. Otherwise, it formats the message with the arguments and returns it.

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

## Dispatching to the Action Method

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" line="157">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="139:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, after getting the error message, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="157:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name, method);">`dispatchMethod`</SwmToken> to actually invoke the action method using reflection. We call <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="84:5:5" line-data=" *       protected ActionDispatcher dispatcher = new EventActionDispatcher(this);">`ActionDispatcher`</SwmToken> next to handle the invocation and return the result.

```java
        return dispatchMethod(mapping, form, request, response, name, method);
    }
```

---

</SwmSnippet>

# Reflective Invocation of Action Methods

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Dispatch to action method (method, name)"] --> node2{"Did method throw an exception?"}
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:364:367"
    node2 -->|"No"| node3["Return navigation outcome (forward)"]
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:367:398"
    click node3 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:398:399"
    node2 -->|"Yes: Business exception"| node4["Propagate business exception"]
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:385:386"
    node2 -->|"Yes: Technical exception"| node5["Log error and throw technical exception"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:388:393"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Dispatch to action method (method, name)"] --> node2{"Did method throw an exception?"}
%%     click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:364:367"
%%     node2 -->|"No"| node3["Return navigation outcome (forward)"]
%%     click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:367:398"
%%     click node3 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:398:399"
%%     node2 -->|"Yes: Business exception"| node4["Propagate business exception"]
%%     click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:385:386"
%%     node2 -->|"Yes: Technical exception"| node5["Log error and throw technical exception"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:388:393"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="358">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="358:5:5" line-data="    protected ActionForward dispatchMethod(ActionMapping mapping,">`dispatchMethod`</SwmToken>, we invoke the action method using reflection, passing the four standard parameters. We call <SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="39:4:4" line-data="public class CommandLinkTag extends AbstractFacesTag {">`CommandLinkTag`</SwmToken> next if the action method is a JSF command link, which needs to handle its own invocation logic.

```java
    protected ActionForward dispatchMethod(ActionMapping mapping,
        ActionForm form, HttpServletRequest request,
        HttpServletResponse response, String name, Method method)
        throws Exception {
        ActionForward forward = null;

        try {
            Object[] args = { mapping, form, request, response };

            forward = (ActionForward) method.invoke(actionInstance, args);
```

---

</SwmSnippet>

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" line="356">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="356:5:5" line-data="    public Object invoke(FacesContext context, Object params[]) {">`invoke`</SwmToken> just returns the stored outcome, ignoring the context and parameters. The output depends on the object's state, not the inputs.

```java
    public Object invoke(FacesContext context, Object params[]) {
        return (this.outcome);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="368">

---

After returning from <SwmToken path="faces/src/main/java/org/apache/struts/faces/taglib/CommandLinkTag.java" pos="39:4:4" line-data="public class CommandLinkTag extends AbstractFacesTag {">`CommandLinkTag`</SwmToken>, we handle errors from the method invocation, log them, fetch error messages from <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken>, and rethrow exceptions as needed. The <SwmToken path="extras/src/main/java/org/apache/struts/actions/EventActionDispatcher.java" pos="84:5:5" line-data=" *       protected ActionDispatcher dispatcher = new EventActionDispatcher(this);">`ActionDispatcher`</SwmToken> wraps up the flow and returns the <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="397:9:9" line-data="        // Return the returned ActionForward instance">`ActionForward`</SwmToken>.

```java
        } catch (ClassCastException e) {
            String message =
                messages.getMessage("dispatch.return", mapping.getPath(), name);

            log.error(message, e);
            throw e;
        } catch (IllegalAccessException e) {
            String message =
                messages.getMessage("dispatch.error", mapping.getPath(), name);

            log.error(message, e);
            throw e;
        } catch (InvocationTargetException e) {
            // Rethrow the target exception if possible so that the
            // exception handling machinery can deal with it
            Throwable t = e.getTargetException();

            if (t instanceof Exception) {
                throw ((Exception) t);
            } else {
                String message =
                    messages.getMessage("dispatch.error", mapping.getPath(),
                        name);

                log.error(message, e);
                throw new ServletException(t);
            }
        }

        // Return the returned ActionForward instance
        return (forward);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
