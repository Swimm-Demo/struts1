---
title: Rendering a hidden input field
---
This document describes how a hidden input field is rendered in a web form. The flow supports both direct and dynamic values, resolves and filters the value for safe output, and writes it to the page. Localization and user-friendly error handling are included.

```mermaid
flowchart TD
  node1["Rendering the hidden input tag"]:::HeadingStyle
  click node1 goToHeading "Rendering the hidden input tag"
  node1 --> node2["Preparing the value for output"]:::HeadingStyle
  click node2 goToHeading "Preparing the value for output"
  node2 --> node3{"Is value provided directly?"}
  node3 -->|"Yes"| node4["Writing the final value to the page"]:::HeadingStyle
  click node4 goToHeading "Writing the final value to the page"
  node3 -->|"No"| node5["Retrieving bean properties for rendering"]:::HeadingStyle
  click node5 goToHeading "Retrieving bean properties for rendering"
  node5 --> node4
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Rendering the hidden input tag

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Render hidden input tag"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:68:69"
    node1 --> node2{"Should value be rendered separately?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:71:74"
    node2 -->|"No"| node6["Continue processing page"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:73:74"
    node2 -->|"Yes"| node3{"Is explicit value available?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:80:91"
    node3 -->|"Yes"| node4["Use and filter explicit value"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:81:82"
    node3 -->|"No"| node5["Retrieving bean properties for rendering"]
    
    node4 --> node7["Display value in page"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:93:93"
    node5 --> node7
    node7 --> node6
    
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node5 goToHeading "Retrieving bean properties for rendering"
node5:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Render hidden input tag"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:68:69"
%%     node1 --> node2{"Should value be rendered separately?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:71:74"
%%     node2 -->|"No"| node6["Continue processing page"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:73:74"
%%     node2 -->|"Yes"| node3{"Is explicit value available?"}
%%     click node3 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:80:91"
%%     node3 -->|"Yes"| node4["Use and filter explicit value"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:81:82"
%%     node3 -->|"No"| node5["Retrieving bean properties for rendering"]
%%     
%%     node4 --> node7["Display value in page"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:93:93"
%%     node5 --> node7
%%     node7 --> node6
%%     
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node5 goToHeading "Retrieving bean properties for rendering"
%% node5:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" line="67">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" pos="67:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we render the hidden input tag and check if the value should be written directly or handled in the tag body. If 'write' is false, we move on to evaluate expressions using <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>, which lets us handle dynamic values before rendering.

```java
    public int doStartTag() throws JspException {
        // Render the <html:input type="hidden"> tag as before
        super.doStartTag();

        // Is rendering the value separately requested?
        if (!write) {
            return (EVAL_BODY_TAG);
        }

```

---

</SwmSnippet>

## Evaluating resource tag expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="120">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="120:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> evaluates any EL expressions for tag attributes, then calls the parent tag's start logic. This lets us handle dynamic values before rendering the tag.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

        return (super.doStartTag());
    }
```

---

</SwmSnippet>

## Resolving and setting tag attribute values

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start evaluating resource attributes"] --> node2{"Does 'id' expression evaluate to a
value?"}
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:132:136"
    node2 -->|"Yes"| node3["Update resource id"]
    click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:136:139"
    node2 -->|"No"| node4{"Does 'input' expression evaluate to a
value?"}
    click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:138:139"
    node3 --> node4
    node4 -->|"Yes"| node5["Update resource input"]
    click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:141:144"
    node4 -->|"No"| node6{"Does 'name' expression evaluate to a
value?"}
    click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:143:144"
    node5 --> node6
    node6 -->|"Yes"| node7["Update resource name"]
    click node6 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:146:149"
    node6 -->|"No"| node8["Finish attribute evaluation"]
    click node7 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:148:149"
    node7 --> node8
    click node8 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:150:150"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start evaluating resource attributes"] --> node2{"Does 'id' expression evaluate to a
%% value?"}
%%     click node1 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:132:136"
%%     node2 -->|"Yes"| node3["Update resource id"]
%%     click node2 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:136:139"
%%     node2 -->|"No"| node4{"Does 'input' expression evaluate to a
%% value?"}
%%     click node3 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:138:139"
%%     node3 --> node4
%%     node4 -->|"Yes"| node5["Update resource input"]
%%     click node4 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:141:144"
%%     node4 -->|"No"| node6{"Does 'name' expression evaluate to a
%% value?"}
%%     click node5 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:143:144"
%%     node5 --> node6
%%     node6 -->|"Yes"| node7["Update resource name"]
%%     click node6 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:146:149"
%%     node6 -->|"No"| node8["Finish attribute evaluation"]
%%     click node7 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:148:149"
%%     node7 --> node8
%%     click node8 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:150:150"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="132">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="132:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we resolve EL expressions for each attribute and set them on the tag. When 'input' is resolved, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken>, which updates the configuration if allowed.

```java
    private void evaluateExpressions()
        throws JspException {
        String string = null;

        if ((string =
                EvalHelper.evalString("id", getIdExpr(), this, pageContext)) != null) {
            setId(string);
        }

        if ((string =
                EvalHelper.evalString("input", getInputExpr(), this, pageContext)) != null) {
            setInput(string);
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="473">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="473:5:5" line-data="    public void setInput(String input) {">`setInput`</SwmToken> checks if configuration is frozen using the 'configured' flag. If it's frozen, it throws an exception, so input can't be changed after finalization. This keeps config immutable after setup.

```java
    public void setInput(String input) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        this.input = input;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="146">

---

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="121:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> after <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken>, we keep resolving and setting other attributes like 'name'. If <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken> failed due to frozen config, this part wouldn't run, so the tag setup depends on config state.

```java
        if ((string =
                EvalHelper.evalString("name", getNameExpr(), this, pageContext)) != null) {
            setName(string);
        }
    }
```

---

</SwmSnippet>

## Preparing the value for output

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Decide value for hidden field"] --> node2{"Is direct value provided?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:76:80"
    node2 -->|"Yes"| node3["Filter direct value for safe output"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:80:82"
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:81:81"
    node2 -->|"No"| node4["Look up value using name/property"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:83:84"
    node4 --> node5{"Is looked-up value available?"}
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:86:90"
    node5 -->|"Yes"| node6["Filter looked-up value for safe output"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:89:89"
    node5 -->|"No"| node7["Use empty string as value"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:87:87"
    node3 --> node8["Render filtered value in hidden field"]
    node6 --> node8
    node7 --> node8
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java:78:91"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Decide value for hidden field"] --> node2{"Is direct value provided?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:76:80"
%%     node2 -->|"Yes"| node3["Filter direct value for safe output"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:80:82"
%%     click node3 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:81:81"
%%     node2 -->|"No"| node4["Look up value using name/property"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:83:84"
%%     node4 --> node5{"Is <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="187:24:26" line-data="     * @throws JspException if a class cast exception occurs on a looked-up">`looked-up`</SwmToken> value available?"}
%%     click node5 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:86:90"
%%     node5 -->|"Yes"| node6["Filter <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="187:24:26" line-data="     * @throws JspException if a class cast exception occurs on a looked-up">`looked-up`</SwmToken> value for safe output"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:89:89"
%%     node5 -->|"No"| node7["Use empty string as value"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:87:87"
%%     node3 --> node8["Render filtered value in hidden field"]
%%     node6 --> node8
%%     node7 --> node8
%%     click node8 openCode "<SwmPath>[taglib/…/html/HiddenTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java)</SwmPath>:78:91"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" line="76">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" pos="67:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> after <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>, we prep the value for output. If it's not set, we use <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" pos="81:5:5" line-data="            results = TagUtils.getInstance().filter(value);">`TagUtils`</SwmToken> to look up and filter it, making sure it's safe and ready for rendering.

```java
        // Calculate the value to be rendered separately
        // * @since Struts 1.1
        String results = null;

        if (value != null) {
            results = TagUtils.getInstance().filter(value);
        } else {
            Object value =
                TagUtils.getInstance().lookup(pageContext, name, property, null);

            if (value == null) {
                results = "";
            } else {
                results = TagUtils.getInstance().filter(value.toString());
            }
        }

```

---

</SwmSnippet>

## Retrieving bean properties for rendering

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="897">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="897:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String property,">`lookup`</SwmToken>, we try to find the bean and its property. If it's missing, we grab an error message from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> to throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="898:8:8" line-data="        String scope) throws JspException {">`JspException`</SwmToken>, making error handling consistent and localized.

```java
    public Object lookup(PageContext pageContext, String name, String property,
        String scope) throws JspException {
        // Look up the requested bean, and return if requested
        Object bean = lookup(pageContext, name, scope);

        if (bean == null) {
            JspException e = null;

            if (scope == null) {
                e = new JspException(messages.getMessage("lookup.bean.any", name));
            } else {
```

---

</SwmSnippet>

### Generating localized error messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> here just passes the call to the locale-aware version, so we can handle localization and argument formatting in one spot.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

### Formatting and caching localized messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Request message using key and locale,
inserting argument"] --> node2{"Is message template found for key and
locale?"}
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 -->|"Yes"| node3["Format message with argument"]
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:286:312"
    node2 -->|"No"| node4{"Is system set to return null for missing
messages?"}
    node4 -->|"Yes"| node5["Return null"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
    node4 -->|"No"| node6["Return placeholder with key and locale"]
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:301:303"
    node3 --> node7["Return formatted message"]
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Request message using key and locale,
%% inserting argument"] --> node2{"Is message template found for key and
%% locale?"}
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 -->|"Yes"| node3["Format message with argument"]
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:286:312"
%%     node2 -->|"No"| node4{"Is system set to return null for missing
%% messages?"}
%%     node4 -->|"Yes"| node5["Return null"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%     node4 -->|"No"| node6["Return placeholder with key and locale"]
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:301:303"
%%     node3 --> node7["Return formatted message"]
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> here wraps the argument in an array and calls the main formatting method, so all messages go through the same formatting logic.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> here handles formatting and caching of localized messages. It defaults locale if needed, escapes format strings, caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> instances for performance, and returns placeholders if messages are missing.

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

### Handling property access and exceptions

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Is a property specified?"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:916:918"
    node1 -->|"No"| node2["Return the bean"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:917:918"
    node1 -->|"Yes"| node3["Retrieve property from bean"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:922:923"
    node3 -->|"Success"| node4["Return property value"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:922:923"
    node3 -->|"Access/Argument/Invocation failure"| node5["Return error with details"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:924:941"
    node3 -->|"NoSuchMethod failure"| node6{"Is bean name the default key?"}
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:949:955"
    node6 -->|"Yes"| node7["Use bean's class name in error message"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:950:954"
    node6 -->|"No"| node8["Return error with bean name"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:957:958"
    node7 --> node8
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Is a property specified?"]
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:916:918"
%%     node1 -->|"No"| node2["Return the bean"]
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:917:918"
%%     node1 -->|"Yes"| node3["Retrieve property from bean"]
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:922:923"
%%     node3 -->|"Success"| node4["Return property value"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:922:923"
%%     node3 -->|"Access/Argument/Invocation failure"| node5["Return error with details"]
%%     click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:924:941"
%%     node3 -->|"NoSuchMethod failure"| node6{"Is bean name the default key?"}
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:949:955"
%%     node6 -->|"Yes"| node7["Use bean's class name in error message"]
%%     click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:950:954"
%%     node6 -->|"No"| node8["Return error with bean name"]
%%     click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:957:958"
%%     node7 --> node8
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="908">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="908:14:14" line-data="                e = new JspException(messages.getMessage(&quot;lookup.bean&quot;, name,">`lookup`</SwmToken>, after getting messages from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken>, we handle property access and catch exceptions. Each error gets a localized message, making debugging easier and output more user-friendly.

```java
                e = new JspException(messages.getMessage("lookup.bean", name,
                            scope));
            }

            saveException(pageContext, e);
            throw e;
        }

        if (property == null) {
            return bean;
        }

        // Locate and return the specified property
        try {
            return PropertyUtils.getProperty(bean, property);
        } catch (IllegalAccessException e) {
            saveException(pageContext, e);
            throw new JspException(messages.getMessage("lookup.access",
                    property, name), e);
        } catch (IllegalArgumentException e) {
            saveException(pageContext, e);
            throw new JspException(messages.getMessage("lookup.argument",
                    property, name), e);
        } catch (InvocationTargetException e) {
            Throwable t = e.getTargetException();

            if (t == null) {
                t = e;
            }

            saveException(pageContext, t);
            throw new JspException(messages.getMessage("lookup.target",
                    property, name), e);
        } catch (NoSuchMethodException e) {
            saveException(pageContext, e);

            String beanName = name;

            // Name defaults to Contants.BEAN_KEY if no name is specified by
            // an input tag. Thus lookup the bean under the key and use
            // its class name for the exception message.
            if (Constants.BEAN_KEY.equals(name)) {
                Object obj = pageContext.findAttribute(Constants.BEAN_KEY);

                if (obj != null) {
                    beanName = obj.getClass().getName();
                }
            }

            throw new JspException(messages.getMessage("lookup.method",
                    property, beanName), e);
        }
    }
```

---

</SwmSnippet>

## Writing the final value to the page

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" line="93">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" pos="67:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, after getting the value from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HiddenTag.java" pos="93:1:1" line-data="        TagUtils.getInstance().write(pageContext, results);">`TagUtils`</SwmToken>, we write it to the page using TagUtils.write. This wraps the output and handles errors with localized messages if anything goes wrong.

```java
        TagUtils.getInstance().write(pageContext, results);

        return (EVAL_BODY_TAG);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="1186">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1186:5:5" line-data="    public void write(PageContext pageContext, String text)">`write`</SwmToken> prints the text to the page. If there's an IO error, it grabs a localized message from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="34:10:10" line-data="import org.apache.struts.util.MessageResources;">`MessageResources`</SwmToken> and throws a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1187:3:3" line-data="        throws JspException {">`JspException`</SwmToken>, so errors are handled and reported cleanly.

```java
    public void write(PageContext pageContext, String text)
        throws JspException {
        JspWriter writer = pageContext.getOut();

        try {
            writer.print(text);
        } catch (IOException e) {
            saveException(pageContext, e);
            throw new JspException(messages.getMessage("write.io", e.toString()), e);
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
