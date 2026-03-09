---
title: Processing and Preparing Tag Attributes
---
This document describes how tag attributes are processed and prepared for use. When a tag is started, attribute expressions are evaluated and validated, ensuring all properties are set correctly before continuing with tag processing.

# Starting tag processing and prepping for expression evaluation

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" line="924">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="924:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we kick off tag processing by evaluating all attribute expressions. This ensures any dynamic values are resolved before moving on to the rest of the tag logic.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

```

---

</SwmSnippet>

## Resolving tag attribute values and enforcing config constraints

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start evaluating tag attribute
expressions"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java:936:1162"
    
    subgraph loop1["For each configurable attribute (e.g.,
accesskey, alt, bundle, dir, disabled,
etc.)"]
        node2{"Does the attribute expression evaluate
to a value?"}
        click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java:941:1161"
        node2 -->|"Yes"| node3["Set attribute on tag with evaluated
value"]
        click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java:944:1161"
        node3 --> node4["Next attribute"]
        click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java:941:1161"
        node2 -->|"No"| node4
    end
    node4 --> node5["All attributes processed - tag is
configured"]
    click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java:1162:1162"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start evaluating tag attribute
%% expressions"]
%%     click node1 openCode "<SwmPath>[el/…/html/ELTextTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java)</SwmPath>:936:1162"
%%     
%%     subgraph loop1["For each configurable attribute (e.g.,
%% accesskey, alt, bundle, dir, disabled,
%% etc.)"]
%%         node2{"Does the attribute expression evaluate
%% to a value?"}
%%         click node2 openCode "<SwmPath>[el/…/html/ELTextTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java)</SwmPath>:941:1161"
%%         node2 -->|"Yes"| node3["Set attribute on tag with evaluated
%% value"]
%%         click node3 openCode "<SwmPath>[el/…/html/ELTextTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java)</SwmPath>:944:1161"
%%         node3 --> node4["Next attribute"]
%%         click node4 openCode "<SwmPath>[el/…/html/ELTextTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java)</SwmPath>:941:1161"
%%         node2 -->|"No"| node4
%%     end
%%     node4 --> node5["All attributes processed - tag is
%% configured"]
%%     click node5 openCode "<SwmPath>[el/…/html/ELTextTag.java](el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java)</SwmPath>:1162:1162"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" line="936">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="936:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we resolve each tag attribute, and when we hit 'bundle', we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="961:1:1" line-data="            setBundle(string);">`setBundle`</SwmToken> to update the config, but only if it's not frozen. This step ensures the bundle value is set before rendering.

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

<SwmToken path="core/src/main/java/org/apache/struts/config/ExceptionConfig.java" pos="89:5:5" line-data="    public void setBundle(String bundle) {">`setBundle`</SwmToken> checks if the config is frozen using the 'configured' flag. If so, it blocks changes by throwing an exception, so the bundle can't be modified after setup.

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

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" line="964">

---

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="925:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken>, after handling bundle, we keep resolving other attributes. When we get to 'size', we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="1126:1:1" line-data="            setSize(string);">`setSize`</SwmToken>, which checks for frozen config and validates the value before updating.

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
                EvalHelper.evalString("errorKey", getErrorKeyExpr(), this,
                    pageContext)) != null) {
            setErrorKey(string);
        }

        if ((string =
                EvalHelper.evalString("errorStyle", getErrorStyleExpr(), this,
                    pageContext)) != null) {
            setErrorStyle(string);
        }

        if ((string =
                EvalHelper.evalString("errorStyleClass",
                    getErrorStyleClassExpr(), this, pageContext)) != null) {
            setErrorStyleClass(string);
        }

        if ((string =
                EvalHelper.evalString("errorStyleId", getErrorStyleIdExpr(),
                    this, pageContext)) != null) {
            setErrorStyleId(string);
        }

        if ((bool =
                EvalHelper.evalBoolean("indexed", getIndexedExpr(), this,
                    pageContext)) != null) {
            setIndexed(bool.booleanValue());
        }

        if ((string =
            	EvalHelper.evalString("lang", getLangExpr(), this,
            		pageContext)) != null) {
        	setLang(string);
        }

        if ((string =
                EvalHelper.evalString("maxlength", getMaxlengthExpr(), this,
                    pageContext)) != null) {
            setMaxlength(string);
        }

        if ((string =
                EvalHelper.evalString("name", getNameExpr(), this, pageContext)) != null) {
            setName(string);
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
                EvalHelper.evalString("onselect", getOnselectExpr(), this,
                    pageContext)) != null) {
            setOnselect(string);
        }

        if ((string =
                EvalHelper.evalString("property", getPropertyExpr(), this,
                    pageContext)) != null) {
            setProperty(string);
        }

        if ((bool =
                EvalHelper.evalBoolean("readonly", getReadonlyExpr(), this,
                    pageContext)) != null) {
            setReadonly(bool.booleanValue());
        }

        if ((string =
                EvalHelper.evalString("style", getStyleExpr(), this, pageContext)) != null) {
            setStyle(string);
        }

        if ((string =
                EvalHelper.evalString("size", getSizeExpr(), this, pageContext)) != null) {
            setSize(string);
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormPropertyConfig.java" line="192">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/FormPropertyConfig.java" pos="192:5:5" line-data="    public void setSize(int size) {">`setSize`</SwmToken> enforces two checks: config can't be changed if frozen, and size must be <SwmToken path="core/src/main/java/org/apache/struts/config/FormPropertyConfig.java" pos="71:7:9" line-data="     * must be non-negative.&lt;/p&gt;">`non-negative`</SwmToken>. If either fails, it throws an exception to keep the config valid.

```java
    public void setSize(int size) {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        if (size < 0) {
            throw new IllegalArgumentException("size < 0");
        }

        this.size = size;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" line="1129">

---

After returning from <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="1126:1:1" line-data="            setSize(string);">`setSize`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="925:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> wraps up by resolving the remaining attributes. At this point, all tag properties are set and validated, ready for the next step.

```java
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

## Delegating to parent tag logic after attribute resolution

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" line="927">

---

After finishing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="925:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="927:6:6" line-data="        return (super.doStartTag());">`doStartTag`</SwmToken> hands off to the parent tag logic. This lets any inherited processing run, which is needed for proper tag behavior.

```java
        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Resource tag processing and prepping for expression evaluation

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="120">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="120:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> starts by evaluating attribute expressions, making sure any dynamic values are set before moving on to the parent tag logic.

```java
    public int doStartTag() throws JspException {
        evaluateExpressions();

        return (super.doStartTag());
    }
```

---

</SwmSnippet>

# Resolving resource tag attributes and enforcing config immutability

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start expression evaluation"]
    click node1 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:132:135"
    node1 --> node2{"Is 'id' expression present?"}
    click node2 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:136:139"
    node2 -->|"Yes"| node3["Set resource id"]
    click node3 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:138:139"
    node2 -->|"No"| node4{"Is 'input' expression present?"}
    node3 --> node4
    node4 -->|"Yes"| node5["Set resource input"]
    click node4 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:141:144"
    click node5 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:143:144"
    node4 -->|"No"| node6{"Is 'name' expression present?"}
    node5 --> node6
    node6 -->|"Yes"| node7["Set resource name"]
    click node6 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:146:149"
    click node7 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:148:149"
    node6 -->|"No"| node8["End"]
    node7 --> node8
    click node8 openCode "el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java:150:150"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start expression evaluation"]
%%     click node1 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:132:135"
%%     node1 --> node2{"Is 'id' expression present?"}
%%     click node2 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:136:139"
%%     node2 -->|"Yes"| node3["Set resource id"]
%%     click node3 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:138:139"
%%     node2 -->|"No"| node4{"Is 'input' expression present?"}
%%     node3 --> node4
%%     node4 -->|"Yes"| node5["Set resource input"]
%%     click node4 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:141:144"
%%     click node5 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:143:144"
%%     node4 -->|"No"| node6{"Is 'name' expression present?"}
%%     node5 --> node6
%%     node6 -->|"Yes"| node7["Set resource name"]
%%     click node6 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:146:149"
%%     click node7 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:148:149"
%%     node6 -->|"No"| node8["End"]
%%     node7 --> node8
%%     click node8 openCode "<SwmPath>[el/…/bean/ELResourceTag.java](el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java)</SwmPath>:150:150"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="132">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="132:5:5" line-data="    private void evaluateExpressions()">`evaluateExpressions`</SwmToken>, we resolve each resource tag attribute, and when we hit 'input', we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken> to update the config, but only if it's not frozen. This step ensures the input value is set before processing.

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

<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="473:5:5" line-data="    public void setInput(String input) {">`setInput`</SwmToken> checks if the config is frozen using the 'configured' flag. If so, it blocks changes by throwing an exception, so the input can't be modified after setup.

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

After returning from <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="143:1:1" line-data="            setInput(string);">`setInput`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELTextTag.java" pos="925:1:1" line-data="        evaluateExpressions();">`evaluateExpressions`</SwmToken> finishes up by resolving the remaining attributes. At this point, all resource tag properties are set and validated, ready for the next step.

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
