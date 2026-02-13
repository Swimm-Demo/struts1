---
title: Building link URLs for dynamic lists
---
This document explains how link URLs are built for dynamic lists and repeated elements in web pages. Parameters are gathered from tag configuration and the inner body, and an index parameter is included when the tag is used within a loop. The generated URL ensures each link points to the correct item.

```mermaid
flowchart TD
  node1["Building the Link URL with Indexed Parameters
Gather parameters from tag configuration and inner body
(Building the Link URL with Indexed Parameters)"]:::HeadingStyle
  click node1 goToHeading "Building the Link URL with Indexed Parameters"
  node1 --> node2{"Is the tag used within a loop?
(Building the Link URL with Indexed Parameters)"}:::HeadingStyle
  click node2 goToHeading "Building the Link URL with Indexed Parameters"
  node2 -->|"Yes"| node3["Include index parameter
(Building the Link URL with Indexed Parameters)"]:::HeadingStyle
  click node3 goToHeading "Building the Link URL with Indexed Parameters"
  node2 -->|"No"| node4["Generate URL
(Building the Link URL with Indexed Parameters)"]:::HeadingStyle
  click node4 goToHeading "Building the Link URL with Indexed Parameters"
  node3 --> node4
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Where is this flow used?

This flow is used multiple times in the codebase as represented in the following diagram:

```mermaid
graph TD;
      5737ee37608991f9633aec7cbe440dfb95f02d688937b36d8b71835b6c81f03f(taglib/…/html/FrameTag.java::FrameTag.doEndTag) --> 1c70c59d1cb78f506a647f913937b0a28ea8a0ab763cd77d348a7b987e8dbbc1(taglib/…/html/LinkTag.java::LinkTag.calculateURL)

0072d0bda5a83199fb108597a9cc7e79ab04d586b0862cee6b4b66640d2665d0(taglib/…/html/LinkTag.java::LinkTag.doEndTag) --> 1c70c59d1cb78f506a647f913937b0a28ea8a0ab763cd77d348a7b987e8dbbc1(taglib/…/html/LinkTag.java::LinkTag.calculateURL)


classDef mainFlowStyle color:#000000,fill:#7CB9F4
classDef rootsStyle color:#000000,fill:#00FFF4
classDef Style1 color:#000000,fill:#00FFAA
classDef Style2 color:#000000,fill:#FFFF00
classDef Style3 color:#000000,fill:#AA7CB9

%% Swimm:
%% graph TD;
%%       5737ee37608991f9633aec7cbe440dfb95f02d688937b36d8b71835b6c81f03f(<SwmPath>[taglib/…/html/FrameTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FrameTag.java)</SwmPath>::FrameTag.doEndTag) --> 1c70c59d1cb78f506a647f913937b0a28ea8a0ab763cd77d348a7b987e8dbbc1(<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>::LinkTag.calculateURL)
%% 
%% 0072d0bda5a83199fb108597a9cc7e79ab04d586b0862cee6b4b66640d2665d0(<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>::LinkTag.doEndTag) --> 1c70c59d1cb78f506a647f913937b0a28ea8a0ab763cd77d348a7b987e8dbbc1(<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>::LinkTag.calculateURL)
%% 
%% 
%% classDef mainFlowStyle color:#000000,fill:#7CB9F4
%% classDef rootsStyle color:#000000,fill:#00FFF4
%% classDef Style1 color:#000000,fill:#00FFAA
%% classDef Style2 color:#000000,fill:#FFFF00
%% classDef Style3 color:#000000,fill:#AA7CB9
```

# Building the Link URL with Indexed Parameters

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Collect parameters from tag configuration"] --> node2{"Are there inner body parameters?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:411:415"
    node2 -->|"Yes"| node3["Merge inner body parameters"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:418:423"
    node2 -->|"No"| node4{"Is 'indexed' true?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:418:423"
    node3 --> node4
    node4 -->|"Yes"| node5{"Custom indexId provided?"}
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:427:440"
    node4 -->|"No"| node7["Generate URL"]
    node5 -->|"Yes"| node6["Add index parameter with custom name"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:435:436"
    node5 -->|"No"| node8["Add index parameter as 'index'"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:435:436"
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:438:438"
    node6 --> node7
    node8 --> node7
    node7["Generate URL"] --> node9["Return generated URL"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:445:447"
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java:454:454"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Collect parameters from tag configuration"] --> node2{"Are there inner body parameters?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:411:415"
%%     node2 -->|"Yes"| node3["Merge inner body parameters"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:418:423"
%%     node2 -->|"No"| node4{"Is 'indexed' true?"}
%%     click node3 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:418:423"
%%     node3 --> node4
%%     node4 -->|"Yes"| node5{"Custom <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" pos="435:4:4" line-data="            if (indexId != null) {">`indexId`</SwmToken> provided?"}
%%     click node4 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:427:440"
%%     node4 -->|"No"| node7["Generate URL"]
%%     node5 -->|"Yes"| node6["Add index parameter with custom name"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:435:436"
%%     node5 -->|"No"| node8["Add index parameter as 'index'"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:435:436"
%%     click node8 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:438:438"
%%     node6 --> node7
%%     node8 --> node7
%%     node7["Generate URL"] --> node9["Return generated URL"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:445:447"
%%     click node9 openCode "<SwmPath>[taglib/…/html/LinkTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java)</SwmPath>:454:454"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" line="409">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" pos="409:5:5" line-data="    protected String calculateURL()">`calculateURL`</SwmToken>, we start by collecting parameters from tag attributes and the inner body. If 'indexed' is set, we need to figure out which item in a loop this link refers to, so we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" pos="428:7:9" line-data="            int indexValue = getIndexValue();">`getIndexValue()`</SwmToken> next to get the current loop index.

```java
    protected String calculateURL()
        throws JspException {
        // Identify the parameters we will add to the completed URL
        Map params =
            TagUtils.getInstance().computeParameters(pageContext, paramId,
                paramName, paramProperty, paramScope, name, property, scope,
                transaction);

        // Add parameters collected from the tag's inner body
        if (!this.parameters.isEmpty()) {
            if (params == null) {
                params = new HashMap();
            }
            params.putAll(this.parameters);
        }

        // if "indexed=true", add "index=x" parameter to query string
        // * @since Struts 1.1
        if (indexed) {
            int indexValue = getIndexValue();

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="935">

---

GetIndexValue() checks if the tag is inside an <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="938:1:1" line-data="        IterateTag iterateTag =">`IterateTag`</SwmToken> or JSTL loop, grabs the current index from whichever is found, and throws a localized exception if neither is present. This ensures the index parameter is only added when the tag is actually inside a loop.

```java
    protected int getIndexValue()
        throws JspException {
        // look for outer iterate tag
        IterateTag iterateTag =
            (IterateTag) findAncestorWithClass(this, IterateTag.class);

        if (iterateTag != null) {
            return iterateTag.getIndex();
        }

        // Look for JSTL loops
        Integer i = getJstlLoopIndex();

        if (i != null) {
            return i.intValue();
        }

        // this tag should be nested in an IterateTag or JSTL loop tag, if it's not, throw exception
        JspException e =
            new JspException(messages.getMessage("indexed.noEnclosingIterate"));

        TagUtils.getInstance().saveException(pageContext, e);
        throw e;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" line="430">

---

Back in LinkTag.calculateURL, after getting the index from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LinkTag.java" pos="428:7:9" line-data="            int indexValue = getIndexValue();">`getIndexValue()`</SwmToken>, we add it to the parameters map and then build the final URL with all parameters, including the index. This makes sure each link in a loop points to the right item.

```java
            //calculate index, and add as a parameter
            if (params == null) {
                params = new HashMap(); //create new HashMap if no other params
            }

            if (indexId != null) {
                params.put(indexId, Integer.toString(indexValue));
            } else {
                params.put("index", Integer.toString(indexValue));
            }
        }

        String url = null;

        try {
            url = TagUtils.getInstance().computeURLWithCharEncoding(pageContext,
                    forward, href, page, action, module, params, anchor, false,
                    useLocalEncoding);
        } catch (MalformedURLException e) {
            TagUtils.getInstance().saveException(pageContext, e);
            throw new JspException(messages.getMessage("rewrite.url",
                    e.toString()), e);
        }

        return (url);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
