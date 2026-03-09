---
title: Processing and Dispatching User Actions
---
This document describes how user actions are processed and dispatched to the appropriate business operation. The system first checks for cancellation and handles it if present. Otherwise, it determines and invokes the requested business operation based on request parameters, returning the result to the user.

# Dispatch Entry and Cancel Handling

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Check if request was cancelled"]
  click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:201:207"
  node1 --> node2{"Was request cancelled?"}
  click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:201:207"
  node2 -->|"Yes"| node3["Cancelled Method Resolution"]
  
  node3 --> node4{"Did cancelled handler return a result?"}
  click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:204:206"
  node4 -->|"Yes"| node7["Return cancelled result"]
  click node7 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:205:206"
  node4 -->|"No"| node5["Parameter Extraction"]
  
  node2 -->|"No"| node5
  node5 --> node6{"Is method name valid?"}
  click node6 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:217:223"
  node6 -->|"Yes"| node8["Invoking the Target Method"]
  
  node6 -->|"No"| node9["Localized Error Message Retrieval"]
  
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Cancelled Method Resolution"
node3:::HeadingStyle
click node5 goToHeading "Parameter Extraction"
node5:::HeadingStyle
click node8 goToHeading "Invoking the Target Method"
node8:::HeadingStyle
click node9 goToHeading "Localized Error Message Retrieval"
node9:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Check if request was cancelled"]
%%   click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:201:207"
%%   node1 --> node2{"Was request cancelled?"}
%%   click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:201:207"
%%   node2 -->|"Yes"| node3["Cancelled Method Resolution"]
%%   
%%   node3 --> node4{"Did cancelled handler return a result?"}
%%   click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:204:206"
%%   node4 -->|"Yes"| node7["Return cancelled result"]
%%   click node7 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:205:206"
%%   node4 -->|"No"| node5["Parameter Extraction"]
%%   
%%   node2 -->|"No"| node5
%%   node5 --> node6{"Is method name valid?"}
%%   click node6 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:217:223"
%%   node6 -->|"Yes"| node8["Invoking the Target Method"]
%%   
%%   node6 -->|"No"| node9["Localized Error Message Retrieval"]
%%   
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Cancelled Method Resolution"
%% node3:::HeadingStyle
%% click node5 goToHeading "Parameter Extraction"
%% node5:::HeadingStyle
%% click node8 goToHeading "Invoking the Target Method"
%% node8:::HeadingStyle
%% click node9 goToHeading "Localized Error Message Retrieval"
%% node9:::HeadingStyle
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="197">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="197:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken>, we check if the request was cancelled and, if so, try to handle it via the <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="200:6:6" line-data="        // Process &quot;cancelled&quot;">`cancelled`</SwmToken> method. If <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="200:6:6" line-data="        // Process &quot;cancelled&quot;">`cancelled`</SwmToken> returns a non-null forward, we exit early. This avoids running the main action logic when the user cancels.

```java
    public ActionForward execute(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        // Process "cancelled"
        if (isCancelled(request)) {
            ActionForward af = cancelled(mapping, form, request, response);

            if (af != null) {
                return af;
            }
        }

```

---

</SwmSnippet>

## Cancelled Method Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Method Lookup and Caching"]
    
    node1 --> node2{"Is cancellation handler found?"}
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:291:293"
    node2 -->|"Yes"| node3["Dispatching the Cancelled Method"]
    
    node2 -->|"No"| node4["No cancellation action taken"]
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:292:293"
    node3 --> node5["Return result of cancellation handler"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:295:296"
    node4 --> node6["Return null"]
    click node6 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:292:293"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node1 goToHeading "Method Lookup and Caching"
node1:::HeadingStyle
click node3 goToHeading "Dispatching the Cancelled Method"
node3:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Method Lookup and Caching"]
%%     
%%     node1 --> node2{"Is cancellation handler found?"}
%%     click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:291:293"
%%     node2 -->|"Yes"| node3["Dispatching the Cancelled Method"]
%%     
%%     node2 -->|"No"| node4["No cancellation action taken"]
%%     click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:292:293"
%%     node3 --> node5["Return result of cancellation handler"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:295:296"
%%     node4 --> node6["Return null"]
%%     click node6 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:292:293"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node1 goToHeading "Method Lookup and Caching"
%% node1:::HeadingStyle
%% click node3 goToHeading "Dispatching the Cancelled Method"
%% node3:::HeadingStyle
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="282">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="282:5:5" line-data="    protected ActionForward cancelled(ActionMapping mapping, ActionForm form,">`cancelled`</SwmToken>, we try to find a method called 'cancelled' on the action. If it's not there, we return null. Next, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="290:5:5" line-data="            method = getMethod(name);">`getMethod`</SwmToken> to look up the actual Method object for invocation.

```java
    protected ActionForward cancelled(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        // Identify if there is an "cancelled" method to be dispatched to
        String name = "cancelled";
        Method method = null;

        try {
            method = getMethod(name);
        } catch (NoSuchMethodException e) {
            return null;
        }

```

---

</SwmSnippet>

### Method Lookup and Caching

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Receive requested method name"] --> node2{"Is method in cache?"}
  click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:410:411"
  node2 -->|"Yes"| node3["Return method from cache"]
  click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:413:415"
  click node3 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:420:420"
  node2 -->|"No"| node4["Find method by name"]
  click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:416:416"
  node4 --> node5["Add method to cache"]
  click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:417:417"
  node5 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Receive requested method name"] --> node2{"Is method in cache?"}
%%   click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:410:411"
%%   node2 -->|"Yes"| node3["Return method from cache"]
%%   click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:413:415"
%%   click node3 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:420:420"
%%   node2 -->|"No"| node4["Find method by name"]
%%   click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:416:416"
%%   node4 --> node5["Add method to cache"]
%%   click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:417:417"
%%   node5 --> node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="410">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="410:5:5" line-data="    protected Method getMethod(String name)">`getMethod`</SwmToken> checks if the Method object for the given name is already cached. If not, it uses reflection to find it and caches the result. If the lookup logic is more complex, it defers to the superclass (<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken>) for resolution.

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

### Contextual Method Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node0["Given action context and method name"]
    click node0 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:203:203"
    node0 --> node1["Create unique key for cache lookup"]
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:206:210"
    node1 --> node2{"Is method in cache?"}
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:212:214"
    node2 -->|"Yes"| node3["Return cached method"]
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:219:220"
    node2 -->|"No"| node4["Resolve method and cache it"]
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java:215:217"
    node4 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node0["Given action context and method name"]
%%     click node0 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:203:203"
%%     node0 --> node1["Create unique key for cache lookup"]
%%     click node1 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:206:210"
%%     node1 --> node2{"Is method in cache?"}
%%     click node2 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:212:214"
%%     node2 -->|"Yes"| node3["Return cached method"]
%%     click node3 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:219:220"
%%     node2 -->|"No"| node4["Resolve method and cache it"]
%%     click node4 openCode "<SwmPath>[core/…/dispatcher/AbstractDispatcher.java](core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java)</SwmPath>:215:217"
%%     node4 --> node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="203">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="203:7:7" line-data="    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`getMethod`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="43:6:6" line-data="public abstract class AbstractDispatcher implements Dispatcher, Serializable {">`AbstractDispatcher`</SwmToken> builds a cache key from the action class and method name, checks the cache, and if missing, calls <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="215:5:5" line-data="                method = resolveMethod(context, methodName);">`resolveMethod`</SwmToken> to actually find the method. This supports multiple actions with overlapping method names.

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

### Delegated Method Resolution

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" line="288">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="288:3:3" line-data="    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken> hands off the actual method lookup to the configured <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="289:3:3" line-data="        return methodResolver.resolveMethod(context, methodName);">`methodResolver`</SwmToken>, which can implement custom logic for finding the right method to call.

```java
    Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        return methodResolver.resolveMethod(context, methodName);
    }
```

---

</SwmSnippet>

### Servlet-Specific Method Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Try to resolve method using superclass
for methodName"] --> node2{"Was method found?"}
    click node1 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:112:114"
    node2 -->|"Yes"| node3["Return resolved method"]
    click node2 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:113:113"
    click node3 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:113:113"
    node2 -->|"No"| node4{"Is context a ServletActionContext and
method accepts it?"}
    click node4 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:119:126"
    node4 -->|"Yes"| node5["Return method accepting
ServletActionContext"]
    click node5 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:121:122"
    node4 -->|"No"| node6["Return method using classic resolution"]
    click node6 openCode "core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java:129:129"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Try to resolve method using superclass
%% for <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/AbstractDispatcher.java" pos="203:16:16" line-data="    protected final Method getMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`methodName`</SwmToken>"] --> node2{"Was method found?"}
%%     click node1 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:112:114"
%%     node2 -->|"Yes"| node3["Return resolved method"]
%%     click node2 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:113:113"
%%     click node3 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:113:113"
%%     node2 -->|"No"| node4{"Is context a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> and
%% method accepts it?"}
%%     click node4 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:119:126"
%%     node4 -->|"Yes"| node5["Return method accepting
%% <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken>"]
%%     click node5 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:121:122"
%%     node4 -->|"No"| node6["Return method using classic resolution"]
%%     click node6 openCode "<SwmPath>[core/…/servlet/ServletMethodResolver.java](core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java)</SwmPath>:129:129"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="110">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="110:5:5" line-data="    public Method resolveMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveMethod`</SwmToken>, we first try the superclass's method resolution. If that fails, we continue with servlet-specific checks.

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

Back in `ServletMethodResolver.resolveMethod`, after failing to resolve via the superclass, we check if the context is a <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="119:8:8" line-data="        if (context instanceof ServletActionContext) {">`ServletActionContext`</SwmToken> and try to find a method with that parameter. If not found, we keep looking.

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

Back in `ServletMethodResolver.resolveMethod`, if all else fails, we call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="129:3:3" line-data="        return resolveClassicMethod(context, methodName);">`resolveClassicMethod`</SwmToken> to look for a method with the classic signature. This is the final attempt before giving up.

```java
        // Lastly, try the classical argument listing
        return resolveClassicMethod(context, methodName);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="105">

---

<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="105:7:7" line-data="    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {">`resolveClassicMethod`</SwmToken> looks for a method with the given name and the classic Struts parameter signature (<SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="107:10:10" line-data="        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);">`CLASSIC_EXECUTE_SIGNATURE`</SwmToken>) using reflection. This is the last step in method resolution.

```java
    protected final Method resolveClassicMethod(ActionContext context, String methodName) throws NoSuchMethodException {
        Class actionClass = context.getAction().getClass();
        return actionClass.getMethod(methodName, CLASSIC_EXECUTE_SIGNATURE);
    }
```

---

</SwmSnippet>

### Dispatching the Cancelled Method

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="295">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="200:6:6" line-data="        // Process &quot;cancelled&quot;">`cancelled`</SwmToken>, after resolving the method, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="295:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name, method);">`dispatchMethod`</SwmToken> to actually invoke it with the standard action arguments and return its result.

```java
        return dispatchMethod(mapping, form, request, response, name, method);
    }
```

---

</SwmSnippet>

## Invoking the Target Method

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Invoke the business method identified by
'name'"] --> node2{"Did invocation succeed?"}
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:367:367"
    node2 -->|"Yes"| node3["Return the result (next business step)"]
    click node3 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:398:398"
    node2 -->|"No"| node4{"What kind of error occurred?"}
    node4 -->|"Type mismatch"| node5["Report type error and fail to dispatch"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:368:373"
    node4 -->|"Access denied"| node6["Report access error and fail to dispatch"]
    click node6 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:374:379"
    node4 -->|"Business exception"| node7{"Is it a business exception?"}
    click node7 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:383:386"
    node7 -->|"Yes"| node8["Propagate business exception"]
    click node8 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:386:386"
    node7 -->|"No"| node9["Report general error and fail to
dispatch"]
    click node9 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:388:393"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Invoke the business method identified by
%% 'name'"] --> node2{"Did invocation succeed?"}
%%     click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:367:367"
%%     node2 -->|"Yes"| node3["Return the result (next business step)"]
%%     click node3 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:398:398"
%%     node2 -->|"No"| node4{"What kind of error occurred?"}
%%     node4 -->|"Type mismatch"| node5["Report type error and fail to dispatch"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:368:373"
%%     node4 -->|"Access denied"| node6["Report access error and fail to dispatch"]
%%     click node6 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:374:379"
%%     node4 -->|"Business exception"| node7{"Is it a business exception?"}
%%     click node7 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:383:386"
%%     node7 -->|"Yes"| node8["Propagate business exception"]
%%     click node8 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:386:386"
%%     node7 -->|"No"| node9["Report general error and fail to
%% dispatch"]
%%     click node9 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:388:393"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="358">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="358:5:5" line-data="    protected ActionForward dispatchMethod(ActionMapping mapping,">`dispatchMethod`</SwmToken> uses reflection to call the resolved method, handling exceptions and logging errors. If something goes wrong, it fetches error messages from <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> for logging and exception details.

```java
    protected ActionForward dispatchMethod(ActionMapping mapping,
        ActionForm form, HttpServletRequest request,
        HttpServletResponse response, String name, Method method)
        throws Exception {
        ActionForward forward = null;

        try {
            Object[] args = { mapping, form, request, response };

            forward = (ActionForward) method.invoke(actionInstance, args);
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

## Localized Error Message Retrieval

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Request message for key and locale"] --> node2{"Is locale provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 -->|"Yes"| node3["Use provided locale"]
    node2 -->|"No"| node3
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node3 --> node4["Lookup message format in cache for
(locale, key)"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:295:297"
    node4 -->|"Found"| node5["Format message with arguments"]
    node4 -->|"Not found"| node6{"Does message template exist for (locale,
key)?"}
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:298:303"
    node6 -->|"Yes"| node7["Create and cache message format"]
    node6 -->|"No"| node8{"Should return null?"}
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:305:308"
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node8 -->|"Yes"| node9["Return null"]
    node8 -->|"No"| node10["Return placeholder message (???key???) "]
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node7 --> node5
    node5 --> node11["Return formatted message"]
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Request message for key and locale"] --> node2{"Is locale provided?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 -->|"Yes"| node3["Use provided locale"]
%%     node2 -->|"No"| node3
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node3 --> node4["Lookup message format in cache for
%% (locale, key)"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:295:297"
%%     node4 -->|"Found"| node5["Format message with arguments"]
%%     node4 -->|"Not found"| node6{"Does message template exist for (locale,
%% key)?"}
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:298:303"
%%     node6 -->|"Yes"| node7["Create and cache message format"]
%%     node6 -->|"No"| node8{"Should return null?"}
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:305:308"
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node8 -->|"Yes"| node9["Return null"]
%%     node8 -->|"No"| node10["Return placeholder message (???key???) "]
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node7 --> node5
%%     node5 --> node11["Return formatted message"]
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> with a Locale, key, and one argument just wraps the more general version that takes an array of arguments. This is for convenience and code reuse.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> (with Locale, key, and Object\[\]) handles message formatting and caching. It ensures thread safety, uses a default locale if needed, and returns either a formatted message or a placeholder if missing. <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> instances are cached for performance.

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

## Parameter Extraction

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="209">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="197:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken>, after handling cancellation, we extract the parameter name that indicates which action method to call next. This sets up the next step: figuring out which method to invoke.

```java
        // Identify the request parameter containing the method name
        String parameter = getParameter(mapping, form, request, response);

```

---

</SwmSnippet>

## Parameter Name Resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Get dispatch parameter from mapping"]
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:438:439"
    node1 --> node2{"Is parameter empty?"}
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:440:442"
    node2 -->|"Yes"| node4{"Is parameter null and flavor is
DEFAULT_FLAVOR?"}
    node2 -->|"No"| node4
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:444:447"
    node4 -->|"Yes"| node5["Return 'method' as default dispatch"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:446:447"
    node4 -->|"No"| node6{"Is parameter null and flavor is
MAPPING_FLAVOR or DISPATCH_FLAVOR?"}
    click node6 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:449:457"
    node6 -->|"Yes"| node7["Log error and throw business exception"]
    click node7 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:451:457"
    node6 -->|"No"| node8["Return parameter"]
    click node8 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:459:460"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Get dispatch parameter from mapping"]
%%     click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:438:439"
%%     node1 --> node2{"Is parameter empty?"}
%%     click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:440:442"
%%     node2 -->|"Yes"| node4{"Is parameter null and flavor is
%% <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="444:19:19" line-data="        if ((parameter == null) &amp;&amp; (flavor == DEFAULT_FLAVOR)) {">`DEFAULT_FLAVOR`</SwmToken>?"}
%%     node2 -->|"No"| node4
%%     click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:444:447"
%%     node4 -->|"Yes"| node5["Return 'method' as default dispatch"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:446:447"
%%     node4 -->|"No"| node6{"Is parameter null and flavor is
%% <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="450:9:9" line-data="            &amp;&amp; ((flavor == MAPPING_FLAVOR) || (flavor == DISPATCH_FLAVOR))) {">`MAPPING_FLAVOR`</SwmToken> or <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="450:19:19" line-data="            &amp;&amp; ((flavor == MAPPING_FLAVOR) || (flavor == DISPATCH_FLAVOR))) {">`DISPATCH_FLAVOR`</SwmToken>?"}
%%     click node6 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:449:457"
%%     node6 -->|"Yes"| node7["Log error and throw business exception"]
%%     click node7 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:451:457"
%%     node6 -->|"No"| node8["Return parameter"]
%%     click node8 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:459:460"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="435">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="435:5:5" line-data="    protected String getParameter(ActionMapping mapping, ActionForm form,">`getParameter`</SwmToken> figures out the name of the request parameter to use for method dispatch, based on the current flavor. If the parameter is missing, it either defaults to "method" or throws, depending on the flavor. Error messages are fetched from <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> if needed.

```java
    protected String getParameter(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        String parameter = mapping.getParameter();

        if ("".equals(parameter)) {
            parameter = null;
        }

        if ((parameter == null) && (flavor == DEFAULT_FLAVOR)) {
            // use "method" for DEFAULT_FLAVOR if no parameter was provided
            return "method";
        }

        if ((parameter == null)
            && ((flavor == MAPPING_FLAVOR) || (flavor == DISPATCH_FLAVOR))) {
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

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> (with key and one argument) just calls the more general version with a null locale. It's a convenience overload.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Method Name Extraction

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="212">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="197:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken>, after getting the parameter name, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="214:1:1" line-data="            getMethodName(mapping, form, request, response, parameter);">`getMethodName`</SwmToken> to extract the actual method name to invoke. This step can be customized by subclasses.

```java
        // Get the method's name. This could be overridden in subclasses.
        String name =
            getMethodName(mapping, form, request, response, parameter);

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="474">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="474:5:5" line-data="    protected String getMethodName(ActionMapping mapping, ActionForm form,">`getMethodName`</SwmToken> returns the method name to dispatch to, either directly from the parameter (for <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="478:8:8" line-data="        if (flavor == MAPPING_FLAVOR) {">`MAPPING_FLAVOR`</SwmToken>) or by looking it up in the request. This lets us support different dispatching strategies.

```java
    protected String getMethodName(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response,
        String parameter) throws Exception {
        // "Mapping" flavor, defaults to "method"
        if (flavor == MAPPING_FLAVOR) {
            return parameter;
        }

        // default behaviour
        return request.getParameter(parameter);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="216">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="217:5:5" line-data="        if (&quot;execute&quot;.equals(name) || &quot;perform&quot;.equals(name)) {">`execute`</SwmToken>, after getting the method name, we check for recursion by blocking 'execute' and 'perform'. If detected, we log an error and throw, using <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="33:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> to format the error message.

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

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="225">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="197:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken>, after all the setup and checks, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="226:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name);">`dispatchMethod`</SwmToken> to actually invoke the resolved action method and return its <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="197:3:3" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionForward`</SwmToken>.

```java
        // Invoke the named method, and return the result
        return dispatchMethod(mapping, form, request, response, name);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
