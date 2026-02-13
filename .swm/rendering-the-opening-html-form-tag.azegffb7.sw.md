---
title: Rendering the Opening HTML Form Tag
---
This document outlines how the application renders the opening HTML form tag, sets up required attributes, and ensures security with a CSRF token. The process prepares the form to capture user input and makes it available for further processing.

# Starting the form rendering

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="483">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="483:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we start by resolving the form bean and prepping the buffer for the HTML form. Calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="493:7:7" line-data="        results.append(this.renderFormStartElement());">`renderFormStartElement`</SwmToken> right after sets up the actual form tag, which is needed before anything else gets rendered.

```java
    public int doStartTag() throws JspException {

        postbackAction = null;

        // Look up the form bean name, scope, and type if necessary
        this.lookup();

        // Create an appropriate "form" element based on our parameters
        StringBuffer results = new StringBuffer();

        results.append(this.renderFormStartElement());

```

---

</SwmSnippet>

## Building the form tag and attributes

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start form tag generation"] --> node2{"Is XHTML mode?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:553:554"
    node2 -->|"Yes"| node3["Render form id attribute (XHTML)"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:590:596"
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:591:595"
    node2 -->|"No"| node4["Render form name and id attributes (HTML)"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:597:599"
    node3 --> node5["Render method, action, class, and other attributes"]
    node4 --> node5
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:560:571"
    node5 --> node6{"Is NOT XHTML mode?"}
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:572:574"
    node6 -->|"Yes (HTML)"| node7["Render autocomplete attribute"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:573:574"
    node7 --> node8["Render custom attributes"]
    node6 -->|"No (XHTML)"| node8["Render custom attributes"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:577:578"
    node8 --> node9["Finish and return form start tag"]
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:579:581"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start form tag generation"] --> node2{"Is XHTML mode?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:553:554"
%%     node2 -->|"Yes"| node3["Render form id attribute (XHTML)"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:590:596"
%%     click node3 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:591:595"
%%     node2 -->|"No"| node4["Render form name and id attributes (HTML)"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:597:599"
%%     node3 --> node5["Render method, action, class, and other attributes"]
%%     node4 --> node5
%%     click node5 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:560:571"
%%     node5 --> node6{"Is NOT XHTML mode?"}
%%     click node6 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:572:574"
%%     node6 -->|"Yes (HTML)"| node7["Render autocomplete attribute"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:573:574"
%%     node7 --> node8["Render custom attributes"]
%%     node6 -->|"No (XHTML)"| node8["Render custom attributes"]
%%     click node8 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:577:578"
%%     node8 --> node9["Finish and return form start tag"]
%%     click node9 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:579:581"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="553">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="553:5:5" line-data="    protected String renderFormStartElement()">`renderFormStartElement`</SwmToken>, we start building the form tag and immediately call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="558:1:1" line-data="        renderName(results);">`renderName`</SwmToken> to add the main identifiers. This sets up the tag for the rest of the attributes to be added cleanly.

```java
    protected String renderFormStartElement()
        throws JspException {
        StringBuffer results = new StringBuffer("<form");

        // render attributes
        renderName(results);

```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="588">

---

RenderName handles adding 'name' and 'id' attributes, but applies strict rules for XHTML output—throwing an exception if a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="154:5:5" line-data="    protected String styleId = null;">`styleId`</SwmToken> is set, to make sure developers don't misuse the tag. It uses internal helpers for format checks and localization.

```java
    protected void renderName(StringBuffer results)
        throws JspException {
        if (this.isXhtml()) {
            if (getStyleId() == null) {
                renderAttribute(results, "id", beanName);
            } else {
                throw new JspException(messages.getMessage("formTag.ignoredId"));
            }
        } else {
            renderAttribute(results, "name", beanName);
            renderAttribute(results, "id", getStyleId());
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="560">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="493:7:7" line-data="        results.append(this.renderFormStartElement());">`renderFormStartElement`</SwmToken>, after <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="558:1:1" line-data="        renderName(results);">`renderName`</SwmToken> sets up the identifiers, we add all the other form attributes, including method, action, and styling. The tag is finalized and returned as a string.

```java
        renderAttribute(results, "method",
            (getMethod() == null) ? "post" : getMethod());
        renderAction(results);
        renderAttribute(results, "accept-charset", getAcceptCharset());
        renderAttribute(results, "class", getStyleClass());
        renderAttribute(results, "dir", getDir());
        renderAttribute(results, "enctype", getEnctype());
        renderAttribute(results, "lang", getLang());
        renderAttribute(results, "onreset", getOnreset());
        renderAttribute(results, "onsubmit", getOnsubmit());
        renderAttribute(results, "style", getStyle());
        renderAttribute(results, "target", getTarget());
        if (!isXhtml()) {
            renderAttribute(results, "autocomplete", getAutocomplete());
        }

        // Hook for additional attributes
        renderOtherAttributes(results);

        results.append(">");

        return results.toString();
    }
```

---

</SwmSnippet>

## Completing form setup and output

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Add security token to form for CSRF protection"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:495:495"
    node1 --> node2["Write form HTML to the web page"]
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:497:497"
    node2 --> node3["Make form tag available for later processing in request"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:500:501"
    node3 --> node4["Prepare form bean to capture user input"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:503:503"
    node4 --> node5["Allow page to include form body"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java:505:505"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Add security token to form for CSRF protection"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:495:495"
%%     node1 --> node2["Write form HTML to the web page"]
%%     click node2 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:497:497"
%%     node2 --> node3["Make form tag available for later processing in request"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:500:501"
%%     node3 --> node4["Prepare form bean to capture user input"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:503:503"
%%     node4 --> node5["Allow page to include form body"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/FormTag.java](taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java)</SwmPath>:505:505"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="495">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="483:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, after getting the form tag from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="493:7:7" line-data="        results.append(this.renderFormStartElement());">`renderFormStartElement`</SwmToken>, we append a token, write the form to the page, store the tag for later use, and initialize the form bean. The flow ends by including the body.

```java
        results.append(this.renderToken());

        TagUtils.getInstance().write(pageContext, results.toString());

        // Store this tag itself as a page attribute
        pageContext.setAttribute(Constants.FORM_KEY, this,
            PageContext.REQUEST_SCOPE);

        this.initFormBean();

        return (EVAL_BODY_INCLUDE);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
