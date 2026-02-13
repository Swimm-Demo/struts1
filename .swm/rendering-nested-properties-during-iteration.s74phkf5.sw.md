---
title: Rendering Nested Properties During Iteration
---
This document explains how nested property output is rendered and managed during iteration. The flow prepares the output for each item, renders the nested property, and updates references to support dynamic display of complex nested data structures in web pages.

# Iterating and Preparing Nested Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/nested/logic/NestedIterateTag.java" line="125">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/logic/NestedIterateTag.java" pos="125:5:5" line-data="    public int doAfterBody() throws JspException {">`doAfterBody`</SwmToken>, we kick off the post-body logic for the nested iteration. We grab the result from the parent tag's <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/logic/NestedIterateTag.java" pos="125:5:5" line-data="    public int doAfterBody() throws JspException {">`doAfterBody`</SwmToken>, then move on to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyTag.java" pos="30:3:3" line-data=" * NestedPropertyTag.">`NestedPropertyTag`</SwmToken> to handle the rendering of the current nested property. This sets up the output for each item in the iteration.

```java
    public int doAfterBody() throws JspException {
        // store original result
        int temp = super.doAfterBody();
```

---

</SwmSnippet>

## Rendering Nested Property Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyTag.java" line="108">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyTag.java" pos="108:5:5" line-data="    public int doAfterBody() throws JspException {">`doAfterBody`</SwmToken> in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyTag.java" pos="30:3:3" line-data=" * NestedPropertyTag.">`NestedPropertyTag`</SwmToken> checks if there's body content to render, then calls TagUtils.writePrevious to push that content to the right output stream. This step ensures the nested property output is handled properly before clearing the buffer.

```java
    public int doAfterBody() throws JspException {
        /* Render the output */
        if (bodyContent != null) {
            TagUtils.getInstance().writePrevious(pageContext,
                bodyContent.getString());
            bodyContent.clearBody();
        }

        return (SKIP_BODY);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="1206">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1206:5:5" line-data="    public void writePrevious(PageContext pageContext, String text)">`writePrevious`</SwmToken> grabs the current output writer, checks if it's a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="1210:8:8" line-data="        if (writer instanceof BodyContent) {">`BodyContent`</SwmToken>, and if so, switches to the enclosing writer. This makes sure the nested tag's output goes to the right place in the page, not just stuck in a buffer.

```java
    public void writePrevious(PageContext pageContext, String text)
        throws JspException {
        JspWriter writer = pageContext.getOut();

        if (writer instanceof BodyContent) {
            writer = ((BodyContent) writer).getEnclosingWriter();
        }

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

## Updating Reference and Completing Iteration

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/nested/logic/NestedIterateTag.java" line="128">

---

Back in NestedIterateTag.doAfterBody, after <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyTag.java" pos="30:3:3" line-data=" * NestedPropertyTag.">`NestedPropertyTag`</SwmToken> finishes, we update the property reference for the next loop. If we're not skipping the body, this keeps the nested iteration in sync, then we return the result from the parent tag.

```java
        HttpServletRequest request =
            (HttpServletRequest) pageContext.getRequest();

        if (temp != SKIP_BODY) {
            // set the new reference
            NestedPropertyHelper.setProperty(request, deriveNestedProperty());
        }

        // return super result
        return temp;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
