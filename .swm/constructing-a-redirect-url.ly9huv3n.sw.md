---
title: Constructing a Redirect URL
---
This document explains how a redirect URL is created by inheriting settings from a base configuration and adding parameters and anchor information. The resulting URL enables the system to redirect users to the appropriate destination with all required context.

```mermaid
flowchart TD
  node1["Setting up redirect configuration and enforcing naming constraints"]:::HeadingStyle
  click node1 goToHeading "Setting up redirect configuration and enforcing naming constraints"
  node1 --> node2["Building the redirect URL with path and parameters"]:::HeadingStyle
  click node2 goToHeading "Building the redirect URL with path and parameters"
  node2 --> node3{"Are there parameters to add?
(Formatting redirect parameters for the URL)"}:::HeadingStyle
  click node3 goToHeading "Formatting redirect parameters for the URL"
  node3 -->|"Yes"| node4{"Does base path already have '?'?
(Finalizing the redirect URL with parameters and anchor)"}:::HeadingStyle
  click node4 goToHeading "Finalizing the redirect URL with parameters and anchor"
  node4 -->|"Yes"| node5["Finalizing the redirect URL with
parameters and anchor
(Finalizing the redirect URL with parameters and anchor)"]:::HeadingStyle
  click node5 goToHeading "Finalizing the redirect URL with parameters and anchor"
  node4 -->|"No"| node5
  node3 -->|"No"| node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Setting up redirect configuration and enforcing naming constraints

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Create redirect from base
configuration"] --> node3["Copy navigation properties from base
configuration"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:130:132"
    node3 --> node2["Copying additional redirect configuration from base"]
    click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:130:134"
    node2 --> node4["Copying properties from base configuration"]
    
    node4 --> node5["Initializing redirect parameters"]
    
    

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Copying additional redirect configuration from base"
node2:::HeadingStyle
click node4 goToHeading "Copying properties from base configuration"
node4:::HeadingStyle
click node5 goToHeading "Initializing redirect parameters"
node5:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Create redirect from base
%% configuration"] --> node3["Copy navigation properties from base
%% configuration"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:130:132"
%%     node3 --> node2["Copying additional redirect configuration from base"]
%%     click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:130:134"
%%     node2 --> node4["Copying properties from base configuration"]
%%     
%%     node4 --> node5["Initializing redirect parameters"]
%%     
%%     
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Copying additional redirect configuration from base"
%% node2:::HeadingStyle
%% click node4 goToHeading "Copying properties from base configuration"
%% node4:::HeadingStyle
%% click node5 goToHeading "Initializing redirect parameters"
%% node5:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="130">

---

In ActionRedirect.ActionRedirect, we start by copying the name from the base configuration. This sets up the redirect object to match the original config, and we need to call FormBeanConfig.setName next to enforce any constraints on changing the name, like immutability after configuration.

```java
    public ActionRedirect(ForwardConfig baseConfig) {
        setName(baseConfig.getName());
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="152">

---

FormBeanConfig.setName enforces that the name can't be changed after configuration by calling <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="153:1:3" line-data="        throwIfConfigured();">`throwIfConfigured()`</SwmToken> first. This keeps the object's lifecycle predictable and prevents accidental changes.

```java
    public void setName(String name) {
        throwIfConfigured();
        this.name = name;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="132">

---

Back in ActionRedirect.ActionRedirect, after setting the name, we copy the path from the base config. This sets up the destination for the redirect, and we need to call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="132:5:5" line-data="        setPath(baseConfig.getPath());">`getPath`</SwmToken> next to handle any path-specific logic.

```java
        setPath(baseConfig.getPath());
```

---

</SwmSnippet>

## Building the redirect URL with path and parameters

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start: Get base path, parameters, and
anchor"]
  click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:230:233"
  node1 --> node2{"Are there parameters to add?"}
  click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:238:263"
  node2 -->|"No"| node4["Append anchor and return final URL"]
  node2 -->|"Yes"| node3{"Does base path already have '?'"}
  click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:246:256"
  node3 -->|"Yes"| node5["Append '&' and parameters"]
  click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:254:262"
  node3 -->|"No"| node6["Append '?' and parameters"]
  click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:259:262"
  node5 --> node4
  node6 --> node4
  node4["Append anchor and return final URL"]
  click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:265:270"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start: Get base path, parameters, and
%% anchor"]
%%   click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:230:233"
%%   node1 --> node2{"Are there parameters to add?"}
%%   click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:238:263"
%%   node2 -->|"No"| node4["Append anchor and return final URL"]
%%   node2 -->|"Yes"| node3{"Does base path already have '?'"}
%%   click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:246:256"
%%   node3 -->|"Yes"| node5["Append '&' and parameters"]
%%   click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:254:262"
%%   node3 -->|"No"| node6["Append '?' and parameters"]
%%   click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:259:262"
%%   node5 --> node4
%%   node6 --> node4
%%   node4["Append anchor and return final URL"]
%%   click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:265:270"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="230">

---

In ActionRedirect.getPath, we grab the original path first. This gives us the base URL before we start appending parameters or anchors, so we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="232:7:7" line-data="        String originalPath = getOriginalPath();">`getOriginalPath`</SwmToken> next.

```java
    public String getPath() {
        // get the original path and the parameter string that was formed
        String originalPath = getOriginalPath();
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="220">

---

ActionRedirect.getOriginalPath just returns the path from the superclass. This keeps the base URL intact for further processing in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="221:5:5" line-data="        return super.getPath();">`getPath`</SwmToken>.

```java
    public String getOriginalPath() {
        return super.getPath();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="233">

---

Back in ActionRedirect.getPath, after fetching the original path, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="233:7:7" line-data="        String parameterString = getParameterString();">`getParameterString`</SwmToken> to build the query string for the redirect URL.

```java
        String parameterString = getParameterString();
```

---

</SwmSnippet>

### Formatting redirect parameters for the URL

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start building parameter string"] --> node2["For each parameter in parameters"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:295:296"
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:299:327"
    subgraph loop1["For each parameter in parameters"]
      node2 --> node3{"Does parameter have one or multiple
values?"}
      click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:308:322"
      node3 -->|"One value"| node4["Add parameter and value to string"]
      click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:310:310"
      node3 -->|"Multiple values"| node5["For each value of parameter"]
      subgraph loop2["For each value of parameter"]
        node5 --> node6["Add parameter and value to string"]
        click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:316:316"
        node6 --> node7{"More values for this parameter?"}
        click node7 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:318:320"
        node7 -->|"Yes"| node8["Add '&' between values"]
        click node8 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:319:319"
        node8 --> node5
        node7 -->|"No"| node9["Done with this parameter"]
      end
      node4 --> node10{"More parameters?"}
      node9 --> node10
      click node10 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:324:326"
      node10 -->|"Yes"| node11["Add '&' between parameters"]
      click node11 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:325:325"
      node11 --> node2
      node10 -->|"No"| node12["All parameters processed"]
    end
    node12 --> node13["Return query string for redirect"]
    click node13 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:329:330"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start building parameter string"] --> node2["For each parameter in parameters"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:295:296"
%%     click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:299:327"
%%     subgraph loop1["For each parameter in parameters"]
%%       node2 --> node3{"Does parameter have one or multiple
%% values?"}
%%       click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:308:322"
%%       node3 -->|"One value"| node4["Add parameter and value to string"]
%%       click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:310:310"
%%       node3 -->|"Multiple values"| node5["For each value of parameter"]
%%       subgraph loop2["For each value of parameter"]
%%         node5 --> node6["Add parameter and value to string"]
%%         click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:316:316"
%%         node6 --> node7{"More values for this parameter?"}
%%         click node7 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:318:320"
%%         node7 -->|"Yes"| node8["Add '&' between values"]
%%         click node8 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:319:319"
%%         node8 --> node5
%%         node7 -->|"No"| node9["Done with this parameter"]
%%       end
%%       node4 --> node10{"More parameters?"}
%%       node9 --> node10
%%       click node10 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:324:326"
%%       node10 -->|"Yes"| node11["Add '&' between parameters"]
%%       click node11 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:325:325"
%%       node11 --> node2
%%       node10 -->|"No"| node12["All parameters processed"]
%%     end
%%     node12 --> node13["Return query string for redirect"]
%%     click node13 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:329:330"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="295">

---

In ActionRedirect.getParameterString, we loop through <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="299:7:7" line-data="        Iterator iterator = parameterValues.keySet().iterator();">`parameterValues`</SwmToken> and build a query string. Single values are appended directly, arrays are handled by appending each value with the parameter name, all separated by '&'.

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

Finally, after building the parameter string, we return it so ActionRedirect.toString can include it in the object's debug output.

```java
        return strParam.toString();
    }
```

---

</SwmSnippet>

### Generating a debug string for the redirect

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start: Prepare human-readable summary of
redirect"]
  click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:340:341"
  node1 --> node2["Add original path to summary"]
  click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:344:344"
  node2 --> node3["Add parameters to summary"]
  click node3 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:345:345"
  node3 --> node4["Add anchor to summary"]
  click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:346:346"
  node4 --> node5["Return combined summary string for
debugging/logging"]
  click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:348:349"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start: Prepare human-readable summary of
%% redirect"]
%%   click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:340:341"
%%   node1 --> node2["Add original path to summary"]
%%   click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:344:344"
%%   node2 --> node3["Add parameters to summary"]
%%   click node3 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:345:345"
%%   node3 --> node4["Add anchor to summary"]
%%   click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:346:346"
%%   node4 --> node5["Return combined summary string for
%% debugging/logging"]
%%   click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:348:349"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="340">

---

In ActionRedirect.toString, we start building the debug string by appending the original path. This gives context for where the redirect originates, so we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionRedirect.java" pos="344:13:13" line-data="        result.append(&quot;originalPath=&quot;).append(getOriginalPath()).append(&quot;;&quot;);">`getOriginalPath`</SwmToken> next.

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

Back in ActionRedirect.toString, after appending the original path, we add the parameter string to show what data is included in the redirect.

```java
        result.append("parameterString=").append(getParameterString()).append("]");
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="346">

---

Back in ActionRedirect.toString, after adding the parameter string, we append the anchor string to capture any fragment identifier used in the redirect.

```java
        result.append("anchorString=").append(getAnchorString()).append("]");

        return result.toString();
    }
```

---

</SwmSnippet>

### Finalizing the redirect URL with parameters and anchor

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start with original path"] --> node2{"Are there query parameters?"}
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:234:237"
    node2 -->|"No"| node3["Append anchor string"]
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:238:263"
    node2 -->|"Yes"| node4{"Does original path contain '?'"}
    click node4 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:246:256"
    node4 -->|"No"| node5["Add parameters with '?'"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:259:262"
    node4 -->|"Yes"| node6{"Does original path end with '?'"}
    click node6 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:250:251"
    node6 -->|"Yes"| node7["Add parameters"]
    click node7 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:262:262"
    node6 -->|"No"| node8["Add parameters with '&'"]
    click node8 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:254:262"
    node5 --> node3
    node7 --> node3
    node8 --> node3
    node3 --> node9["Return complete URL"]
    click node9 openCode "core/src/main/java/org/apache/struts/action/ActionRedirect.java:266:269"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start with original path"] --> node2{"Are there query parameters?"}
%%     click node1 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:234:237"
%%     node2 -->|"No"| node3["Append anchor string"]
%%     click node2 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:238:263"
%%     node2 -->|"Yes"| node4{"Does original path contain '?'"}
%%     click node4 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:246:256"
%%     node4 -->|"No"| node5["Add parameters with '?'"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:259:262"
%%     node4 -->|"Yes"| node6{"Does original path end with '?'"}
%%     click node6 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:250:251"
%%     node6 -->|"Yes"| node7["Add parameters"]
%%     click node7 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:262:262"
%%     node6 -->|"No"| node8["Add parameters with '&'"]
%%     click node8 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:254:262"
%%     node5 --> node3
%%     node7 --> node3
%%     node8 --> node3
%%     node3 --> node9["Return complete URL"]
%%     click node9 openCode "<SwmPath>[core/…/action/ActionRedirect.java](core/src/main/java/org/apache/struts/action/ActionRedirect.java)</SwmPath>:266:269"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="234">

---

Back in ActionRedirect.getPath, after getting the parameter string, we handle combining the original path, parameters, and anchor. The method checks for existing query separators and appends everything to build the final URL.

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

## Copying additional redirect configuration from base

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="132">

---

Back in ActionRedirect.ActionRedirect, after setting the path, we copy the module from the base config. This keeps routing consistent with the original configuration, so we call ForwardConfig.setModule next.

```java
        setPath(baseConfig.getPath());
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ForwardConfig.java" line="198">

---

ForwardConfig.setPath checks if configuration is frozen before setting the path. If configured, it throws an exception to prevent changes.

```java
    public void setPath(String path) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        this.path = path;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="133">

---

Back in ActionRedirect.ActionRedirect, after setting the path, we copy the module from the base config. This keeps routing consistent with the original configuration, so we call ForwardConfig.setModule next.

```java
        setModule(baseConfig.getModule());
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ForwardConfig.java" line="210">

---

ForwardConfig.setModule checks if configuration is frozen before setting the module. If configured, it throws an exception to prevent changes.

```java
    public void setModule(String module) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        this.module = module;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="134">

---

Back in ActionRedirect.ActionRedirect, after setting the module, we copy the redirect flag from the base config. This controls whether the response is a redirect or forward, so we call ForwardConfig.setRedirect next.

```java
        setRedirect(true);
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ForwardConfig.java" line="222">

---

ForwardConfig.setRedirect checks if configuration is frozen before setting the redirect flag. If configured, it throws an exception to prevent changes.

```java
    public void setRedirect(boolean redirect) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        this.redirect = redirect;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="135">

---

Back in ActionRedirect.ActionRedirect, after setting the redirect flag, we inherit properties from the base config. This copies any additional settings needed for the redirect, so we call BaseConfig.inheritProperties next.

```java
        inheritProperties(baseConfig);
```

---

</SwmSnippet>

## Copying properties from base configuration

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Begin inheriting properties from base
configuration"]
    click node1 openCode "core/src/main/java/org/apache/struts/config/BaseConfig.java:132:150"
    
    subgraph loop1["For each property in base configuration"]
        node1 --> node2{"Is property missing in current
configuration?"}
        click node2 openCode "core/src/main/java/org/apache/struts/config/BaseConfig.java:139:149"
        node2 -->|"Yes"| node3["Add property to current configuration"]
        click node3 openCode "core/src/main/java/org/apache/struts/config/BaseConfig.java:145:148"
        node2 -->|"No"| node4["Check next property"]
        click node4 openCode "core/src/main/java/org/apache/struts/config/BaseConfig.java:139:149"
        node3 --> node4
    end
    node4 --> node5["Finish inheritance process"]
    click node5 openCode "core/src/main/java/org/apache/struts/config/BaseConfig.java:150:150"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Begin inheriting properties from base
%% configuration"]
%%     click node1 openCode "<SwmPath>[core/…/config/BaseConfig.java](core/src/main/java/org/apache/struts/config/BaseConfig.java)</SwmPath>:132:150"
%%     
%%     subgraph loop1["For each property in base configuration"]
%%         node1 --> node2{"Is property missing in current
%% configuration?"}
%%         click node2 openCode "<SwmPath>[core/…/config/BaseConfig.java](core/src/main/java/org/apache/struts/config/BaseConfig.java)</SwmPath>:139:149"
%%         node2 -->|"Yes"| node3["Add property to current configuration"]
%%         click node3 openCode "<SwmPath>[core/…/config/BaseConfig.java](core/src/main/java/org/apache/struts/config/BaseConfig.java)</SwmPath>:145:148"
%%         node2 -->|"No"| node4["Check next property"]
%%         click node4 openCode "<SwmPath>[core/…/config/BaseConfig.java](core/src/main/java/org/apache/struts/config/BaseConfig.java)</SwmPath>:139:149"
%%         node3 --> node4
%%     end
%%     node4 --> node5["Finish inheritance process"]
%%     click node5 openCode "<SwmPath>[core/…/config/BaseConfig.java](core/src/main/java/org/apache/struts/config/BaseConfig.java)</SwmPath>:150:150"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="132">

---

BaseConfig.inheritProperties checks for configuration state, then copies properties from the base config only if they're not already set. This avoids overwriting existing values, and we call <SwmToken path="core/src/main/java/org/apache/struts/config/BaseConfig.java" pos="147:1:1" line-data="                setProperty(key, value);">`setProperty`</SwmToken> next to actually copy each property.

```java
    protected void inheritProperties(BaseConfig baseConfig) {
        throwIfConfigured();

        // Inherit forward properties
        Properties baseProperties = baseConfig.getProperties();
        Enumeration keys = baseProperties.propertyNames();

        while (keys.hasMoreElements()) {
            String key = (String) keys.nextElement();

            // Check if we have this property before copying it
            String value = this.getProperty(key);

            if (value == null) {
                value = baseProperties.getProperty(key);
                setProperty(key, value);
            }
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="88">

---

BaseConfig.setProperty checks for configuration state before setting a property. If not configured, it sets the property in the Properties object.

```java
    public void setProperty(String key, String value) {
        throwIfConfigured();
        properties.setProperty(key, value);
    }
```

---

</SwmSnippet>

## Initializing redirect parameters

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionRedirect.java" line="136">

---

Back in ActionRedirect.ActionRedirect, after inheriting properties, we initialize parameters to make sure the redirect is ready to handle any data passed to the client.

```java
        initializeParameters();
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
