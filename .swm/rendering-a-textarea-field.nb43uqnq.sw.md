---
title: Rendering a Textarea Field
---
This document explains how a textarea field is generated for a web form, including the assembly of its attributes, styles, and content. The resulting textarea reflects the current data and validation state, providing users with an accurate and interactive input field.

```mermaid
flowchart TD
  node1["Rendering the textarea tag start"]:::HeadingStyle
  click node1 goToHeading "Rendering the textarea tag start"
  node1 --> node2["Building textarea attributes"]:::HeadingStyle
  click node2 goToHeading "Building textarea attributes"
  node2 --> node3["Applying styles to the textarea"]:::HeadingStyle
  click node3 goToHeading "Applying styles to the textarea"
  node3 --> node4["Completing textarea markup"]:::HeadingStyle
  click node4 goToHeading "Completing textarea markup"
  node4 --> node5["Populating textarea content"]:::HeadingStyle
  click node5 goToHeading "Populating textarea content"
  node5 --> node6["Finalizing textarea output"]:::HeadingStyle
  click node6 goToHeading "Finalizing textarea output"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Rendering the textarea tag start

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="47">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="47:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken> writes the textarea HTML to the page by calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken>, then signals to evaluate the tag body. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken> to generate the actual markup for the textarea, which is needed for output.

```java
    public int doStartTag() throws JspException {
        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());

        return (EVAL_BODY_TAG);
    }
```

---

</SwmSnippet>

# Building textarea attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start building textarea element"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:59:67"
    node1 --> node2["Add core attributes (name, access key,
tab index, columns, rows)"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:59:67"
    node2 --> node3["Adding event and state handlers"]
    
    node3 --> node4["Determining style attributes"]
    
    node4 --> node5["Add other attributes"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:70:71"
    node5 --> node6["Populating textarea content"]
    
    node6 --> node7["Retrieving bean property value"]
    
    node7 --> node8["Return complete textarea markup"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:75:78"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Adding event and state handlers"
node3:::HeadingStyle
click node4 goToHeading "Determining style attributes"
node4:::HeadingStyle
click node6 goToHeading "Populating textarea content"
node6:::HeadingStyle
click node7 goToHeading "Retrieving bean property value"
node7:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start building textarea element"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:59:67"
%%     node1 --> node2["Add core attributes (name, access key,
%% tab index, columns, rows)"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:59:67"
%%     node2 --> node3["Adding event and state handlers"]
%%     
%%     node3 --> node4["Determining style attributes"]
%%     
%%     node4 --> node5["Add other attributes"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:70:71"
%%     node5 --> node6["Populating textarea content"]
%%     
%%     node6 --> node7["Retrieving bean property value"]
%%     
%%     node7 --> node8["Return complete textarea markup"]
%%     click node8 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:75:78"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Adding event and state handlers"
%% node3:::HeadingStyle
%% click node4 goToHeading "Determining style attributes"
%% node4:::HeadingStyle
%% click node6 goToHeading "Populating textarea content"
%% node6:::HeadingStyle
%% click node7 goToHeading "Retrieving bean property value"
%% node7:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="59">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="59:5:5" line-data="    protected String renderTextareaElement()">`renderTextareaElement`</SwmToken>, we start assembling the textarea tag and call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="63:1:1" line-data="        prepareAttribute(results, &quot;name&quot;, prepareName());">`prepareAttribute`</SwmToken> for each attribute. We use LabelTag.prepareAttribute next because it handles special cases like required styling for the 'class' attribute.

```java
    protected String renderTextareaElement()
        throws JspException {
        StringBuffer results = new StringBuffer("<textarea");

        prepareAttribute(results, "name", prepareName());
        prepareAttribute(results, "accesskey", getAccesskey());
        prepareAttribute(results, "tabindex", getTabindex());
        prepareAttribute(results, "cols", getCols());
        prepareAttribute(results, "rows", getRows());
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" line="161">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/LabelTag.java" pos="161:5:5" line-data="    protected void prepareAttribute(StringBuffer handlers, String name,">`prepareAttribute`</SwmToken> checks if the attribute is 'class' and the field is required, then appends a required style class. After that, it delegates to the superclass to handle the actual attribute formatting.

```java
    protected void prepareAttribute(StringBuffer handlers, String name,
            Object value) {

        if ("class".equals(name) && this.required) {
            String requiredStyleClass = getRequiredStyleClass();
            if (requiredStyleClass != null) {
                value = (value != null) ? (value + " " + requiredStyleClass)
                        : requiredStyleClass;
            }
        }
        super.prepareAttribute(handlers, name, value);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="68">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken>, after setting up attributes, we append event handlers by calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="68:5:5" line-data="        results.append(prepareEventHandlers());">`prepareEventHandlers`</SwmToken> from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="48:6:6" line-data="public abstract class BaseHandlerTag extends BodyTagSupport {">`BaseHandlerTag`</SwmToken>. This adds interaction hooks to the textarea.

```java
        results.append(prepareEventHandlers());
```

---

</SwmSnippet>

## Adding event and state handlers

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Assemble event handler attributes
for HTML element"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1039:1040"
    node1 --> node2["Add mouse event handlers (enables mouse
interactions)"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1042:1042"
    node2 --> node3["Add keyboard event handlers (enables
keyboard interactions)"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1043:1043"
    node3 --> node4["Add text event handlers (enables text
input interactions)"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1044:1044"
    node4 --> node5["Add focus event handlers (enables
focus/blur interactions)"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1045:1045"
    node5 --> node6["Return complete set of event handler
attributes as a string for the HTML
element"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1047:1048"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Assemble event handler attributes
%% for HTML element"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1039:1040"
%%     node1 --> node2["Add mouse event handlers (enables mouse
%% interactions)"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1042:1042"
%%     node2 --> node3["Add keyboard event handlers (enables
%% keyboard interactions)"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1043:1043"
%%     node3 --> node4["Add text event handlers (enables text
%% input interactions)"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1044:1044"
%%     node4 --> node5["Add focus event handlers (enables
%% focus/blur interactions)"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1045:1045"
%%     node5 --> node6["Return complete set of event handler
%% attributes as a string for the HTML
%% element"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1047:1048"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1039">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1039:5:5" line-data="    protected String prepareEventHandlers() {">`prepareEventHandlers`</SwmToken> builds up all event-related attributes for the textarea, including mouse, key, text, and focus events. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1045:1:1" line-data="        prepareFocusEvents(handlers);">`prepareFocusEvents`</SwmToken> next to handle focus-specific logic and state attributes.

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

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1095:5:5" line-data="    protected void prepareFocusEvents(StringBuffer handlers) {">`prepareFocusEvents`</SwmToken> not only sets up onblur and onfocus handlers, but also checks if the textarea or its parent form is disabled or readonly, then adds those attributes if needed.

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

## Applying styles to the textarea

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="69">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken>, after event handlers, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="69:5:5" line-data="        results.append(prepareStyles());">`prepareStyles`</SwmToken> to add style-related attributes. This handles error styling and normal styling for the textarea.

```java
        results.append(prepareStyles());
```

---

</SwmSnippet>

## Determining style attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start preparing styles"] --> node2["Checking for validation errors"]
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:967:971"
  
  node2 --> node3{"Are there validation errors?"}
  node3 -->|"Yes"| node4["Apply error-specific id, class, and
style"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:973:989"
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:973:989"
  node3 -->|"No"| node4
  node4 --> node5["Return the complete style string for the
element"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:993:996"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Checking for validation errors"
node2:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start preparing styles"] --> node2["Checking for validation errors"]
%%   click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:967:971"
%%   
%%   node2 --> node3{"Are there validation errors?"}
%%   node3 -->|"Yes"| node4["Apply error-specific id, class, and
%% style"]
%%   click node3 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:973:989"
%%   click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:973:989"
%%   node3 -->|"No"| node4
%%   node4 --> node5["Return the complete style string for the
%% element"]
%%   click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:993:996"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Checking for validation errors"
%% node2:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="967">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="967:5:5" line-data="    protected String prepareStyles()">`prepareStyles`</SwmToken>, we start by checking if there are validation errors using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="971:7:7" line-data="        boolean errorsExist = doErrorsExist();">`doErrorsExist`</SwmToken>. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="971:7:7" line-data="        boolean errorsExist = doErrorsExist();">`doErrorsExist`</SwmToken> next to decide if error styles should be applied.

```java
    protected String prepareStyles()
        throws JspException {
        StringBuffer styles = new StringBuffer();

        boolean errorsExist = doErrorsExist();

```

---

</SwmSnippet>

### Checking for validation errors

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is error display style configured?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1007:1008"
    node1 -->|"No"| node4["No errors will be shown"]
    node1 -->|"Yes"| node2{"Is there a valid field name?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1011:1011"
    node2 -->|"No"| node4
    node2 -->|"Yes"| node3{"Are there errors for this field?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1016:1017"
    node3 -->|"Yes"| node5["Errors exist for this field"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1016:1017"
    node3 -->|"No"| node4
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1021:1021"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is error display style configured?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1007:1008"
%%     node1 -->|"No"| node4["No errors will be shown"]
%%     node1 -->|"Yes"| node2{"Is there a valid field name?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1011:1011"
%%     node2 -->|"No"| node4
%%     node2 -->|"Yes"| node3{"Are there errors for this field?"}
%%     click node3 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1016:1017"
%%     node3 -->|"Yes"| node5["Errors exist for this field"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1016:1017"
%%     node3 -->|"No"| node4
%%     click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1021:1021"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1003">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1003:5:5" line-data="    protected boolean doErrorsExist()">`doErrorsExist`</SwmToken> looks for error style attributes and then calls TagUtils.getActionMessages to check if there are validation messages for this field.

```java
    protected boolean doErrorsExist()
        throws JspException {
        boolean errorsExist = false;

        if ((getErrorStyleId() != null) || (getErrorStyle() != null)
            || (getErrorStyleClass() != null)) {
            String actualName = prepareName();

            if (actualName != null) {
                ActionMessages errors =
                    TagUtils.getInstance().getActionMessages(pageContext,
                        errorKey);

                errorsExist = ((errors != null)
                    && (errors.size(actualName) > 0));
            }
        }

        return errorsExist;
    }
```

---

</SwmSnippet>

### Aggregating error messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Look up parameter value in page context"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:731:731"
    node1 --> node2{"Is there a value?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:733:733"
    node2 -->|"No"| node7["Return no messages"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:763:763"
    node2 -->|"Yes"| node3{"Type of value?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:735:752"
    node3 -->|"Single message"| node4["Add message to messages"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:736:737"
    node3 -->|"List of messages"| loop1
    node3 -->|ActionErrors| node10["Add messages from errors"]
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:748:748"
    node3 -->|ActionMessages| node6["Use these messages"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:750:750"
    node3 -->|"Other type"| node9["Raise error"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:752:754"
    subgraph loop1["For each message in list"]
      node5["Add message to messages"]
      click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:742:743"
    end
    node4 --> node8["Return messages"]
    loop1 --> node8
    node6 --> node8
    node7 --> node8
    node10 --> node8
    node9 --> node8
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:763:763"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Look up parameter value in page context"]
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:731:731"
%%     node1 --> node2{"Is there a value?"}
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:733:733"
%%     node2 -->|"No"| node7["Return no messages"]
%%     click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:763:763"
%%     node2 -->|"Yes"| node3{"Type of value?"}
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:735:752"
%%     node3 -->|"Single message"| node4["Add message to messages"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:736:737"
%%     node3 -->|"List of messages"| loop1
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="745:12:12" line-data="                } else if (value instanceof ActionErrors) {">`ActionErrors`</SwmToken>| node10["Add messages from errors"]
%%     click node10 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:748:748"
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1012:1:1" line-data="                ActionMessages errors =">`ActionMessages`</SwmToken>| node6["Use these messages"]
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:750:750"
%%     node3 -->|"Other type"| node9["Raise error"]
%%     click node9 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:752:754"
%%     subgraph loop1["For each message in list"]
%%       node5["Add message to messages"]
%%       click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:742:743"
%%     end
%%     node4 --> node8["Return messages"]
%%     loop1 --> node8
%%     node6 --> node8
%%     node7 --> node8
%%     node10 --> node8
%%     node9 --> node8
%%     click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:763:763"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="727">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="727:5:5" line-data="    public ActionMessages getActionMessages(PageContext pageContext,">`getActionMessages`</SwmToken>, we grab the error attribute from the page context, convert it to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="727:3:3" line-data="    public ActionMessages getActionMessages(PageContext pageContext,">`ActionMessages`</SwmToken> no matter its original type, so error handling is uniform.

```java
    public ActionMessages getActionMessages(PageContext pageContext,
        String paramName) throws JspException {
        ActionMessages am = new ActionMessages();

        Object value = pageContext.findAttribute(paramName);

        if (value != null) {
            try {
                if (value instanceof String) {
                    am.add(ActionMessages.GLOBAL_MESSAGE,
                        new ActionMessage((String) value));
                } else if (value instanceof String[]) {
                    String[] keys = (String[]) value;

                    for (int i = 0; i < keys.length; i++) {
                        am.add(ActionMessages.GLOBAL_MESSAGE,
                            new ActionMessage(keys[i]));
                    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="745">

---

After processing, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1013:7:7" line-data="                    TagUtils.getInstance().getActionMessages(pageContext,">`getActionMessages`</SwmToken> returns an <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="746:1:1" line-data="                    ActionMessages m = (ActionMessages) value;">`ActionMessages`</SwmToken> object with all error messages, regardless of the original attribute type.

```java
                } else if (value instanceof ActionErrors) {
                    ActionMessages m = (ActionMessages) value;

                    am.add(m);
                } else if (value instanceof ActionMessages) {
                    am = (ActionMessages) value;
                } else {
                    throw new JspException(messages.getMessage(
                            "actionMessages.errors", value.getClass().getName()));
                }
            } catch (JspException e) {
                throw e;
            } catch (Exception e) {
                log.warn("Unable to retieve ActionMessage for paramName : "
                    + paramName, e);
            }
        }

        return am;
    }
```

---

</SwmSnippet>

### Finalizing style and accessibility attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node2{"Are there validation errors?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:973:973"
    subgraph group1["For each attribute: id, style, class"]
        node2 -->|"Yes"| node3{"Is error value present?"}
        node3 -->|"Yes"| node4["Use error value for attribute"]
        node3 -->|"No"| node5["Use normal value for attribute"]
        node4 --> node6["Next attribute"]
        node5 --> node6
        node6 --> node3
    end
    node2 -->|"No"| node7["Use normal values for all attributes"]
    group1 --> node8["Set title and alt attributes (with
internationalization)"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:991:992"
    node8 --> node9["Prepare internationalization
attributes"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:993:993"
    node9 --> node10["Return final style attributes"]
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:995:995"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Are there validation errors?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:973:973"
%%     subgraph group1["For each attribute: id, style, class"]
%%         node2 -->|"Yes"| node3{"Is error value present?"}
%%         node3 -->|"Yes"| node4["Use error value for attribute"]
%%         node3 -->|"No"| node5["Use normal value for attribute"]
%%         node4 --> node6["Next attribute"]
%%         node5 --> node6
%%         node6 --> node3
%%     end
%%     node2 -->|"No"| node7["Use normal values for all attributes"]
%%     group1 --> node8["Set title and alt attributes (with
%% internationalization)"]
%%     click node8 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:991:992"
%%     node8 --> node9["Prepare internationalization
%% attributes"]
%%     click node9 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:993:993"
%%     node9 --> node10["Return final style attributes"]
%%     click node10 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:995:995"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="973">

---

After error checks in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="69:5:5" line-data="        results.append(prepareStyles());">`prepareStyles`</SwmToken>, we set id, style, and class attributes based on error state, then use message() for localized title and alt values.

```java
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

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="830:5:5" line-data="    protected String message(String literal, String key)">`message`</SwmToken> checks if both literal and key are set—throws if so. Otherwise, it returns the literal or fetches a localized string by key.

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

After setting all style and accessibility attributes in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="69:5:5" line-data="        results.append(prepareStyles());">`prepareStyles`</SwmToken>, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="993:1:1" line-data="        prepareInternationalization(styles);">`prepareInternationalization`</SwmToken> to add any i18n-specific attributes before returning the style string.

```java
        prepareInternationalization(styles);

        return styles.toString();
    }
```

---

</SwmSnippet>

## Completing textarea markup

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="70">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken>, after all attributes are set, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="73:7:7" line-data="        results.append(this.renderData());">`renderData`</SwmToken> to fill the textarea with its value or property lookup.

```java
        prepareOtherAttributes(results);
        results.append(">");

        results.append(this.renderData());

```

---

</SwmSnippet>

## Populating textarea content

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node2{"Is preset value present?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:90:91"
    node2 -->|"Yes"| node5["Filter and return preset value"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:94:94"
    node2 -->|"No"| node3["Retrieve value from data source"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:91:91"
    node3 --> node6{"Is data source value present?"}
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:94:94"
    node6 -->|"Yes"| node7["Filter and return data source value"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:94:94"
    node6 -->|"No"| node4["Return empty string"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java:94:94"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Is preset value present?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:90:91"
%%     node2 -->|"Yes"| node5["Filter and return preset value"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:94:94"
%%     node2 -->|"No"| node3["Retrieve value from data source"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:91:91"
%%     node3 --> node6{"Is data source value present?"}
%%     click node6 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:94:94"
%%     node6 -->|"Yes"| node7["Filter and return data source value"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:94:94"
%%     node6 -->|"No"| node4["Return empty string"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/TextareaTag.java](taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java)</SwmPath>:94:94"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="86">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="86:5:5" line-data="    protected String renderData()">`renderData`</SwmToken> uses the value if set, otherwise calls <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="91:7:7" line-data="            data = this.lookupProperty(this.name, this.property);">`lookupProperty`</SwmToken> to fetch it from the bean, then filters it for HTML output.

```java
    protected String renderData()
        throws JspException {
        String data = this.value;

        if (data == null) {
            data = this.lookupProperty(this.name, this.property);
        }

        return (data == null) ? "" : TagUtils.getInstance().filter(data);
    }
```

---

</SwmSnippet>

## Retrieving bean property value

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1200">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1200:5:5" line-data="    protected String lookupProperty(String beanName, String property)">`lookupProperty`</SwmToken>, we use TagUtils.lookup to fetch the bean from the page context, handling scope resolution before property access.

```java
    protected String lookupProperty(String beanName, String property)
        throws JspException {
        Object bean =
            TagUtils.getInstance().lookup(this.pageContext, beanName, null);

```

---

</SwmSnippet>

### Resolving bean scope

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is a specific scope provided?"}
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:865:865"
  node1 -->|"No"| node2["Find value by name in all available
scopes using pageContext"]
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:866:866"
  node1 -->|"Yes"| node3["Try to find value by name in specified
scope using pageContext"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:870:870"
  node3 -->|"Success"| node4["Return value from specified scope"]
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:870:870"
  node3 -->|"Error (invalid scope)"| node5["Save error and throw exception"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:872:873"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is a specific scope provided?"}
%%   click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:865:865"
%%   node1 -->|"No"| node2["Find value by name in all available
%% scopes using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:9:9" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`pageContext`</SwmToken>"]
%%   click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:866:866"
%%   node1 -->|"Yes"| node3["Try to find value by name in specified
%% scope using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:9:9" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`pageContext`</SwmToken>"]
%%   click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:870:870"
%%   node3 -->|"Success"| node4["Return value from specified scope"]
%%   click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:870:870"
%%   node3 -->|"Error (invalid scope)"| node5["Save error and throw exception"]
%%   click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:872:873"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="863">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:5:5" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`lookup`</SwmToken> checks if <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:19:19" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`scopeName`</SwmToken> is null—if so, it searches all scopes for the bean. Otherwise, it converts <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="863:19:19" line-data="    public Object lookup(PageContext pageContext, String name, String scopeName)">`scopeName`</SwmToken> to an integer and fetches from the specified scope.

```java
    public Object lookup(PageContext pageContext, String name, String scopeName)
        throws JspException {
        if (scopeName == null) {
            return pageContext.findAttribute(name);
        }

        try {
            return pageContext.getAttribute(name, instance.getScope(scopeName));
        } catch (JspException e) {
            saveException(pageContext, e);
            throw e;
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="809">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="809:5:5" line-data="    public int getScope(String scopeName)">`getScope`</SwmToken> lowercases <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="809:9:9" line-data="    public int getScope(String scopeName)">`scopeName`</SwmToken> before lookup, so all keys match. If the scope isn't found, it throws an exception with a message (even though scope is null in the message).

```java
    public int getScope(String scopeName)
        throws JspException {
        Integer scope = (Integer) scopes.get(scopeName.toLowerCase());

        if (scope == null) {
            throw new JspException(messages.getMessage("lookup.scope", scope));
        }

        return scope.intValue();
    }
```

---

</SwmSnippet>

### Handling property access and errors

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is the data object (bean) available?"}
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1205:1207"
  node1 -->|"No"| node2["Report error: Object not found
(beanName)"]
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1206:1207"
  node1 -->|"Yes"| node3["Attempt to retrieve property value from
bean"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1209:1210"
  node3 --> node4{"Which error occurred during property
access?"}
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1211:1222"
  node4 -->|"No error"| node5["Return property value"]
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1210:1210"
  node4 -->|"Access denied"| node6["Report error: Access denied (property,
beanName)"]
  click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1211:1213"
  node4 -->|"Invocation error"| node7["Report error: Invocation error
(property, error details)"]
  click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1214:1218"
  node4 -->|"Method not found"| node8["Report error: Method not found
(property, beanName)"]
  click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java:1219:1221"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is the data object (bean) available?"}
%%   click node1 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1205:1207"
%%   node1 -->|"No"| node2["Report error: Object not found
%% (<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1200:9:9" line-data="    protected String lookupProperty(String beanName, String property)">`beanName`</SwmToken>)"]
%%   click node2 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1206:1207"
%%   node1 -->|"Yes"| node3["Attempt to retrieve property value from
%% bean"]
%%   click node3 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1209:1210"
%%   node3 --> node4{"Which error occurred during property
%% access?"}
%%   click node4 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1211:1222"
%%   node4 -->|"No error"| node5["Return property value"]
%%   click node5 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1210:1210"
%%   node4 -->|"Access denied"| node6["Report error: Access denied (property,
%% <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1200:9:9" line-data="    protected String lookupProperty(String beanName, String property)">`beanName`</SwmToken>)"]
%%   click node6 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1211:1213"
%%   node4 -->|"Invocation error"| node7["Report error: Invocation error
%% (property, error details)"]
%%   click node7 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1214:1218"
%%   node4 -->|"Method not found"| node8["Report error: Method not found
%% (property, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1200:9:9" line-data="    protected String lookupProperty(String beanName, String property)">`beanName`</SwmToken>)"]
%%   click node8 openCode "<SwmPath>[taglib/…/html/BaseHandlerTag.java](taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java)</SwmPath>:1219:1221"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" line="1205">

---

After bean lookup in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="91:7:7" line-data="            data = this.lookupProperty(this.name, this.property);">`lookupProperty`</SwmToken>, if the bean is missing or property access fails, we throw a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/BaseHandlerTag.java" pos="1206:5:5" line-data="            throw new JspException(messages.getMessage(&quot;getter.bean&quot;, beanName));">`JspException`</SwmToken>—so the textarea won't show invalid data.

```java
        if (bean == null) {
            throw new JspException(messages.getMessage("getter.bean", beanName));
        }

        try {
            return BeanUtils.getProperty(bean, property);
        } catch (IllegalAccessException e) {
            throw new JspException(messages.getMessage("getter.access",
                    property, beanName), e);
        } catch (InvocationTargetException e) {
            Throwable t = e.getTargetException();

            throw new JspException(messages.getMessage("getter.result",
                    property, t.toString()), e);
        } catch (NoSuchMethodException e) {
            throw new JspException(messages.getMessage("getter.method",
                    property, beanName), e);
        }
    }
```

---

</SwmSnippet>

## Finalizing textarea output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" line="75">

---

After getting the textarea content from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="73:7:7" line-data="        results.append(this.renderData());">`renderData`</SwmToken>, we append the closing </textarea> tag and return the full HTML string from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/TextareaTag.java" pos="48:14:14" line-data="        TagUtils.getInstance().write(pageContext, this.renderTextareaElement());">`renderTextareaElement`</SwmToken>.

```java
        results.append("</textarea>");

        return results.toString();
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
