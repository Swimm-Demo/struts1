---
title: Routing Requests to Action Handlers
---
This document describes how HTTP requests are routed to the correct action handler based on an optional method name. If a method is specified, the corresponding handler is invoked. If no method is provided, a default handler is used. The flow receives an HTTP request as input and ensures the appropriate action logic is executed as output.

# Choosing the Method to Dispatch

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="313">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="313:5:5" line-data="    protected ActionForward dispatchMethod(ActionMapping mapping,">`dispatchMethod`</SwmToken>, we check if the method name is null (e.g., missing or tampered in the request). If so, we delegate to <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="320:5:5" line-data="            return this.unspecified(mapping, form, request, response);">`unspecified`</SwmToken> to handle the case where no method was provided

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

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["No specific action provided"] --> node2["Check for method named 'unspecified'"]
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:245:247"
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:248:253"
    node2 --> node3{"Does 'unspecified' method exist?"}
    click node3 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:253:254"
    node3 -->|"Yes"| node4["Dispatch to 'unspecified' method"]
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:264:265"
    node3 -->|"No"| node5["Log error and throw exception"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java:255:262"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["No specific action provided"] --> node2["Check for method named 'unspecified'"]
%%     click node1 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:245:247"
%%     click node2 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:248:253"
%%     node2 --> node3{"Does 'unspecified' method exist?"}
%%     click node3 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:253:254"
%%     node3 -->|"Yes"| node4["Dispatch to 'unspecified' method"]
%%     click node4 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:264:265"
%%     node3 -->|"No"| node5["Log error and throw exception"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/ActionDispatcher.java](extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java)</SwmPath>:255:262"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="245">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="245:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, we try to find a method called 'unspecified' to use as a default handler when no method name was given, so the action can still respond in a predictable way.

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

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="254">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="245:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, if the 'unspecified' method isn't found, we log an error with details from the mapping and throw a <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="261:5:5" line-data="            throw new ServletException(message, e);">`ServletException`</SwmToken>, making it clear that the fallback handler is missing.

```java
        } catch (NoSuchMethodException e) {
            String message =
                messages.getMessage("dispatch.parameter", mapping.getPath(),
                    mapping.getParameter());

            log.error(message);

            throw new ServletException(message, e);
        }

```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="264">

---

Finally, in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="245:5:5" line-data="    protected ActionForward unspecified(ActionMapping mapping, ActionForm form,">`unspecified`</SwmToken>, we call <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="264:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name, method);">`dispatchMethod`</SwmToken> again with the 'unspecified' method and its Method object, so the dispatch logic is reused and the response is handled the same way as any other method.

```java
        return dispatchMethod(mapping, form, request, response, name, method);
    }
```

---

</SwmSnippet>

## Resolving and Invoking the Target Method

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="323">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="264:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name, method);">`dispatchMethod`</SwmToken>, after handling unspecified cases, we use reflection to get the Method object for the requested name, so we can invoke the correct handler dynamically.

```java
        // Identify the method object to be dispatched to
        Method method = null;

        try {
            method = getMethod(name);
```

---

</SwmSnippet>

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" line="328">

---

Finally, in <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="341:3:3" line-data="        return dispatchMethod(mapping, form, request, response, name, method);">`dispatchMethod`</SwmToken>, if the method isn't found, we log the error, create a user-facing message, and throw a <SwmToken path="extras/src/main/java/org/apache/struts/actions/ActionDispatcher.java" pos="328:6:6" line-data="        } catch (NoSuchMethodException e) {">`NoSuchMethodException`</SwmToken> to stop the flow and make the problem clear. Otherwise, we proceed to invoke the resolved method.

```java
        } catch (NoSuchMethodException e) {
            String message =
                messages.getMessage("dispatch.method", mapping.getPath(), name);

            log.error(message, e);

            String userMsg =
                messages.getMessage("dispatch.method.user", mapping.getPath());
            NoSuchMethodException e2 = new NoSuchMethodException(userMsg);
            e2.initCause(e);
            throw e2;
        }

        return dispatchMethod(mapping, form, request, response, name, method);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
