---
title: Redirect URL Construction
---
This document explains how redirect <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="276:22:22" line-data="     * @return a string which can be appended to the URLs.  The">`URLs`</SwmToken> are constructed to send users to a new location, including any additional information in query parameters and anchors. The flow receives a base path, parameters, and an anchor, and produces a fully formed URL for redirection.

# Building the Redirect Path

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start with base path, parameters, and
anchor"]
  click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:230:233"
  node1 --> node2{"Are there parameters to add?"}
  
  node2 -->|"No"| node4["Build final URL: base path + anchor"]
  click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:265:270"
  node2 -->|"Yes"| node3{"Does base path already have '?'"}
  click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:246:256"
  node3 -->|"Yes"| node5["Add parameters with '&', then anchor"]
  click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:254:270"
  node3 -->|"No"| node6["Add parameters with '?', then anchor"]
  click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:259:270"
  node5 --> node4
  node6 --> node4
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Finalizing the Redirect URL"
node2:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start with base path, parameters, and
%% anchor"]
%%   click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:230:233"
%%   node1 --> node2{"Are there parameters to add?"}
%%   
%%   node2 -->|"No"| node4["Build final URL: base path + anchor"]
%%   click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:265:270"
%%   node2 -->|"Yes"| node3{"Does base path already have '?'"}
%%   click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:246:256"
%%   node3 -->|"Yes"| node5["Add parameters with '&', then anchor"]
%%   click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:254:270"
%%   node3 -->|"No"| node6["Add parameters with '?', then anchor"]
%%   click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:259:270"
%%   node5 --> node4
%%   node6 --> node4
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Finalizing the Redirect URL"
%% node2:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="230">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="230:5:5" line-data="    public String getPath() {">`getPath`</SwmToken>, we grab the base path using <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="232:7:7" line-data="        String originalPath = getOriginalPath();">`getOriginalPath`</SwmToken>. This is needed because the path might already have query parameters, and we need to build on top of whatever is returned. The function assumes <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="232:7:7" line-data="        String originalPath = getOriginalPath();">`getOriginalPath`</SwmToken> gives us a valid path string, so we don't do any format checks here. Next, we need to call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken> to see if there are any parameters to append.

```java
    public String getPath() {
        // get the original path and the parameter string that was formed
        String originalPath = getOriginalPath();
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="220">

---

<SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="220:5:5" line-data="    public String getOriginalPath() {">`getOriginalPath`</SwmToken> just returns the path from the superclass. It's a pass-through, so any logic about the path format or content is handled upstream. After this, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken> continues building the redirect URL.

```java
    public String getOriginalPath() {
        return super.getPath();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="233">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>, after getting the original path, we fetch the parameter string with <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken>. This tells us if there are any query parameters to add to the path, and how to format them.

```java
        String parameterString = getParameterString();
```

---

</SwmSnippet>

## Formatting Query Parameters

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start building query string"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:295:296"
    
    subgraph loop1["For each parameter"]
        node1 --> node2{"Does parameter have single or multiple
values?"}
        click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:301:322"
        node2 -->|"Single value"| node3["Add parameter and value to query string"]
        click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:308:311"
        node2 -->|"Multiple values"| loop2
        
        subgraph loop2["For each value of parameter"]
            node4["Add parameter and value to query string"]
            click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:315:321"
        end
    end
    loop1 --> node5["Return query string"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:329:330"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start building query string"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:295:296"
%%     
%%     subgraph loop1["For each parameter"]
%%         node1 --> node2{"Does parameter have single or multiple
%% values?"}
%%         click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:301:322"
%%         node2 -->|"Single value"| node3["Add parameter and value to query string"]
%%         click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:308:311"
%%         node2 -->|"Multiple values"| loop2
%%         
%%         subgraph loop2["For each value of parameter"]
%%             node4["Add parameter and value to query string"]
%%             click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:315:321"
%%         end
%%     end
%%     loop1 --> node5["Return query string"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:329:330"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="295">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="295:5:5" line-data="    public String getParameterString() {">`getParameterString`</SwmToken>, we loop through all parameters and build a query string. Each key-value pair is formatted as key=value, and if a key has multiple values, we repeat the key for each value. Everything is joined with '&'.

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

Finally, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken> returns the built query string. After this, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="329:5:5" line-data="        return strParam.toString();">`toString`</SwmToken> is called to get the string representation of the <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="343:6:6" line-data="        result.append(&quot;ActionRedirect [&quot;);">`ActionRedirect`</SwmToken>, which includes the parameter string for debugging or logging.

```java
        return strParam.toString();
    }
```

---

</SwmSnippet>

## Debugging Output Construction

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start building ActionRedirect summary"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:340:341"
    node1 --> node2["Include original path in summary"]
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:344:344"
    node2 --> node3["Include parameters in summary"]
    click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:345:345"
    node3 --> node4["Include anchor in summary"]
    click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:346:346"
    node4 --> node5["Return summary string"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:348:349"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start building <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="343:6:6" line-data="        result.append(&quot;ActionRedirect [&quot;);">`ActionRedirect`</SwmToken> summary"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:340:341"
%%     node1 --> node2["Include original path in summary"]
%%     click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:344:344"
%%     node2 --> node3["Include parameters in summary"]
%%     click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:345:345"
%%     node3 --> node4["Include anchor in summary"]
%%     click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:346:346"
%%     node4 --> node5["Return summary string"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:348:349"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="340">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="340:5:5" line-data="    public String toString() {">`toString`</SwmToken>, we start building a string that shows the internal state of the <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="343:6:6" line-data="        result.append(&quot;ActionRedirect [&quot;);">`ActionRedirect`</SwmToken>. We call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="344:13:13" line-data="        result.append(&quot;originalPath=&quot;).append(getOriginalPath()).append(&quot;;&quot;);">`getOriginalPath`</SwmToken> to include the base path in the output.

```java
    public String toString() {
        StringBuffer result = new StringBuffer(DEFAULT_BUFFER_SIZE);

        result.append("ActionRedirect [");
        result.append("originalPath=").append(getOriginalPath()).append(";");
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="345">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="269:5:5" line-data="        return result.toString();">`toString`</SwmToken>, after adding the original path, we append the current parameter string by calling <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="345:13:13" line-data="        result.append(&quot;parameterString=&quot;).append(getParameterString()).append(&quot;]&quot;);">`getParameterString`</SwmToken>. This gives a snapshot of all parameters at this moment.

```java
        result.append("parameterString=").append(getParameterString()).append("]");
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="346">

---

Finally, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="348:5:5" line-data="        return result.toString();">`toString`</SwmToken> adds the anchor string to the output, so the debug string shows the full redirect state: path, parameters, and anchor.

```java
        result.append("anchorString=").append(getAnchorString()).append("]");

        return result.toString();
    }
```

---

</SwmSnippet>

## Finalizing the Redirect URL

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start with original path"] --> node2{"Are there parameters to add?"}
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:236:236"
    node2 -->|"No"| node5["Append anchor string"]
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:238:238"
    node2 -->|"Yes"| node3{"Does original path contain '?'"}
    click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:246:246"
    node3 -->|"No"| node4["Append '?' and parameters"]
    click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:259:262"
    node3 -->|"Yes"| node6{"Does original path end with '?'"}
    click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:250:251"
    node6 -->|"Yes"| node7["Append parameters"]
    click node7 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:262:262"
    node6 -->|"No"| node8["Append '&' and parameters"]
    click node8 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:254:262"
    node4 --> node5
    node7 --> node5
    node8 --> node5
    node5 --> node10["Build final URL with anchor"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:266:266"
    click node10 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:269:269"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start with original path"] --> node2{"Are there parameters to add?"}
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:236:236"
%%     node2 -->|"No"| node5["Append anchor string"]
%%     click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:238:238"
%%     node2 -->|"Yes"| node3{"Does original path contain '?'"}
%%     click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:246:246"
%%     node3 -->|"No"| node4["Append '?' and parameters"]
%%     click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:259:262"
%%     node3 -->|"Yes"| node6{"Does original path end with '?'"}
%%     click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:250:251"
%%     node6 -->|"Yes"| node7["Append parameters"]
%%     click node7 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:262:262"
%%     node6 -->|"No"| node8["Append '&' and parameters"]
%%     click node8 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:254:262"
%%     node4 --> node5
%%     node7 --> node5
%%     node8 --> node5
%%     node5 --> node10["Build final URL with anchor"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:266:266"
%%     click node10 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:269:269"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="234">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>, after getting the parameter string, we figure out if we need to use '?' or '&' to append parameters, depending on whether the original path already has a query section. Then we add the parameters and anchor string to finish the URL. After this, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="269:5:5" line-data="        return result.toString();">`toString`</SwmToken> can be used to see the full redirect state for debugging.

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

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
