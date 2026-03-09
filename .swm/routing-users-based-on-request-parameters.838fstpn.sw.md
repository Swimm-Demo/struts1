---
title: Routing Users Based on Request Parameters
---
This document describes how users are routed to the correct module and page by extracting routing parameters from HTTP requests, including multipart form submissions. The flow ensures users are only routed when valid information is provided, otherwise an error is shown.

# Extracting Routing Parameters from the Request

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/SwitchAction.java" line="83">

---

In <SwmToken path="extras/src/main/java/org/apache/struts/actions/SwitchAction.java" pos="83:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken>, we're grabbing 'page' and 'prefix' from the request to figure out where to send the user. If these aren't found, we need to check for multipart form data, so we call <SwmToken path="core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" pos="38:4:4" line-data="public class MultipartRequestWrapper extends HttpServletRequestWrapper {">`MultipartRequestWrapper`</SwmToken> next to handle cases where parameters might be embedded in a multipart request.

```java
    public ActionForward execute(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        // Identify the request parameters controlling our actions
        String page = request.getParameter("page");
        String prefix = request.getParameter("prefix");

```

---

</SwmSnippet>

## Retrieving Parameters from Multipart Requests

<SwmSnippet path="/core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" line="75">

---

In <SwmToken path="core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" pos="75:5:5" line-data="    public String getParameter(String name) {">`getParameter`</SwmToken>, we're first checking the standard request for the parameter. If it's not there, we need to call <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken> next to get the underlying request object, since multipart data might be handled differently and could require digging deeper.

```java
    public String getParameter(String name) {
        String value = getRequest().getParameter(name);

```

---

</SwmSnippet>

### Accessing the Underlying Servlet Request

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="94">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="94:5:5" line-data="    public HttpServletRequest getRequest() {">`getRequest`</SwmToken> just grabs the actual servlet request from the current web context. This is needed so parameter retrieval works even if the request is wrapped or modified elsewhere.

```java
    public HttpServletRequest getRequest() {
        return servletWebContext().getRequest();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="67">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:5:5" line-data="    protected ServletWebContext servletWebContext() {">`servletWebContext`</SwmToken> just casts the base context to <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:3:3" line-data="    protected ServletWebContext servletWebContext() {">`ServletWebContext`</SwmToken> without checking. If the assumption fails, you'll get a ClassCastException, so this is only safe if the context is always set up correctly.

```java
    protected ServletWebContext servletWebContext() {
        return (ServletWebContext) this.getBaseContext();
    }
```

---

</SwmSnippet>

### Fallback to Local Parameter Map

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is direct value present?"}
    click node1 openCode "core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java:78:79"
    node1 -->|"Yes"| node2["Return direct value"]
    click node2 openCode "core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java:86:87"
    node1 -->|"No"| node3{"Is there a list of values for the
parameter and is it not empty?"}
    click node3 openCode "core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java:79:81"
    node3 -->|"Yes"| node4["Return first value in list"]
    click node4 openCode "core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java:82:83"
    node3 -->|"No"| node5["Return no value"]
    click node5 openCode "core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java:86:87"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is direct value present?"}
%%     click node1 openCode "<SwmPath>[core/…/upload/MultipartRequestWrapper.java](core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java)</SwmPath>:78:79"
%%     node1 -->|"Yes"| node2["Return direct value"]
%%     click node2 openCode "<SwmPath>[core/…/upload/MultipartRequestWrapper.java](core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java)</SwmPath>:86:87"
%%     node1 -->|"No"| node3{"Is there a list of values for the
%% parameter and is it not empty?"}
%%     click node3 openCode "<SwmPath>[core/…/upload/MultipartRequestWrapper.java](core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java)</SwmPath>:79:81"
%%     node3 -->|"Yes"| node4["Return first value in list"]
%%     click node4 openCode "<SwmPath>[core/…/upload/MultipartRequestWrapper.java](core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java)</SwmPath>:82:83"
%%     node3 -->|"No"| node5["Return no value"]
%%     click node5 openCode "<SwmPath>[core/…/upload/MultipartRequestWrapper.java](core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java)</SwmPath>:86:87"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" line="78">

---

Back in <SwmToken path="extras/src/main/java/org/apache/struts/actions/SwitchAction.java" pos="87:9:9" line-data="        String page = request.getParameter(&quot;page&quot;);">`getParameter`</SwmToken>, after checking the servlet request (from <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken>), if the parameter isn't found, we look in the local parameters map. This covers cases where multipart form data stores values differently, so both sources are checked before returning.

```java
        if (value == null) {
            String[] mValue = (String[]) parameters.get(name);

            if ((mValue != null) && (mValue.length > 0)) {
                value = mValue[0];
            }
        }

        return value;
    }
```

---

</SwmSnippet>

## Validating and Routing the Request

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are 'page' and 'prefix' provided?"}
    click node1 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:90:95"
    node1 -->|"Yes"| node2["Switch session to module identified by
'prefix'"]
    click node2 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:97:99"
    node1 -->|"No"| node3["Show error: Missing 'page' or 'prefix'"]
    click node3 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:91:95"
    node2 --> node4{"Is module selection successful
(MODULE_KEY set)?"}
    click node4 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:101:106"
    node4 -->|"Yes"| node5["Forward user to 'page' in selected
module"]
    click node5 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:109:110"
    node4 -->|"No"| node6["Show error: Invalid module prefix"]
    click node6 openCode "extras/src/main/java/org/apache/struts/actions/SwitchAction.java:102:106"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are 'page' and 'prefix' provided?"}
%%     click node1 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:90:95"
%%     node1 -->|"Yes"| node2["Switch session to module identified by
%% 'prefix'"]
%%     click node2 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:97:99"
%%     node1 -->|"No"| node3["Show error: Missing 'page' or 'prefix'"]
%%     click node3 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:91:95"
%%     node2 --> node4{"Is module selection successful
%% (<SwmToken path="extras/src/main/java/org/apache/struts/actions/SwitchAction.java" pos="101:10:10" line-data="        if (request.getAttribute(Globals.MODULE_KEY) == null) {">`MODULE_KEY`</SwmToken> set)?"}
%%     click node4 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:101:106"
%%     node4 -->|"Yes"| node5["Forward user to 'page' in selected
%% module"]
%%     click node5 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:109:110"
%%     node4 -->|"No"| node6["Show error: Invalid module prefix"]
%%     click node6 openCode "<SwmPath>[extras/…/actions/SwitchAction.java](extras/src/main/java/org/apache/struts/actions/SwitchAction.java)</SwmPath>:102:106"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/SwitchAction.java" line="90">

---

Back in `SwitchAction.execute`, after getting parameters from <SwmToken path="core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" pos="38:4:4" line-data="public class MultipartRequestWrapper extends HttpServletRequestWrapper {">`MultipartRequestWrapper`</SwmToken>, we check if 'page' and 'prefix' are valid. If not, we throw an error. If they're good, we switch modules and forward to the requested page. This ensures only valid requests are processed.

```java
        if ((page == null) || (prefix == null)) {
            String message = messages.getMessage("switch.required");

            log.error(message);
            throw new ServletException(message);
        }

        // Switch to the requested module
        ModuleUtils.getInstance().selectModule(prefix, request,
            getServlet().getServletContext());

        if (request.getAttribute(Globals.MODULE_KEY) == null) {
            String message = messages.getMessage("switch.prefix", prefix);

            log.error(message);
            throw new ServletException(message);
        }

        // Forward control to the specified module-relative URI
        return (new ActionForward(page));
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
