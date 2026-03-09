---
title: Preparing tags for rendering with dynamic attributes
---
This document describes how tags in JSP pages are prepared for rendering by evaluating and applying all dynamic attribute values. Each attribute is resolved at runtime to reflect the current page context, and after all attributes are set, the tag's main logic is executed. Resource tags are handled similarly, ensuring dynamic and flexible page content.

```mermaid
flowchart TD
  node1["Starting the tag processing"]:::HeadingStyle
  click node1 goToHeading "Starting the tag processing"
  node1 --> node2{"Are there dynamic attributes?"}
  node2 -->|"Yes"| node3["Evaluating and applying tag attributes"]:::HeadingStyle
  click node3 goToHeading "Evaluating and applying tag attributes"
  node2 -->|"No"| node4["Delegating to base tag logic"]:::HeadingStyle
  click node4 goToHeading "Delegating to base tag logic"
  node3 --> node4
  node4 --> node5{"Does the tag reference resources?"}
  node5 -->|"Yes"| node6["Processing resource tag"]:::HeadingStyle
  click node6 goToHeading "Processing resource tag"
  node6 --> node7["Resolving resource attributes"]:::HeadingStyle
  click node7 goToHeading "Resolving resource attributes"
  node5 -->|"No"| node8["Tag ready for rendering"]
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Starting the tag processing

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" line="694">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="694:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we kick off tag processing by resolving all dynamic expressions for the tag's attributes. Calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> here ensures that any values set via expressions are evaluated and applied before the tag does anything else.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

```

---

</SwmSnippet>

## Evaluating and applying tag attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Evaluate and apply property
expressions"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:706:874"
    
    subgraph loop1["For each configurable property (e.g.,
accessKey, alt, bundle, etc.)"]
        node2["Evaluate dynamic expression for property"]
        click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:711:872"
        node2 --> node3{"Does the expression yield a value?"}
        click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:711:872"
        node3 -->|"Yes"| node4["Update property with evaluated value"]
        click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:714:872"
        node3 -->|"No"| node5["Continue to next property"]
        click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:711:872"
        node4 --> node6["Next property"]
        node5 --> node6
        node6 --> node2
    end
    loop1 --> node7["Reset button is fully configured"]
    click node7 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java:874:874"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Evaluate and apply property
%% expressions"]
%%     click node1 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:706:874"
%%     
%%     subgraph loop1["For each configurable property (e.g.,
%% <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="712:6:6" line-data="                EvalHelper.evalString(&quot;accessKey&quot;, getAccesskeyExpr(), this,">`accessKey`</SwmToken>, alt, bundle, etc.)"]
%%         node2["Evaluate dynamic expression for property"]
%%         click node2 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:711:872"
%%         node2 --> node3{"Does the expression yield a value?"}
%%         click node3 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:711:872"
%%         node3 -->|"Yes"| node4["Update property with evaluated value"]
%%         click node4 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:714:872"
%%         node3 -->|"No"| node5["Continue to next property"]
%%         click node5 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:711:872"
%%         node4 --> node6["Next property"]
%%         node5 --> node6
%%         node6 --> node2
%%     end
%%     loop1 --> node7["Reset button is fully configured"]
%%     click node7 openCode "<SwmPath>[el/…/html/ELResetTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java)</SwmPath>:874:874"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" line="706">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="706:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we loop through each attribute, resolve its value from the page context, and set it. When we hit the bundle attribute, we call its setter, which checks if configuration is frozen before updating. This prevents changes if the config is locked.

```java
    private void evaluateExpressions()
        throws JspException {
        String string = null;
        Boolean bool = null;

        if ((string =
                EvalHelper.evalString("accessKey", getAccesskeyExpr(), this,
                    pageContext)) != null) {
            setAccesskey(string);
        }

        if ((string =
                EvalHelper.evalString("alt", getAltExpr(), this, pageContext)) != null) {
            setAlt(string);
        }

        if ((string =
                EvalHelper.evalString("altKey", getAltKeyExpr(), this,
                    pageContext)) != null) {
            setAltKey(string);
        }

        if ((string =
                EvalHelper.evalString("bundle", getBundleExpr(), this,
                    pageContext)) != null) {
            setBundle(string);
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ExceptionConfig.java" line="89">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="89:5:5" line-data="    public void setBundle(String bundle) {">`setBundle`</SwmToken> checks if the config is frozen before updating the bundle value. If it's locked, it throws an exception, so you can't change the bundle after setup. This is stricter than a normal setter.

```java
    public void setBundle(String bundle) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        this.bundle = bundle;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" line="734">

---

After coming back from <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="731:1:1" line-data="            setBundle(string);">`setBundle`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="34:4:4" line-data="public class ExceptionConfig extends BaseConfig {">`ExceptionConfig`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> continues resolving and setting the rest of the tag's attributes. If the bundle setter threw, we'd bail out, but otherwise, we finish applying all dynamic values.

```java
        if ((string =
        		EvalHelper.evalString("dir", getDirExpr(), this,
        			pageContext)) != null) {
        	setDir(string);
        }
        
        if ((bool =
                EvalHelper.evalBoolean("disabled", getDisabledExpr(), this,
                    pageContext)) != null) {
            setDisabled(bool.booleanValue());
        }

        if ((string =
            	EvalHelper.evalString("lang", getLangExpr(), this,
            		pageContext)) != null) {
        	setLang(string);
        }

        if ((string =
                EvalHelper.evalString("onblur", getOnblurExpr(), this,
                    pageContext)) != null) {
            setOnblur(string);
        }

        if ((string =
                EvalHelper.evalString("onchange", getOnchangeExpr(), this,
                    pageContext)) != null) {
            setOnchange(string);
        }

        if ((string =
                EvalHelper.evalString("onclick", getOnclickExpr(), this,
                    pageContext)) != null) {
            setOnclick(string);
        }

        if ((string =
                EvalHelper.evalString("ondblclick", getOndblclickExpr(), this,
                    pageContext)) != null) {
            setOndblclick(string);
        }

        if ((string =
                EvalHelper.evalString("onfocus", getOnfocusExpr(), this,
                    pageContext)) != null) {
            setOnfocus(string);
        }

        if ((string =
                EvalHelper.evalString("onkeydown", getOnkeydownExpr(), this,
                    pageContext)) != null) {
            setOnkeydown(string);
        }

        if ((string =
                EvalHelper.evalString("onkeypress", getOnkeypressExpr(), this,
                    pageContext)) != null) {
            setOnkeypress(string);
        }

        if ((string =
                EvalHelper.evalString("onkeyup", getOnkeyupExpr(), this,
                    pageContext)) != null) {
            setOnkeyup(string);
        }

        if ((string =
                EvalHelper.evalString("onmousedown", getOnmousedownExpr(),
                    this, pageContext)) != null) {
            setOnmousedown(string);
        }

        if ((string =
                EvalHelper.evalString("onmousemove", getOnmousemoveExpr(),
                    this, pageContext)) != null) {
            setOnmousemove(string);
        }

        if ((string =
                EvalHelper.evalString("onmouseout", getOnmouseoutExpr(), this,
                    pageContext)) != null) {
            setOnmouseout(string);
        }

        if ((string =
                EvalHelper.evalString("onmouseover", getOnmouseoverExpr(),
                    this, pageContext)) != null) {
            setOnmouseover(string);
        }

        if ((string =
                EvalHelper.evalString("onmouseup", getOnmouseupExpr(), this,
                    pageContext)) != null) {
            setOnmouseup(string);
        }

        if ((string =
                EvalHelper.evalString("property", getPropertyExpr(), this,
                    pageContext)) != null) {
            setProperty(string);
        }

        if ((string =
                EvalHelper.evalString("style", getStyleExpr(), this, pageContext)) != null) {
            setStyle(string);
        }

        if ((string =
                EvalHelper.evalString("styleClass", getStyleClassExpr(), this,
                    pageContext)) != null) {
            setStyleClass(string);
        }

        if ((string =
                EvalHelper.evalString("styleId", getStyleIdExpr(), this,
                    pageContext)) != null) {
            setStyleId(string);
        }

        if ((string =
                EvalHelper.evalString("tabindex", getTabindexExpr(), this,
                    pageContext)) != null) {
            setTabindex(string);
        }

        if ((string =
                EvalHelper.evalString("title", getTitleExpr(), this, pageContext)) != null) {
            setTitle(string);
        }

        if ((string =
                EvalHelper.evalString("titleKey", getTitleKeyExpr(), this,
                    pageContext)) != null) {
            setTitleKey(string);
        }

        if ((string =
                EvalHelper.evalString("value", getValueExpr(), this, pageContext)) != null) {
            setValue(string);
        }
    }
```

---

</SwmSnippet>

## Delegating to base tag logic

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" line="697">

---

After finishing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="697:6:6" line-data="        return (super.doStartTag());">`doStartTag`</SwmToken>, we hand off to the base tag logic. This uses the attributes we just set to handle rendering and any tag-specific behavior. The next step involves processing related tags like <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> to handle resources.

```java
        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Processing resource tag

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node0["Start tag processing"]
    click node0 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:120:120"
    node0 --> node1["Evaluate dynamic expressions"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:121:121"
    node1 --> node2["Delegate tag processing to parent class"]
    click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:123:123"
    node2 --> node3["Tag processing handled by parent class"]
    click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:123:123"
    node3 --> node4["End"]
    click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:124:124"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node0["Start tag processing"]
%%     click node0 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:120:120"
%%     node0 --> node1["Evaluate dynamic expressions"]
%%     click node1 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:121:121"
%%     node1 --> node2["Delegate tag processing to parent class"]
%%     click node2 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:123:123"
%%     node2 --> node3["Tag processing handled by parent class"]
%%     click node3 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:123:123"
%%     node3 --> node4["End"]
%%     click node4 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:124:124"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="120">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="120:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> starts by resolving its dynamic attributes, then delegates to its base logic. This lets the tag reference resources based on the current page context.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Resolving resource attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Evaluate resource expressions"]
  node1 --> node2{"'id' expression yields value?"}
  click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:136:139"
  node2 -->|"Yes"| node3["Update resource 'id'"]
  click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:138:139"
  node2 -->|"No"| node4
  node1 --> node5{"'input' expression yields value?"}
  click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:141:144"
  node5 -->|"Yes"| node6["Update resource 'input'"]
  click node6 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:143:144"
  node5 -->|"No"| node4
  node1 --> node7{"'name' expression yields value?"}
  click node7 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:146:149"
  node7 -->|"Yes"| node8["Update resource 'name'"]
  click node8 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:148:149"
  node7 -->|"No"| node4
  node4["End"]
  click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:150:150"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Evaluate resource expressions"]
%%   node1 --> node2{"'id' expression yields value?"}
%%   click node2 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:136:139"
%%   node2 -->|"Yes"| node3["Update resource 'id'"]
%%   click node3 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:138:139"
%%   node2 -->|"No"| node4
%%   node1 --> node5{"'input' expression yields value?"}
%%   click node5 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:141:144"
%%   node5 -->|"Yes"| node6["Update resource 'input'"]
%%   click node6 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:143:144"
%%   node5 -->|"No"| node4
%%   node1 --> node7{"'name' expression yields value?"}
%%   click node7 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:146:149"
%%   node7 -->|"Yes"| node8["Update resource 'name'"]
%%   click node8 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:148:149"
%%   node7 -->|"No"| node4
%%   node4["End"]
%%   click node4 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:150:150"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="132">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="132:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken> for <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>, we resolve id and input from the page context. Setting input calls into <SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="180:14:14" line-data="     * @param actionConfig The {@link ActionConfig} that this config is from,">`ActionConfig`</SwmToken>, which checks if config is frozen before updating. This prevents changes after setup.

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

<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="473:5:5" line-data="    public void setInput(String input) {">`setInput`</SwmToken> checks if config is frozen before updating the input value. If it's locked, it throws, so you can't change input after setup. Otherwise, it just sets the value.

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

After coming back from <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="180:14:14" line-data="     * @param actionConfig The {@link ActionConfig} that this config is from,">`ActionConfig`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELResetTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> finishes resolving and setting the remaining attributes like name. If input failed, we'd bail out, otherwise we keep applying values.

```java
        if ((string =
                EvalHelper.evalString("name", getNameExpr(), this, pageContext)) != null) {
            setName(string);
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
