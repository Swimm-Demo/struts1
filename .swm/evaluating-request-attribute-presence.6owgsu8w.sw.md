---
title: Evaluating Request Attribute Presence
---
This document describes how the system determines whether a specific request attribute is present, based on user-specified criteria. Only the first attribute set among cookie, header, bean, parameter, role, or user is checked, and the result is used to control conditional rendering in JSP pages.

# Where is this flow used?

This flow is used multiple times in the codebase as represented in the following diagram:

```mermaid
graph TD;
      d764121753f4241f575e3cdded1c26c88a62fa21ffcac7414d61ea635d42adc7(el/…/logic/ELMatchTag.java::ELMatchTag.condition) --> 618b6d2d8db222b09f8118b4c2e657b6ee4421e83e76faf46b7b2cfd6fd10196(taglib/…/logic/PresentTag.java::PresentTag.condition)

3e0b80c83c745b7354a5cb2525ac48cb58fbc1ce3a427b5f2ba9138c0746ef20(el/…/logic/ELNotMatchTag.java::ELNotMatchTag.condition) --> 618b6d2d8db222b09f8118b4c2e657b6ee4421e83e76faf46b7b2cfd6fd10196(taglib/…/logic/PresentTag.java::PresentTag.condition)


classDef mainFlowStyle color:#000000,fill:#7CB9F4
classDef rootsStyle color:#000000,fill:#00FFF4
classDef Style1 color:#000000,fill:#00FFAA
classDef Style2 color:#000000,fill:#FFFF00
classDef Style3 color:#000000,fill:#AA7CB9

%% Swimm:
%% graph TD;
%%       d764121753f4241f575e3cdded1c26c88a62fa21ffcac7414d61ea635d42adc7(<SwmPath>[el/…/logic/ELMatchTag.java](el/src/main/java/org/apache/strutsel/taglib/logic/ELMatchTag.java)</SwmPath>::ELMatchTag.condition) --> 618b6d2d8db222b09f8118b4c2e657b6ee4421e83e76faf46b7b2cfd6fd10196(<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>::PresentTag.condition)
%% 
%% 3e0b80c83c745b7354a5cb2525ac48cb58fbc1ce3a427b5f2ba9138c0746ef20(<SwmPath>[el/…/logic/ELNotMatchTag.java](el/src/main/java/org/apache/strutsel/taglib/logic/ELNotMatchTag.java)</SwmPath>::ELNotMatchTag.condition) --> 618b6d2d8db222b09f8118b4c2e657b6ee4421e83e76faf46b7b2cfd6fd10196(<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>::PresentTag.condition)
%% 
%% 
%% classDef mainFlowStyle color:#000000,fill:#7CB9F4
%% classDef rootsStyle color:#000000,fill:#00FFF4
%% classDef Style1 color:#000000,fill:#00FFAA
%% classDef Style2 color:#000000,fill:#FFFF00
%% classDef Style3 color:#000000,fill:#AA7CB9
```

# Evaluating Request Attribute Presence

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Determine what value to check for presence"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:67:106"
    node1 --> node2{"Which value type is specified?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:74:97"
    node2 -->|"Cookie"| node3["Check if cookie is present"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:74:76"
    node2 -->|"Header"| node4["Check if header is present"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:77:80"
    node2 -->|"Bean"| node5["Check if bean is present"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:81:82"
    node2 -->|"Parameter"| node6["Check if parameter is present"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:83:86"
    node2 -->|"Role"| node7["Check if user has specified role"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:87:92"
    subgraph loop1["For each role in role list"]
        node7 --> node14{"Is user in role?"}
        click node14 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:90:91"
        node14 -->|"Yes"| node15["Set present to true"]
        click node15 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:91:91"
        node14 -->|"No"| node14
    end
    node7 --> node10{"Does presence match desired?"}
    node2 -->|"User"| node8["Check if user matches specified name"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:93:96"
    node2 -->|"None"| node9["Exception"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:97:103"
    node3 --> node10{"Does presence match desired?"}
    node4 --> node10
    node5 --> node10
    node6 --> node10
    node8 --> node10
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:105:106"
    node10 -->|"Yes"| node12["Return true"]
    click node12 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:105:106"
    node10 -->|"No"| node13["Return false"]
    click node13 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:105:106"
    node9 --> node16["End"]
    click node16 openCode "taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java:97:103"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Determine what value to check for presence"]
%%     click node1 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:67:106"
%%     node1 --> node2{"Which value type is specified?"}
%%     click node2 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:74:97"
%%     node2 -->|"Cookie"| node3["Check if cookie is present"]
%%     click node3 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:74:76"
%%     node2 -->|"Header"| node4["Check if header is present"]
%%     click node4 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:77:80"
%%     node2 -->|"Bean"| node5["Check if bean is present"]
%%     click node5 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:81:82"
%%     node2 -->|"Parameter"| node6["Check if parameter is present"]
%%     click node6 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:83:86"
%%     node2 -->|"Role"| node7["Check if user has specified role"]
%%     click node7 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:87:92"
%%     subgraph loop1["For each role in role list"]
%%         node7 --> node14{"Is user in role?"}
%%         click node14 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:90:91"
%%         node14 -->|"Yes"| node15["Set present to true"]
%%         click node15 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:91:91"
%%         node14 -->|"No"| node14
%%     end
%%     node7 --> node10{"Does presence match desired?"}
%%     node2 -->|"User"| node8["Check if user matches specified name"]
%%     click node8 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:93:96"
%%     node2 -->|"None"| node9["Exception"]
%%     click node9 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:97:103"
%%     node3 --> node10{"Does presence match desired?"}
%%     node4 --> node10
%%     node5 --> node10
%%     node6 --> node10
%%     node8 --> node10
%%     click node10 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:105:106"
%%     node10 -->|"Yes"| node12["Return true"]
%%     click node12 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:105:106"
%%     node10 -->|"No"| node13["Return false"]
%%     click node13 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:105:106"
%%     node9 --> node16["End"]
%%     click node16 openCode "<SwmPath>[taglib/…/logic/PresentTag.java](taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java)</SwmPath>:97:103"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java" line="67">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java" pos="67:5:5" line-data="    protected boolean condition(boolean desired)">`condition`</SwmToken>, the function starts by checking each possible request attribute (cookie, header, name, parameter, role, user) in a fixed order, using only the first non-null one to determine presence. If multiple are set, only the first is used, which isn't obvious from the interface. The logic for each attribute is delegated to helper methods or direct checks.

```java
    protected boolean condition(boolean desired)
        throws JspException {
        // Evaluate the presence of the specified value
        boolean present = false;
        HttpServletRequest request =
            (HttpServletRequest) pageContext.getRequest();

        if (cookie != null) {
            present = this.isCookiePresent(request);
        } else if (header != null) {
            String value = request.getHeader(header);

            present = (value != null);
        } else if (name != null) {
            present = this.isBeanPresent();
        } else if (parameter != null) {
            String value = request.getParameter(parameter);

            present = (value != null);
        } else if (role != null) {
            StringTokenizer st =
                new StringTokenizer(role, ROLE_DELIMITER, false);

            while (!present && st.hasMoreTokens()) {
                present = request.isUserInRole(st.nextToken());
            }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/PresentTag.java" line="93">

---

Finally, the function either throws an exception if no field is set, or returns true if the detected presence matches the 'desired' value. This determines if the tag body should be evaluated in the JSP.

```java
        } else if (user != null) {
            Principal principal = request.getUserPrincipal();

            present = (principal != null) && user.equals(principal.getName());
        } else {
            JspException e =
                new JspException(messages.getMessage("logic.selector"));

            TagUtils.getInstance().saveException(pageContext, e);
            throw e;
        }

        return (present == desired);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
