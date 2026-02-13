---
title: Redirect Summary Generation
---
This document describes how a readable summary string is created to represent a redirect, including its base path, parameters, and anchor. The process involves resolving the redirect's path, composing the full URL with any parameters and anchor, and assembling these details into a single summary string for display or logging.

```mermaid
flowchart TD
  node1["Building the Redirect Description"]:::HeadingStyle
  click node1 goToHeading "Building the Redirect Description"
  node1 --> node2["Resolving the Base Path"]:::HeadingStyle
  click node2 goToHeading "Resolving the Base Path"
  node2 --> node3["Composing the Full Redirect Path"]:::HeadingStyle
  click node3 goToHeading "Composing the Full Redirect Path"
  node3 --> node4["Building the Query String"]:::HeadingStyle
  click node4 goToHeading "Building the Query String"
  node4 --> node5["Finalizing the Redirect URL"]:::HeadingStyle
  click node5 goToHeading "Finalizing the Redirect URL"
  node5 --> node6["Completing the Redirect Summary"]:::HeadingStyle
  click node6 goToHeading "Completing the Redirect Summary"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Building the Redirect Description

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="340">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="340:5:5" line-data="    public String toString() {">`toString`</SwmToken>, we're starting to build a string that summarizes the redirect. We call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="344:13:13" line-data="        result.append(&quot;originalPath=&quot;).append(getOriginalPath()).append(&quot;;&quot;);">`getOriginalPath`</SwmToken> right away to grab the base path, since that's the main thing we want to show first in the output.

```java
    public String toString() {
        StringBuffer result = new StringBuffer(DEFAULT_BUFFER_SIZE);

        result.append("ActionRedirect [");
        result.append("originalPath=").append(getOriginalPath()).append(";");
```

---

</SwmSnippet>

## Resolving the Base Path

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="220">

---

<SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="220:5:5" line-data="    public String getOriginalPath() {">`getOriginalPath`</SwmToken> just delegates to the parent class's <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>, so we get the unmodified path. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken> to possibly add parameters or anchors to this base path.

```java
    public String getOriginalPath() {
        return super.getPath();
    }
```

---

</SwmSnippet>

## Composing the Full Redirect Path

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="230">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="230:5:5" line-data="    public String getPath() {">`getPath`</SwmToken>, we grab the original path first so we can build on top of it. We need this as the base before adding parameters or anchors.

```java
    public String getPath() {
        // get the original path and the parameter string that was formed
        String originalPath = getOriginalPath();
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="233">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>, after getting the original path, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken> to see if there are any parameters to add to the URL.

```java
        String parameterString = getParameterString();
```

---

</SwmSnippet>

### Building the Query String

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start building query string for redirect"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:295:296"
    subgraph loop1["For each parameter"]
        node1 --> node2{"Does parameter have single or multiple values?"}
        click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:301:308"
        node2 -->|"Single value"| node3["Add parameter and value to string"]
        click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:310:311"
        node2 -->|"Multiple values"| node4["For each value"]
        subgraph loop2["For each value"]
            node4 --> node5["Add parameter and value to string"]
            click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:316:317"
            node5 --> node6{"Is this the last value?"}
            click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:318:320"
            node6 -->|"No"| node7["Add '&' separator"]
            click node7 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:319:319"
            node7 --> node4
            node6 -->|"Yes"| node8["Continue to next parameter"]
        end
        node3 --> node9{"Is this the last parameter?"}
        node8 --> node9
        click node9 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:324:326"
        node9 -->|"No"| node10["Add '&' separator"]
        click node10 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:325:325"
        node10 --> node2
        node9 -->|"Yes"| node11["Finish"]
        click node11 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:327:329"
    end
    node11 --> node12["Return query string for redirect"]
    click node12 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:329:330"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start building query string for redirect"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:295:296"
%%     subgraph loop1["For each parameter"]
%%         node1 --> node2{"Does parameter have single or multiple values?"}
%%         click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:301:308"
%%         node2 -->|"Single value"| node3["Add parameter and value to string"]
%%         click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:310:311"
%%         node2 -->|"Multiple values"| node4["For each value"]
%%         subgraph loop2["For each value"]
%%             node4 --> node5["Add parameter and value to string"]
%%             click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:316:317"
%%             node5 --> node6{"Is this the last value?"}
%%             click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:318:320"
%%             node6 -->|"No"| node7["Add '&' separator"]
%%             click node7 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:319:319"
%%             node7 --> node4
%%             node6 -->|"Yes"| node8["Continue to next parameter"]
%%         end
%%         node3 --> node9{"Is this the last parameter?"}
%%         node8 --> node9
%%         click node9 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:324:326"
%%         node9 -->|"No"| node10["Add '&' separator"]
%%         click node10 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:325:325"
%%         node10 --> node2
%%         node9 -->|"Yes"| node11["Finish"]
%%         click node11 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:327:329"
%%     end
%%     node11 --> node12["Return query string for redirect"]
%%     click node12 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:329:330"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="295">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="295:5:5" line-data="    public String getParameterString() {">`getParameterString`</SwmToken>, we loop through all parameters, handling both single and multiple values, and build a query string. The function expects <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="299:7:7" line-data="        Iterator iterator = parameterValues.keySet().iterator();">`parameterValues`</SwmToken> to be a map with String keys and values that are either a String or String\[\]. <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="296:11:11" line-data="        StringBuffer strParam = new StringBuffer(DEFAULT_BUFFER_SIZE);">`DEFAULT_BUFFER_SIZE`</SwmToken> is just for efficiency.

```java
    public String getParameterString() {
        StringBuffer strParam = new StringBuffer(DEFAULT_BUFFER_SIZE);

        // loop through all parameters
        Iterator iterator = parameterValues.keySet().iterator();

        while (iterator.hasNext()) {
            // get the parameter name
            String paramName = (String) iterator.next();

            // get the value for this parameter
            Object value = parameterValues.get(paramName);

            if (value instanceof String) {
                // just one value for this param
                strParam.append(paramName).append("=").append(value);
            } else if (value instanceof String[]) {
                // loop through all values for this param
                String[] values = (String[]) value;

                for (int i = 0; i < values.length; i++) {
                    strParam.append(paramName).append("=").append(values[i]);

                    if (i < (values.length - 1)) {
                        strParam.append("&");
                    }
                }
            }

            if (iterator.hasNext()) {
                strParam.append("&");
            }
        }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="329">

---

After building the query string in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken>, we return to <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="329:5:5" line-data="        return strParam.toString();">`toString`</SwmToken> to include this info in the redirect summary.

```java
        return strParam.toString();
    }
```

---

</SwmSnippet>

### Finalizing the Redirect URL

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start with base path"] --> node2{"Are there parameters to add?"}
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:236:237"
    node2 -->|"No"| node3["Append anchor fragment"]
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:238:238"
    node2 -->|"Yes"| node4{"Does base path already contain '?'"}
    click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:246:248"
    node4 -->|"No"| node5["Append '?' and parameters"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:259:262"
    node4 -->|"Yes"| node6{"Does '?' end the path?"}
    click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:250:251"
    node6 -->|"Yes"| node7["Append parameters"]
    click node7 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:262:262"
    node6 -->|"No"| node8["Append '&' and parameters"]
    click node8 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:254:262"
    node5 --> node3
    node7 --> node3
    node8 --> node3
    node3 --> node9["Return final path"]
    click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:266:266"
    click node9 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:269:269"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start with base path"] --> node2{"Are there parameters to add?"}
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:236:237"
%%     node2 -->|"No"| node3["Append anchor fragment"]
%%     click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:238:238"
%%     node2 -->|"Yes"| node4{"Does base path already contain '?'"}
%%     click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:246:248"
%%     node4 -->|"No"| node5["Append '?' and parameters"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:259:262"
%%     node4 -->|"Yes"| node6{"Does '?' end the path?"}
%%     click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:250:251"
%%     node6 -->|"Yes"| node7["Append parameters"]
%%     click node7 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:262:262"
%%     node6 -->|"No"| node8["Append '&' and parameters"]
%%     click node8 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:254:262"
%%     node5 --> node3
%%     node7 --> node3
%%     node8 --> node3
%%     node3 --> node9["Return final path"]
%%     click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:266:266"
%%     click node9 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:269:269"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="234">

---

After getting the parameter string in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>, we figure out where to add parameters and the anchor, then return the full URL. We go back to <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="269:5:5" line-data="        return result.toString();">`toString`</SwmToken> to show this final URL in the summary.

```java
        String anchorString = getAnchorString();

        StringBuffer result = new StringBuffer(originalPath);

        if ((parameterString != null) && (parameterString.length() > 0)) {
            // the parameter separator we're going to use
            String paramSeparator = "?";

            // true if we need to use a parameter separator after originalPath
            boolean needsParamSeparator = true;

            // does the original path already have a "?"?
            int paramStartIndex = originalPath.indexOf("?");

            if (paramStartIndex > 0) {
                // did the path end with "?"?
                needsParamSeparator = (paramStartIndex != (originalPath.length()
                    - 1));

                if (needsParamSeparator) {
                    paramSeparator = "&";
                }
            }

            if (needsParamSeparator) {
                result.append(paramSeparator);
            }

            result.append(parameterString);
        }

        // append anchor string (or blank if none was set)
        result.append(anchorString);


        return result.toString();
    }
```

---

</SwmSnippet>

## Completing the Redirect Summary

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="345">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="269:5:5" line-data="        return result.toString();">`toString`</SwmToken>, after showing the original path, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="345:13:13" line-data="        result.append(&quot;parameterString=&quot;).append(getParameterString()).append(&quot;]&quot;);">`getParameterString`</SwmToken> to include the current parameters in the summary output.

```java
        result.append("parameterString=").append(getParameterString()).append("]");
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="346">

---

After adding the parameter string in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="348:5:5" line-data="        return result.toString();">`toString`</SwmToken>, we append the anchor string and finish building the summary, so the output shows everything about the redirect.

```java
        result.append("anchorString=").append(getAnchorString()).append("]");

        return result.toString();
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
