---
title: Rendering a Form Label
---
This document explains how a label element is rendered for a form field, ensuring accessibility, styling, and localization. The input is the data and attributes for the label, and the output is a fully rendered label element in the HTML output.

# Building the label element

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Add accessibility and association attributes to label"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java:117:125"
    node1 --> node2["Styling and message resolution"]
    
    node2 --> node3["Event and state attribute assembly"]
    
    node3 --> node4["Set the visible label text"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java:127:133"
    node4 --> node5["Render the complete label on the page"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java:134:139"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Styling and message resolution"
node2:::HeadingStyle
click node3 goToHeading "Event and state attribute assembly"
node3:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Add accessibility and association attributes to label"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/LabelTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java)</SwmPath>:117:125"
%%     node1 --> node2["Styling and message resolution"]
%%     
%%     node2 --> node3["Event and state attribute assembly"]
%%     
%%     node3 --> node4["Set the visible label text"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/LabelTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java)</SwmPath>:127:133"
%%     node4 --> node5["Render the complete label on the page"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/LabelTag.java](taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java)</SwmPath>:134:139"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Styling and message resolution"
%% node2:::HeadingStyle
%% click node3 goToHeading "Event and state attribute assembly"
%% node3:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" line="117">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" pos="117:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, we start assembling the <label> element and its basic attributes. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="48:6:6" line-data="public abstract class BaseHandlerTag extends BodyTagSupport {">`BaseHandlerTag`</SwmToken> next to handle style and error-related attributes, since those aren't managed here.

```java
    public int doEndTag() throws JspException {
        // Generate the opening element
        StringBuffer results = new StringBuffer("<label");
        prepareAttribute(results, "accesskey", getAccesskey());
        prepareAttribute(results, "for", getForId() != null ? getForId()
                : prepareName());
        prepareAttribute(results, "tabindex", getTabindex());
        prepareAttribute(results, "title", getTitle());
        results.append(prepareStyles());
```

---

</SwmSnippet>

## Styling and message resolution

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Prepare styles for element"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:967:968"
    node1 --> node2{"Are there errors?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:971:971"
    subgraph group1["For each style attribute (id, style, class)"]
      node2 -->|"Yes"| node3{"Is error-specific value present?"}
      node3 -->|"Yes"| node4["Apply error-specific value"]
      click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:973:987"
      node3 -->|"No"| node5["Apply default value"]
      click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:976:989"
    end
    node2 -->|"No"| node6["Apply default values for all attributes"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:976:989"
    node4 --> node7["Set title and alt attributes"]
    node5 --> node7
    node6 --> node7
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:991:992"
    node7 --> node8["Prepare internationalization"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:993:993"
    node8 --> node9["Return composed styles"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:995:996"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Prepare styles for element"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:967:968"
%%     node1 --> node2{"Are there errors?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:971:971"
%%     subgraph group1["For each style attribute (id, style, class)"]
%%       node2 -->|"Yes"| node3{"Is error-specific value present?"}
%%       node3 -->|"Yes"| node4["Apply error-specific value"]
%%       click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:973:987"
%%       node3 -->|"No"| node5["Apply default value"]
%%       click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:976:989"
%%     end
%%     node2 -->|"No"| node6["Apply default values for all attributes"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:976:989"
%%     node4 --> node7["Set title and alt attributes"]
%%     node5 --> node7
%%     node6 --> node7
%%     click node7 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:991:992"
%%     node7 --> node8["Prepare internationalization"]
%%     click node8 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:993:993"
%%     node8 --> node9["Return composed styles"]
%%     click node9 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:995:996"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="967">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="967:5:5" line-data="    protected String prepareStyles()">`prepareStyles`</SwmToken>, we decide which style, id, and class attributes to use based on error state, and resolve title and alt text using the message function for localization. This keeps error handling and localization separate from the tag logic.

```java
    protected String prepareStyles()
        throws JspException {
        StringBuffer styles = new StringBuffer();

        boolean errorsExist = doErrorsExist();

        if (errorsExist && (getErrorStyleId() != null)) {
            prepareAttribute(styles, "id", getErrorStyleId());
        } else {
            prepareAttribute(styles, "id", getStyleId());
        }

        if (errorsExist && (getErrorStyle() != null)) {
            prepareAttribute(styles, "style", getErrorStyle());
        } else {
            prepareAttribute(styles, "style", getStyle());
        }

        if (errorsExist && (getErrorStyleClass() != null)) {
            prepareAttribute(styles, "class", getErrorStyleClass());
        } else {
            prepareAttribute(styles, "class", getStyleClass());
        }

        prepareAttribute(styles, "title", message(getTitle(), getTitleKey()));
        prepareAttribute(styles, "alt", message(getAlt(), getAltKey()));
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="830">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="830:5:5" line-data="    protected String message(String literal, String key)">`message`</SwmToken> enforces that only one of 'literal' or 'key' is set, retrieves localized text if 'key' is used, returns the literal if provided, or null if neither. This prevents ambiguous message resolution.

```java
    protected String message(String literal, String key)
        throws JspException {
        if (literal != null) {
            if (key != null) {
                JspException e =
                    new JspException(messages.getMessage("common.both"));

                TagUtils.getInstance().saveException(pageContext, e);
                throw e;
            } else {
                return (literal);
            }
        } else {
            if (key != null) {
                return TagUtils.getInstance().message(pageContext, getBundle(),
                    getLocale(), key);
            } else {
                return null;
            }
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="993">

---

After returning from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" pos="125:5:5" line-data="        results.append(prepareStyles());">`prepareStyles`</SwmToken>, we append internationalization attributes and return the full styles string, so the caller gets all style and locale-specific markup.

```java
        prepareInternationalization(styles);

        return styles.toString();
    }
```

---

</SwmSnippet>

## Adding event handlers

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" line="126">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" pos="117:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, after getting styles, we append event handlers to the label. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="48:6:6" line-data="public abstract class BaseHandlerTag extends BodyTagSupport {">`BaseHandlerTag`</SwmToken> again to centralize event logic and keep the tag code clean.

```java
        results.append(prepareEventHandlers());
```

---

</SwmSnippet>

## Event and state attribute assembly

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start: Begin with empty event handlers for the UI component"]
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1040:1040"
  node1 --> node2["Add mouse event handlers to handlers"]
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1042:1042"
  node2 --> node3["Add keyboard event handlers to handlers"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1043:1043"
  node3 --> node4["Add text event handlers to handlers"]
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1044:1044"
  node4 --> node5["Add focus event handlers to handlers"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1045:1045"
  node5 --> node6["Return all combined event handlers as a string"]
  click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1047:1047"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start: Begin with empty event handlers for the UI component"]
%%   click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1040:1040"
%%   node1 --> node2["Add mouse event handlers to handlers"]
%%   click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1042:1042"
%%   node2 --> node3["Add keyboard event handlers to handlers"]
%%   click node3 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1043:1043"
%%   node3 --> node4["Add text event handlers to handlers"]
%%   click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1044:1044"
%%   node4 --> node5["Add focus event handlers to handlers"]
%%   click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1045:1045"
%%   node5 --> node6["Return all combined event handlers as a string"]
%%   click node6 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1047:1047"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1039">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1039:5:5" line-data="    protected String prepareEventHandlers() {">`prepareEventHandlers`</SwmToken> collects all event attributes and calls <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1045:1:1" line-data="        prepareFocusEvents(handlers);">`prepareFocusEvents`</SwmToken> to add focus events and set disabled/readonly if needed, so the label's markup matches the UI state.

```java
    protected String prepareEventHandlers() {
        StringBuffer handlers = new StringBuffer();

        prepareMouseEvents(handlers);
        prepareKeyEvents(handlers);
        prepareTextEvents(handlers);
        prepareFocusEvents(handlers);

        return handlers.toString();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1095">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1095:5:5" line-data="    protected void prepareFocusEvents(StringBuffer handlers) {">`prepareFocusEvents`</SwmToken> adds focus event handlers and checks both tag and form state to append disabled/readonly attributes, so the label's markup reflects the form's UI state.

```java
    protected void prepareFocusEvents(StringBuffer handlers) {
        prepareAttribute(handlers, "onblur", getOnblur());
        prepareAttribute(handlers, "onfocus", getOnfocus());

        // Get the parent FormTag (if necessary)
        FormTag formTag = null;

        if ((doDisabled && !getDisabled()) || (doReadonly && !getReadonly())) {
            formTag =
                (FormTag) pageContext.getAttribute(Constants.FORM_KEY,
                    PageContext.REQUEST_SCOPE);
        }

        // Format Disabled
        if (doDisabled) {
            boolean formDisabled =
                (formTag == null) ? false : formTag.isDisabled();

            if (formDisabled || getDisabled()) {
                handlers.append(" disabled=\"disabled\"");
            }
        }

        // Format Read Only
        if (doReadonly) {
            boolean formReadOnly =
                (formTag == null) ? false : formTag.isReadonly();

            if (formReadOnly || getReadonly()) {
                handlers.append(" readonly=\"readonly\"");
            }
        }
    }
```

---

</SwmSnippet>

## Finalizing label markup

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" line="127">

---

After returning from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="48:6:6" line-data="public abstract class BaseHandlerTag extends BodyTagSupport {">`BaseHandlerTag`</SwmToken>, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" pos="117:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken> finishes the label markup by adding focus events, other attributes, resolving the label value, and writing the final HTML to the page.

```java
        prepareFocusEvents(results);
        prepareOtherAttributes(results);
        results.append(">");

        // Prepare the label value
        this.value = message(this.text, this.key);
        prepareValue(results);

        // End tag
        results.append("</label>");
        TagUtils.getInstance().write(this.pageContext, results.toString());

        return (EVAL_PAGE);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
