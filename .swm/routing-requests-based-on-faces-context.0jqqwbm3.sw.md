---
title: Routing Requests Based on Faces Context
---
This document describes how web requests are routed based on whether they originate from a standard web interaction or a JavaServer Faces (JSF) context. Actions from JSF requests are linked to their forms, while standard requests are processed as usual. If a JSF request cannot be matched to a form, no action path is returned.

```mermaid
flowchart TD
  node1["Routing Requests Based on Faces
Context
Receive web request
(Routing Requests Based on Faces Context)"]:::HeadingStyle
  click node1 goToHeading "Routing Requests Based on Faces Context"
  node1 --> node2{"Is this a JSF (Faces) request?
(Routing Requests Based on Faces Context)"}:::HeadingStyle
  click node2 goToHeading "Routing Requests Based on Faces Context"
  node2 -->|"No"| node3["Routing Requests Based on Faces
Context
Request routed to appropriate
handler
(Routing Requests Based on Faces Context)"]:::HeadingStyle
  click node3 goToHeading "Routing Requests Based on Faces Context"
  node2 -->|"Yes"| node4{"Form found?
(Routing Requests Based on Faces Context)"}:::HeadingStyle
  click node4 goToHeading "Routing Requests Based on Faces Context"
  node4 -->|"Yes"| node3
  node4 -->|"No"| node5["Routing Requests Based on Faces
Context
No handler found
(Routing Requests Based on Faces Context)"]:::HeadingStyle
  click node5 goToHeading "Routing Requests Based on Faces Context"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Routing Requests Based on Faces Context

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Receive web request"] --> node2{"Is this a Faces request?"}
  click node1 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:321:325"
  node2 -->|"No"| node3["Process as standard request"]
  click node2 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:330:335"
  node3 --> node10["Return result from standard processing"]
  click node3 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:334:335"
  click node10 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:334:335"
  node2 -->|"Yes"| node4["Get command component"]
  click node4 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:338:338"

  subgraph loop1["Traverse parent components to find form"]
    node4 --> node5{"Is component a FormComponent?"}
    click node5 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:343:349"
    node5 -->|"Yes"| node9["Form found, continue processing"]
    click node9 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:349:349"
    node5 -->|"No"| node6{"Is component null?"}
    click node6 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:345:347"
    node6 -->|"No"| node7["Move to parent component"]
    click node7 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:344:344"
    node7 --> node5
    node6 -->|"Yes"| node8["No form found, return null"]
    click node8 openCode "faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java:347:348"
  end

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Receive web request"] --> node2{"Is this a Faces request?"}
%%   click node1 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:321:325"
%%   node2 -->|"No"| node3["Process as standard request"]
%%   click node2 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:330:335"
%%   node3 --> node10["Return result from standard processing"]
%%   click node3 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:334:335"
%%   click node10 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:334:335"
%%   node2 -->|"Yes"| node4["Get command component"]
%%   click node4 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:338:338"
%% 
%%   subgraph loop1["Traverse parent components to find form"]
%%     node4 --> node5{"Is component a <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="343:10:10" line-data="        while (!(component instanceof FormComponent)) {">`FormComponent`</SwmToken>?"}
%%     click node5 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:343:349"
%%     node5 -->|"Yes"| node9["Form found, continue processing"]
%%     click node9 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:349:349"
%%     node5 -->|"No"| node6{"Is component null?"}
%%     click node6 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:345:347"
%%     node6 -->|"No"| node7["Move to parent component"]
%%     click node7 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:344:344"
%%     node7 --> node5
%%     node6 -->|"Yes"| node8["No form found, return null"]
%%     click node8 openCode "<SwmPath>[faces/…/application/FacesRequestProcessor.java](faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java)</SwmPath>:347:348"
%%   end
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" line="321">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="321:5:5" line-data="    protected String processPath(HttpServletRequest request,">`processPath`</SwmToken> decides if the request is coming from a JSF (Faces) context by checking for an <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="326:1:1" line-data="        ActionEvent event = (ActionEvent)">`ActionEvent`</SwmToken> in the request. If it's not a Faces request, it just calls the superclass method. If it is, it climbs up the <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="338:1:1" line-data="        UIComponent component = event.getComponent();">`UIComponent`</SwmToken> tree from the event's component to find the nearest <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="343:10:10" line-data="        while (!(component instanceof FormComponent)) {">`FormComponent`</SwmToken>, since that's where the action path lives. If it can't find a <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="343:10:10" line-data="        while (!(component instanceof FormComponent)) {">`FormComponent`</SwmToken>, it logs a warning and bails out. This is how it separates Faces and <SwmToken path="faces/src/main/java/org/apache/struts/faces/application/FacesRequestProcessor.java" pos="329:5:7" line-data="        // Handle non-Faces requests in the usual way">`non-Faces`</SwmToken> handling and ensures actions are tied to forms.

```java
    protected String processPath(HttpServletRequest request,
                                 HttpServletResponse response)
        throws IOException {

        // Are we processing a Faces request?
        ActionEvent event = (ActionEvent)
            request.getAttribute(Constants.ACTION_EVENT_KEY);

        // Handle non-Faces requests in the usual way
        if (event == null) {
            if (log.isTraceEnabled()) {
                log.trace("Performing standard processPath() processing");
            }
            return (super.processPath(request, response));
        }

        // Calculate the path from the form name
        UIComponent component = event.getComponent();
        if (log.isTraceEnabled()) {
            log.trace("Locating form parent for command component " +
                      event.getComponent());
        }
        while (!(component instanceof FormComponent)) {
            component = component.getParent();
            if (component == null) {
                log.warn("Command component was not nested in a Struts form!");
                return (null);
            }
        }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
