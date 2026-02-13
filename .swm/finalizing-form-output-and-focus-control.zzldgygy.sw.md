---
title: Finalizing Form Output and Focus Control
---
This document explains how the form output is finalized as part of the form lifecycle in the web application. After processing a form, the system cleans up form-related attributes, appends the closing form tag, and optionally generates <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="689:5:5" line-data="        // Render JavaScript to set the input focus if required">`JavaScript`</SwmToken> to set input focus for the user. The finalized output is written to the page and the form state is reset.

# Cleaning Up and Finalizing Form Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="679">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="679:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, we clear out form-related attributes from the request scope so nothing hangs around after the form is processed. Then we append the closing '</form>' tag to the output buffer. If there's a focus property set, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="691:7:7" line-data="            results.append(this.renderFocusJavascript());">`renderFocusJavascript`</SwmToken> to add <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="689:5:5" line-data="        // Render JavaScript to set the input focus if required">`JavaScript`</SwmToken> that sets the input focus when the page loads. This keeps the form output tidy and optionally adds client-side focus handling.

```java
    public int doEndTag() throws JspException {
        // Remove the page scope attributes we created
        pageContext.removeAttribute(Constants.BEAN_KEY,
            PageContext.REQUEST_SCOPE);
        pageContext.removeAttribute(Constants.FORM_KEY,
            PageContext.REQUEST_SCOPE);

        // Render a tag representing the end of our current form
        StringBuffer results = new StringBuffer("</form>");

        // Render JavaScript to set the input focus if required
        if (this.focus != null) {
            results.append(this.renderFocusJavascript());
        }

```

---

</SwmSnippet>

## Generating Focus Control Script

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="715">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="715:5:5" line-data="    protected String renderFocusJavascript() {">`renderFocusJavascript`</SwmToken>, we start building the script tag and decide how to wrap the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="722:10:10" line-data="            results.append(&quot; language=\&quot;JavaScript\&quot;&quot;);">`JavaScript`</SwmToken> based on whether the page is XHTML or HTML. Then we construct the reference to the form element that should get focus, including an index if needed. The script only sets focus if the element is valid, visible, and enabled. Calling <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="721:7:7" line-data="        if (!this.isXhtml() &amp;&amp; this.scriptLanguage) {">`isXhtml`</SwmToken> next lets us pick the right wrapping for the script content.

```java
    protected String renderFocusJavascript() {
        StringBuffer results = new StringBuffer();

        results.append(lineEnd);
        results.append("<script type=\"text/javascript\"");

        if (!this.isXhtml() && this.scriptLanguage) {
            results.append(" language=\"JavaScript\"");
        }

        results.append(">");
        results.append(lineEnd);

        // xhtml content should emit CDATA section
        // but html content should use the browser hiding trick
        results.append(isXhtml() ? "//<![CDATA[" : "<!--");
        results.append(lineEnd);

        // Construct the index if needed and insert into focus statement
        String index = "";
        if (this.focusIndex != null) {
            StringBuffer sb = new StringBuffer("[");
            sb.append(this.focusIndex);
            sb.append("]");
            index = sb.toString();
        }
        
        // Construct the control name that will receive focus.
        StringBuffer focusControl = new StringBuffer("document.forms[\"");
        focusControl.append(beanName);
        focusControl.append("\"].elements[\"");
        focusControl.append(this.focus);
        focusControl.append("\"]");
        focusControl.append(index);

        results.append("  var focusControl = ");
        results.append(focusControl.toString());
        results.append(";");
        results.append(lineEnd);
        results.append(lineEnd);

        results.append("  if (focusControl != null && ");
        results.append("focusControl.type != \"hidden\" && ");
        results.append("!focusControl.disabled && ");
        results.append("focusControl.style.display != \"none\") {");
        results.append(lineEnd);

        results.append("     focusControl");
        results.append(".focus();");
        results.append(lineEnd);

        results.append("  }");
        results.append(lineEnd);

        results.append("//");
        results.append(isXhtml() ? "]]>" : "-->");
```

---

</SwmSnippet>

### Determining Document Type for Script Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="898">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="898:5:5" line-data="    private boolean isXhtml() {">`isXhtml`</SwmToken> just asks <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="899:3:3" line-data="        return TagUtils.getInstance().isXhtml(this.pageContext);">`TagUtils`</SwmToken> if the current page is XHTML. This keeps the XHTML detection logic out of <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="50:4:4" line-data="public class FormTag extends TagSupport {">`FormTag`</SwmToken> and lets <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="899:3:3" line-data="        return TagUtils.getInstance().isXhtml(this.pageContext);">`TagUtils`</SwmToken> handle it. We call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="899:3:3" line-data="        return TagUtils.getInstance().isXhtml(this.pageContext);">`TagUtils`</SwmToken> next to actually check the page context.

```java
    private boolean isXhtml() {
        return TagUtils.getInstance().isXhtml(this.pageContext);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="838">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="838:5:5" line-data="    public boolean isXhtml(PageContext pageContext) {">`isXhtml`</SwmToken> in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="899:3:3" line-data="        return TagUtils.getInstance().isXhtml(this.pageContext);">`TagUtils`</SwmToken> grabs the value for <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="841:14:16" line-data="            xhtml = (String) lookup(pageContext, Globals.XHTML_KEY, null);">`Globals.XHTML_KEY`</SwmToken> from the page context and checks if it's 'true'. If the lookup fails, it logs the error and throws a runtime exception, so you know something went wrong.

```java
    public boolean isXhtml(PageContext pageContext) {
        String xhtml;
        try {
            xhtml = (String) lookup(pageContext, Globals.XHTML_KEY, null);
            return "true".equalsIgnoreCase(xhtml);
        } catch (JspException e) {
            log.error("Failed xhtml lookup", e);
            throw new RuntimeException(e);
        }
    }
```

---

</SwmSnippet>

### Completing the Focus Script Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="771">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="691:7:7" line-data="            results.append(this.renderFocusJavascript());">`renderFocusJavascript`</SwmToken>, after checking <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="721:7:7" line-data="        if (!this.isXhtml() &amp;&amp; this.scriptLanguage) {">`isXhtml`</SwmToken>, we finish the script tag with either a CDATA section or HTML comments, depending on the document type. This makes sure the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="689:5:5" line-data="        // Render JavaScript to set the input focus if required">`JavaScript`</SwmToken> is handled right in both XHTML and HTML pages.

```java
        results.append(lineEnd);

        results.append("</script>");
        results.append(lineEnd);

        return results.toString();
    }
```

---

</SwmSnippet>

## Writing Output and Resetting State

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" line="694">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="679:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, after getting the focus script from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="691:7:7" line-data="            results.append(this.renderFocusJavascript());">`renderFocusJavascript`</SwmToken>, we write the whole output (closing form tag and optional <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="689:5:5" line-data="        // Render JavaScript to set the input focus if required">`JavaScript`</SwmToken>) to the JSP output stream. Then we reset <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="703:1:1" line-data="        postbackAction = null;">`postbackAction`</SwmToken> to null and return <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/FormTag.java" pos="706:4:4" line-data="        return (EVAL_PAGE);">`EVAL_PAGE`</SwmToken> so the rest of the JSP keeps processing.

```java
        // Print this value to our output writer
        JspWriter writer = pageContext.getOut();

        try {
            writer.print(results.toString());
        } catch (IOException e) {
            throw new JspException(messages.getMessage("common.io", e.toString()), e);
        }

        postbackAction = null;

        // Continue processing this page
        return (EVAL_PAGE);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
