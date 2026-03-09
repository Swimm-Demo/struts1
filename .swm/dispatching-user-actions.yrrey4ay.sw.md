---
title: Dispatching User Actions
---
This document explains how user requests are routed to the correct action method. The system checks for cancellations, determines the requested action, and invokes the appropriate business logic, returning the result or an error message.

```mermaid
flowchart TD
  node1["Handling Request Cancellation and Dispatch Entry"]:::HeadingStyle
  click node1 goToHeading "Handling Request Cancellation and Dispatch Entry"
  node1 -->|"Not cancelled"| node2["Extracting the Dispatch Parameter"]:::HeadingStyle
  click node2 goToHeading "Extracting the Dispatch Parameter"
  node2 -->|"Parameter present"| node3["Resolving the Target Method Name"]:::HeadingStyle
  click node3 goToHeading "Resolving the Target Method Name"
  node3 -->|"Valid method name"| node4["Invoking the Target Method via Reflection"]:::HeadingStyle
  click node4 goToHeading "Invoking the Target Method via Reflection"
  node2 -->|"Parameter missing"| node5["Handling Missing Method Names"]:::HeadingStyle
  click node5 goToHeading "Handling Missing Method Names"
  node3 -->|"Invalid method name"| node5

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Handling Request Cancellation and Dispatch Entry

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Was the request cancelled?"}
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:145:151"
    node1 -->|"Yes"| node2["Handle cancellation and return result"]
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:146:150"
    node1 -->|"No"| node3["Extracting the Dispatch Parameter"]
    
    node3 --> node4["Determine method name from parameter"]
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:156:158"
    node4 --> node5{"Is method name 'execute' or 'perform'?"}
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:161:167"
    node5 -->|"Yes"| node6["Formatting Error Messages for Missing Parameters"]
    
    node5 -->|"No"| node7["Dispatch to requested action"]
    click node7 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:170:171"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Extracting the Dispatch Parameter"
node3:::HeadingStyle
click node6 goToHeading "Formatting Error Messages for Missing Parameters"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Was the request cancelled?"}
%%     click node1 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:145:151"
%%     node1 -->|"Yes"| node2["Handle cancellation and return result"]
%%     click node2 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:146:150"
%%     node1 -->|"No"| node3["Extracting the Dispatch Parameter"]
%%     
%%     node3 --> node4["Determine method name from parameter"]
%%     click node4 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:156:158"
%%     node4 --> node5{"Is method name 'execute' or 'perform'?"}
%%     click node5 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:161:167"
%%     node5 -->|"Yes"| node6["Formatting Error Messages for Missing Parameters"]
%%     
%%     node5 -->|"No"| node7["Dispatch to requested action"]
%%     click node7 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:170:171"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Extracting the Dispatch Parameter"
%% node3:::HeadingStyle
%% click node6 goToHeading "Formatting Error Messages for Missing Parameters"
%% node6:::HeadingStyle
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="142">

---

In `DispatchAction.execute`, we check if the request was cancelled (using <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="145:4:4" line-data="        if (isCancelled(request)) {">`isCancelled`</SwmToken>). If so, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="146:7:7" line-data="            ActionForward af = cancelled(mapping, form, request, response);">`cancelled`</SwmToken>`(...)` to see if there's a specific <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="142:3:3" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionForward`</SwmToken> to return. If <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="146:7:7" line-data="            ActionForward af = cancelled(mapping, form, request, response);">`cancelled`</SwmToken> gives us a non-null result, we return it right away and skip the rest of the logic. This is how we handle user-initiated or system-triggered cancellations before doing any dispatch work.

```java
    public ActionForward execute(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        if (isCancelled(request)) {
            ActionForward af = cancelled(mapping, form, request, response);

            if (af != null) {
                return af;
            }
        }

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="216">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="216:5:5" line-data="    protected ActionForward cancelled(ActionMapping mapping, ActionForm form,">`cancelled`</SwmToken> is just a stub here—it always returns null and ignores all arguments. It's a hook for subclasses to override if they want to handle cancellations in a custom way. If not overridden, the flow just continues as if nothing special happened.

```java
    protected ActionForward cancelled(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        return null;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="153">

---

After coming back from <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="146:7:7" line-data="            ActionForward af = cancelled(mapping, form, request, response);">`cancelled`</SwmToken>, `DispatchAction.execute` grabs a parameter (via <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="154:7:7" line-data="        String parameter = getParameter(mapping, form, request, response);">`getParameter`</SwmToken>) that tells us which method to dispatch to. This is how the framework figures out what action to run for the current request.

```java
        // Get the parameter. This could be overridden in subclasses.
        String parameter = getParameter(mapping, form, request, response);

```

---

</SwmSnippet>

## Extracting the Dispatch Parameter

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="315">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="315:5:5" line-data="    protected String getParameter(ActionMapping mapping, ActionForm form,">`getParameter`</SwmToken> pulls the parameter name from the mapping. If it's missing, it logs an error and throws a <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="328:5:5" line-data="            throw new ServletException(message);">`ServletException`</SwmToken> with a message from <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken>. This stops the flow if dispatching can't be determined.

```java
    protected String getParameter(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {

        // Identify the request parameter containing the method name
        String parameter = mapping.getParameter();

        if (parameter == null) {
            String message =
                messages.getMessage("dispatch.handler", mapping.getPath());

            log.error(message);

            throw new ServletException(message);
        }


        return parameter;
    }
```

---

</SwmSnippet>

## Formatting Error Messages for Missing Parameters

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:15" line-data="    public String getMessage(String key, Object arg0) {">`getMessage(String key, Object arg0)`</SwmToken> just wraps the argument and calls the more general overload with a Locale and an Object array. This sets up message formatting and localization.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Preparing Message Formatting Arguments

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:7:7" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`Locale`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:3:3" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:17:17" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`Object`</SwmToken>`)` just wraps the single argument in an array and calls the main formatting method. This keeps the API flexible for messages with different numbers of arguments.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

## Formatting and Escaping Message Strings

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Receive locale, key, and arguments"] --> node2{"Is locale provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:286:312"
    node2 -->|"Yes"| node3["Use provided locale"]
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node2 -->|"No"| node4["Use default locale"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node3 --> node5{"Is message format available for key and
locale?"}
    node4 --> node5
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:295:297"
    node5 -->|"Yes"| node8["Format message with arguments"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:311"
    node5 -->|"No"| node6{"Does message template exist for key?"}
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:299:301"
    node6 -->|"Yes"| node7["Escape and create message format"]
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:305:307"
    node6 -->|"No"| node9{"Should return null? (returnNull flag)"}
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node9 -->|"Yes"| node10["Return null"]
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:302"
    node9 -->|"No"| node11["Return placeholder message"]
    click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:303:303"
    node7 --> node8
    node8 --> node12["Return formatted message"]
    click node12 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Receive locale, key, and arguments"] --> node2{"Is locale provided?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:286:312"
%%     node2 -->|"Yes"| node3["Use provided locale"]
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node2 -->|"No"| node4["Use default locale"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node3 --> node5{"Is message format available for key and
%% locale?"}
%%     node4 --> node5
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:295:297"
%%     node5 -->|"Yes"| node8["Format message with arguments"]
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:311"
%%     node5 -->|"No"| node6{"Does message template exist for key?"}
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:299:301"
%%     node6 -->|"Yes"| node7["Escape and create message format"]
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:305:307"
%%     node6 -->|"No"| node9{"Should return null? (<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="302:3:3" line-data="                    return returnNull ? null : (&quot;???&quot; + formatKey + &quot;???&quot;);">`returnNull`</SwmToken> flag)"}
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node9 -->|"Yes"| node10["Return null"]
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:302"
%%     node9 -->|"No"| node11["Return placeholder message"]
%%     click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:303:303"
%%     node7 --> node8
%%     node8 --> node12["Return formatted message"]
%%     click node12 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:7:7" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`Locale`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:3:3" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:17:17" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`Object`</SwmToken>`[])` checks the cache for a <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken>, creates and escapes one if missing, and formats the message with the given arguments. If the format string isn't found, it returns a placeholder or null.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="417">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="417:5:5" line-data="    protected String escape(String string) {">`escape`</SwmToken> checks if escaping is enabled, and if so, doubles every single quote in the string. This is to keep <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> happy and avoid formatting bugs.

```java
    protected String escape(String string) {
        if (!isEscape()) {
            return string;
        }

        if ((string == null) || (string.indexOf('\'') < 0)) {
            return string;
        }

        int n = string.length();
        StringBuffer sb = new StringBuffer(n);

        for (int i = 0; i < n; i++) {
            char ch = string.charAt(i);

            if (ch == '\'') {
                sb.append('\'');
            }

            sb.append(ch);
        }
```

---

</SwmSnippet>

## Resolving the Target Method Name

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="156">

---

After getting the parameter, `DispatchAction.execute` calls <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="158:1:1" line-data="            getMethodName(mapping, form, request, response, parameter);">`getMethodName`</SwmToken> to figure out the actual method name to invoke. This lets subclasses tweak how parameters map to method names.

```java
        // Get the method's name. This could be overridden in subclasses.
        String name =
            getMethodName(mapping, form, request, response, parameter);

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="371">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="371:5:5" line-data="    protected String getMethodName(ActionMapping mapping, ActionForm form,">`getMethodName`</SwmToken> just grabs the method name from the request using the parameter key. This is how the framework lets the client pick which method to run.

```java
    protected String getMethodName(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response,
        String parameter) throws Exception {
        // Identify the method name to be dispatched to.
        // dispatchMethod() will call unspecified() if name is null
        return request.getParameter(parameter);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="160">

---

After getting the method name, `DispatchAction.execute` checks if it's 'execute' or 'perform' to avoid recursion. If so, it logs an error and throws a <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="166:5:5" line-data="            throw new ServletException(message);">`ServletException`</SwmToken> with a message from <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken>.

```java
        // Prevent recursive calls
        if ("execute".equals(name) || "perform".equals(name)) {
            String message =
                messages.getMessage("dispatch.recursive", mapping.getPath());

            log.error(message);
            throw new ServletException(message);
        }

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="169">

---

After all the checks, `DispatchAction.execute` calls <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="170:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name);">`dispatchMethod`</SwmToken> with the resolved method name and returns whatever <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="142:3:3" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionForward`</SwmToken> it gets. This is where the actual action method gets called.

```java
        // Invoke the named method, and return the result
        return dispatchMethod(mapping, form, request, response, name);
    }
```

---

</SwmSnippet>

# Invoking the Target Method via Reflection

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is action name provided?"}
  click node1 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:244:246"
  node1 -->|"No"| node2["Handling Missing Method Names"]
  
  node1 -->|"Yes"| node3["Find method for action name"]
  click node3 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:252:252"
  node3 -->|"Found"| node4["Caching and Retrieving Method Objects"]
  
  node4 --> node5["Dispatcher-Level Method Lookup and Caching"]
  
  node5 --> node6["Resolving Methods with Context-Aware Logic"]
  
  node6 --> node7["Servlet-Specific and Classic Method Resolution"]
  
  node7 --> node8["Handling Method Lookup Failures"]
  
  node3 -->|"Not found"| node2
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Handling Missing Method Names"
node2:::HeadingStyle
click node4 goToHeading "Caching and Retrieving Method Objects"
node4:::HeadingStyle
click node5 goToHeading "Dispatcher-Level Method Lookup and Caching"
node5:::HeadingStyle
click node6 goToHeading "Resolving Methods with Context-Aware Logic"
node6:::HeadingStyle
click node7 goToHeading "Servlet-Specific and Classic Method Resolution"
node7:::HeadingStyle
click node8 goToHeading "Handling Method Lookup Failures"
node8:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is action name provided?"}
%%   click node1 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:244:246"
%%   node1 -->|"No"| node2["Handling Missing Method Names"]
%%   
%%   node1 -->|"Yes"| node3["Find method for action name"]
%%   click node3 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:252:252"
%%   node3 -->|"Found"| node4["Caching and Retrieving Method Objects"]
%%   
%%   node4 --> node5["Dispatcher-Level Method Lookup and Caching"]
%%   
%%   node5 --> node6["Resolving Methods with Context-Aware Logic"]
%%   
%%   node6 --> node7["Servlet-Specific and Classic Method Resolution"]
%%   
%%   node7 --> node8["Handling Method Lookup Failures"]
%%   
%%   node3 -->|"Not found"| node2
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Handling Missing Method Names"
%% node2:::HeadingStyle
%% click node4 goToHeading "Caching and Retrieving Method Objects"
%% node4:::HeadingStyle
%% click node5 goToHeading "Dispatcher-Level Method Lookup and Caching"
%% node5:::HeadingStyle
%% click node6 goToHeading "Resolving Methods with Context-Aware Logic"
%% node6:::HeadingStyle
%% click node7 goToHeading "Servlet-Specific and Classic Method Resolution"
%% node7:::HeadingStyle
%% click node8 goToHeading "Handling Method Lookup Failures"
%% node8:::HeadingStyle
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="238">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="238:5:5" line-data="    protected ActionForward dispatchMethod(ActionMapping mapping,">`dispatchMethod`</SwmToken>, if the method name is null, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="245:5:5" line-data="            return this.unspecified(mapping, form, request, response);">`unspecified`</SwmToken> to handle the case where no valid method was provided. This avoids trying to reflectively call a method with a null name.

```java
    protected ActionForward dispatchMethod(ActionMapping mapping,
        ActionForm form, HttpServletRequest request,
        HttpServletResponse response, String name)
        throws Exception {
        // Make sure we have a valid method name to call.
        // This may be null if the user hacks the query string.
        if (name == null) {
            return this.unspecified(mapping, form, request, response);
        }

```

---

</SwmSnippet>

## Handling Missing Method Names

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="188">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="188:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, we build an error message using the mapping path and parameter name (via <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="193:3:3" line-data="                mapping.getParameter());">`getParameter`</SwmToken>) to log and report exactly what's missing in the request.

```java
    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        String message =
            messages.getMessage("dispatch.parameter", mapping.getPath(),
                mapping.getParameter());
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="192">

---

After getting the parameter, <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="188:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken> logs the error message (which is localized via <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken>) before throwing the exception. This helps with debugging and internationalization.

```java
            messages.getMessage("dispatch.parameter", mapping.getPath(),
                mapping.getParameter());

        log.error(message);

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="197">

---

After logging the error, <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="188:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken> throws a <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="197:5:5" line-data="        throw new ServletException(message);">`ServletException`</SwmToken> with the message, which halts processing and signals an error to the framework.

```java
        throw new ServletException(message);
    }
```

---

</SwmSnippet>

## Resolving the Method Object for Dispatch

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="248">

---

After handling unspecified, <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="170:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name);">`dispatchMethod`</SwmToken> tries to get the Method object for the given name (using <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="252:5:5" line-data="            method = getMethod(name);">`getMethod`</SwmToken>). This uses a cache to speed up repeated lookups.

```java
        // Identify the method object to be dispatched to
        Method method = null;

        try {
            method = getMethod(name);
```

---

</SwmSnippet>

## Caching and Retrieving Method Objects

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Receive method name"] --> node2{"Is method in cache?"}
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:344:346"
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:347:349"
    node2 -->|"Yes"| node3["Return method from cache"]
    click node3 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:347:354"
    node2 -->|"No"| node4["Look up method by name"]
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:350:351"
    node4 --> node5["Add method to cache"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:351:352"
    node5 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Receive method name"] --> node2{"Is method in cache?"}
%%     click node1 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:344:346"
%%     click node2 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:347:349"
%%     node2 -->|"Yes"| node3["Return method from cache"]
%%     click node3 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:347:354"
%%     node2 -->|"No"| node4["Look up method by name"]
%%     click node4 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:350:351"
%%     node4 --> node5["Add method to cache"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:351:352"
%%     node5 --> node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="344">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="344:5:5" line-data="    protected Method getMethod(String name)">`getMethod`</SwmToken> checks the cache for the Method object, and if missing, uses reflection to find it and puts it in the cache. The whole thing is synchronized for thread safety.

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

## Dispatcher-Level Method Lookup and Caching

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Need method for action/method
name"]
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:203:204"
    node1 --> node5["Create unique key from action class and
method name"]
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:206:210"
    node5 --> node2{"Is method in cache?"}
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:212:214"
    node2 -->|"Yes"| node3["Return cached method"]
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:219:220"
    node2 -->|"No"| node4["Resolve method and store in cache"]
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:215:217"
    node4 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Need method for action/method
%% name"]
%%     click node1 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:203:204"
%%     node1 --> node5["Create unique key from action class and
%% method name"]
%%     click node5 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:206:210"
%%     node5 --> node2{"Is method in cache?"}
%%     click node2 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:212:214"
%%     node2 -->|"Yes"| node3["Return cached method"]
%%     click node3 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:219:220"
%%     node2 -->|"No"| node4["Resolve method and store in cache"]
%%     click node4 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:215:217"
%%     node4 --> node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="203">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="203:7:7" line-data="    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`getMethod`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken> builds a cache key from the action class and method name, checks the cache, and if missing, calls <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> to find and cache the Method object.

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

## Resolving Methods with Context-Aware Logic

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="288">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="288:3:3" line-data="    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken> just hands off to <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="289:3:3" line-data="        return methodResolver.resolveMethod(context, methodName);">`methodResolver`</SwmToken>, which can be customized to handle different method resolution strategies depending on the context.

```java
    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        return methodResolver.resolveMethod(context, methodName);
    }
```

---

</SwmSnippet>

## Servlet-Specific and Classic Method Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Try to resolve method using standard
strategy"] --> node2{"Was method found?"}
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:112:114"
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:112:114"
    node2 -->|"Yes"| node3["Return resolved method"]
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:113:113"
    node2 -->|"No"| node4{"Is context a servlet context?"}
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:119:126"
    node4 -->|"Yes"| node5{"Does method accept servlet context?"}
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:121:122"
    node5 -->|"Yes"| node6["Return method that accepts servlet
context"]
    click node6 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:122:122"
    node5 -->|"No"| node7["Try classic method resolution"]
    click node7 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:129:129"
    node4 -->|"No"| node7
    node7 --> node8["Return resolved method"]
    click node8 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:129:129"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Try to resolve method using standard
%% strategy"] --> node2{"Was method found?"}
%%     click node1 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:112:114"
%%     click node2 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:112:114"
%%     node2 -->|"Yes"| node3["Return resolved method"]
%%     click node3 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:113:113"
%%     node2 -->|"No"| node4{"Is context a servlet context?"}
%%     click node4 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:119:126"
%%     node4 -->|"Yes"| node5{"Does method accept servlet context?"}
%%     click node5 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:121:122"
%%     node5 -->|"Yes"| node6["Return method that accepts servlet
%% context"]
%%     click node6 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:122:122"
%%     node5 -->|"No"| node7["Try classic method resolution"]
%%     click node7 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:129:129"
%%     node4 -->|"No"| node7
%%     node7 --> node8["Return resolved method"]
%%     click node8 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:129:129"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="110">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="110:5:5" line-data="    public Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken>, we first try the superclass's logic to resolve the method. If that fails, we check for a ServletActionContext-specific method, and if that also fails, we fall back to classic method resolution.

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

After failing the superclass check, <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="49:4:4" line-data="public class ServletMethodResolver extends AbstractMethodResolver {">`ServletMethodResolver`</SwmToken> tries to find a method that takes a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> parameter. This supports actions that need servlet-specific context.

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

If both the superclass and <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> checks fail, <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="49:4:4" line-data="public class ServletMethodResolver extends AbstractMethodResolver {">`ServletMethodResolver`</SwmToken> falls back to <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="129:3:3" line-data="        return resolveClassicMethod(context, methodName);">`resolveClassicMethod`</SwmToken> for legacy support.

```java
        // Lastly, try the classical argument listing
        return resolveClassicMethod(context, methodName);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="105">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="105:7:7" line-data="    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveClassicMethod`</SwmToken> uses <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="107:10:10" line-data="        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);">`CLASSIC_EXECUTE_SIGNATURE`</SwmToken> to find the method by name and expected parameter types in the action class. This is how classic (legacy) action methods are resolved.

```java
    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        Class actionClass = context.getAction().getClass();
        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);
    }
```

---

</SwmSnippet>

## Handling Method Lookup Failures

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["User requests an action by name"] --> node2{"Is the requested action available?"}
  click node1 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:253:264"
  node2 -->|"Yes"| node3["Invoke the requested action method"]
  click node2 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:266:299"
  node2 -->|"No"| node4["Inform user: Action not found"]
  click node4 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:253:264"
  node3 --> node5{"Did the action succeed?"}
  click node3 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:266:299"
  node5 -->|"Yes"| node6["Return result to user (ActionForward)"]
  click node6 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:301:302"
  node5 -->|"No"| node7["Inform user: Action failed"]
  click node7 openCode "extras/src/main/java/org/apache/struts/actions/DispatchAction.java:272:299"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["User requests an action by name"] --> node2{"Is the requested action available?"}
%%   click node1 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:253:264"
%%   node2 -->|"Yes"| node3["Invoke the requested action method"]
%%   click node2 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:266:299"
%%   node2 -->|"No"| node4["Inform user: Action not found"]
%%   click node4 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:253:264"
%%   node3 --> node5{"Did the action succeed?"}
%%   click node3 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:266:299"
%%   node5 -->|"Yes"| node6["Return result to user (<SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="142:3:3" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionForward`</SwmToken>)"]
%%   click node6 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:301:302"
%%   node5 -->|"No"| node7["Inform user: Action failed"]
%%   click node7 openCode "<SwmPath>[extras/…/actions/DispatchAction.java](extras/src/main/java/org/apache/struts/actions/DispatchAction.java)</SwmPath>:272:299"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="253">

---

After logging the technical error, <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="170:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name);">`dispatchMethod`</SwmToken> gets a user-friendly message from <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken>, wraps it in a new exception, links the original cause, and throws it. This keeps logs and user messages separate.

```java
        } catch (NoSuchMethodException e) {
            String message =
                messages.getMessage("dispatch.method", mapping.getPath(), name);

            log.error(message, e);

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="259">

---

After logging the technical error, <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="170:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name);">`dispatchMethod`</SwmToken> gets a user-friendly message from <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken>, wraps it in a new exception, links the original cause, and throws it. This keeps logs and user messages separate.

```java
            String userMsg =
                messages.getMessage("dispatch.method.user", mapping.getPath());
            NoSuchMethodException e2 = new NoSuchMethodException(userMsg);
            e2.initCause(e);
            throw e2;
        }

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/DispatchAction.java" line="266">

---

Here, DispatchAction.dispatchMethod actually calls the resolved method using Java reflection, passing the four expected parameters and casting the result to <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="266:1:1" line-data="        ActionForward forward = null;">`ActionForward`</SwmToken>. If the method signature doesn't match, or if there's an access or invocation error, we catch the exception, log a formatted message (using <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken> for localization), and handle it by either rethrowing or wrapping it. This is where we rely on <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="51:6:6" line-data="public abstract class MessageResources implements Serializable {">`MessageResources`</SwmToken> again to make sure error messages are clear and localized, right after coming back from its logic. Finally, we return the <SwmToken path="extras/src/main/java/org/apache/struts/actions/DispatchAction.java" pos="266:1:1" line-data="        ActionForward forward = null;">`ActionForward`</SwmToken> from the invoked method, or propagate the error if something went wrong.

```java
        ActionForward forward = null;

        try {
            Object[] args = { mapping, form, request, response };

            forward = (ActionForward) method.invoke(this, args);
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
