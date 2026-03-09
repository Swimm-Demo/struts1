---
title: Preparing tag attributes for dynamic rendering
---
This document describes how tag attributes are dynamically resolved and prepared for rendering, supporting flexible customization of UI tags. The flow covers both cancel and resource tags, ensuring that all dynamic values are set and configuration rules are respected before delegating to the main tag logic.

# Starting the Cancel Tag Evaluation

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" line="694">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="694:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we start by resolving all EL expressions for the tag's attributes. Calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> here ensures any dynamic values are set up before the tag continues processing.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

```

---

</SwmSnippet>

## Evaluating Cancel Tag Expressions

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Begin evaluating button configuration"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java:706:874"
    
    subgraph loop1["For each button attribute (access key,
alt text, disabled, event handlers,
style, value, etc.)"]
        node2{"Is there a dynamic value for this
attribute?"}
        click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java:711:872"
        node2 -->|"Yes"| node3["Update attribute with evaluated value"]
        click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java:714:873"
        node3 --> node5["Next attribute"]
        node2 -->|"No"| node5
    end
    node5["All attributes processed"]
    click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java:874:874"
    node5 --> node4["Button is ready with updated properties"]
    click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java:874:874"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Begin evaluating button configuration"]
%%     click node1 openCode "<SwmPath>[el/…/html/ELCancelTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java)</SwmPath>:706:874"
%%     
%%     subgraph loop1["For each button attribute (access key,
%% alt text, disabled, event handlers,
%% style, value, etc.)"]
%%         node2{"Is there a dynamic value for this
%% attribute?"}
%%         click node2 openCode "<SwmPath>[el/…/html/ELCancelTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java)</SwmPath>:711:872"
%%         node2 -->|"Yes"| node3["Update attribute with evaluated value"]
%%         click node3 openCode "<SwmPath>[el/…/html/ELCancelTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java)</SwmPath>:714:873"
%%         node3 --> node5["Next attribute"]
%%         node2 -->|"No"| node5
%%     end
%%     node5["All attributes processed"]
%%     click node5 openCode "<SwmPath>[el/…/html/ELCancelTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java)</SwmPath>:874:874"
%%     node5 --> node4["Button is ready with updated properties"]
%%     click node4 openCode "<SwmPath>[el/…/html/ELCancelTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java)</SwmPath>:874:874"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" line="706">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="706:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we go through each attribute, resolve its value, and set it if present. When we hit 'bundle', we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="731:1:1" line-data="            setBundle(string);">`setBundle`</SwmToken>, which might have extra checks, so we need to jump into the <SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="34:4:4" line-data="public class ExceptionConfig extends BaseConfig {">`ExceptionConfig`</SwmToken> logic next.

```java
    private void evaluateExpressions()
        throws JspException {
        String string = null;
        Boolean bool = null;

        if ((string =
                EvalHelper.evalString("accesskey", getAccesskeyExpr(), this,
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

<SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="89:5:5" line-data="    public void setBundle(String bundle) {">`setBundle`</SwmToken> only updates the bundle if the configuration isn't frozen. If it's already locked, it throws an exception to block changes after setup.

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

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" line="734">

---

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken>, after handling 'bundle' (which might throw if config is frozen), we just keep evaluating and setting the rest of the attributes. The rest don't have the same config lock.

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

## Delegating to Parent Tag Logic

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" line="697">

---

After finishing up in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="697:6:6" line-data="        return (super.doStartTag());">`doStartTag`</SwmToken> just calls the parent method to do the main tag work. All our tag-specific setup is done, so we hand off control.

```java
        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Starting the Resource Tag Evaluation

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="120">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="120:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> in the resource tag resolves all EL expressions for its attributes, then hands off to the parent for the main tag logic. This keeps everything in sync before rendering or resource handling.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Evaluating Resource Tag Expressions

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Evaluate expressions for resource
attributes"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:132:150"
    node1 --> node2{"Is there a value for 'id'?"}
    click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:136:139"
    node2 -->|"Yes"| node3["Update resource 'id'"]
    click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:138:139"
    node2 -->|"No"| node4{"Is there a value for 'input'?"}
    node3 --> node4
    node4 -->|"Yes"| node5["Update resource 'input'"]
    click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:141:144"
    click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:143:144"
    node4 -->|"No"| node6{"Is there a value for 'name'?"}
    node5 --> node6
    node6 -->|"Yes"| node7["Update resource 'name'"]
    click node6 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:146:149"
    click node7 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:148:149"
    node6 -->|"No"| node8["Done"]
    node7 --> node8
    node8["Done"]
    click node8 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:150:150"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Evaluate expressions for resource
%% attributes"]
%%     click node1 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:132:150"
%%     node1 --> node2{"Is there a value for 'id'?"}
%%     click node2 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:136:139"
%%     node2 -->|"Yes"| node3["Update resource 'id'"]
%%     click node3 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:138:139"
%%     node2 -->|"No"| node4{"Is there a value for 'input'?"}
%%     node3 --> node4
%%     node4 -->|"Yes"| node5["Update resource 'input'"]
%%     click node4 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:141:144"
%%     click node5 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:143:144"
%%     node4 -->|"No"| node6{"Is there a value for 'name'?"}
%%     node5 --> node6
%%     node6 -->|"Yes"| node7["Update resource 'name'"]
%%     click node6 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:146:149"
%%     click node7 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:148:149"
%%     node6 -->|"No"| node8["Done"]
%%     node7 --> node8
%%     node8["Done"]
%%     click node8 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:150:150"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="132">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="132:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we resolve 'id' and 'input'. When we get to 'input', we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken>, which might throw if config is frozen, so we need to check with <SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="180:14:14" line-data="     * @param actionConfig The {@link ActionConfig} that this config is from,">`ActionConfig`</SwmToken> next.

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

<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="473:5:5" line-data="    public void setInput(String input) {">`setInput`</SwmToken> only updates the input field if the configuration isn't frozen. If it's locked, it throws an exception, so you can't change things after setup.

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

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCancelTag.java" pos="695:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> for the resource tag, after handling 'input' (which might throw if config is frozen), we just set 'name' directly if it's present. No config checks here.

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
