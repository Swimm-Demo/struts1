---
title: Redirecting users to a dynamic URL
---
This document explains how users are redirected to a dynamically generated URL based on parameters and context. The process involves building the destination URL, sending the redirect response, and stopping further page processing.

# Triggering the Redirect Logic

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="267">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="267:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, we kick off the redirect process by generating the target URL with <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:7:7" line-data="        this.doRedirect(this.generateRedirectURL());">`generateRedirectURL`</SwmToken>. This step ensures the redirect destination is built with all relevant parameters before actually performing the redirect.

```java
    public int doEndTag() throws JspException {
        this.doRedirect(this.generateRedirectURL());
```

---

</SwmSnippet>

## Building the Redirect URL

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="279">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="279:5:5" line-data="    protected String generateRedirectURL()">`generateRedirectURL`</SwmToken>, we gather all parameters for the redirect by calling TagUtils.computeParameters. This consolidates any tag or context data into a single map for URL construction.

```java
    protected String generateRedirectURL()
        throws JspException {
        Map params =
            TagUtils.getInstance().computeParameters(pageContext, paramId,
                paramName, paramProperty, paramScope, name, property, scope,
                transaction);

```

---

</SwmSnippet>

### Extracting Parameters from Context

See <SwmLink doc-title="Building the Parameter Map for Tag Processing">[Building the Parameter Map for Tag Processing](/.swm/building-the-parameter-map-for-tag-processing.j9jq6qxx.sw.md)</SwmLink>

### Encoding and Finalizing the Redirect URL

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="286">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:7:7" line-data="        this.doRedirect(this.generateRedirectURL());">`generateRedirectURL`</SwmToken>, after getting parameters, we encode and assemble the final URL with <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="289:11:11" line-data="            url = TagUtils.getInstance().computeURLWithCharEncoding(pageContext,">`computeURLWithCharEncoding`</SwmToken>. If something goes wrong (like a malformed URL), we save the exception using TagUtils.saveException for error handling.

```java
        String url = null;

        try {
            url = TagUtils.getInstance().computeURLWithCharEncoding(pageContext,
                    forward, href, page, action, module, params, anchor, true,
                    useLocalEncoding);
        } catch (MalformedURLException e) {
            TagUtils.getInstance().saveException(pageContext, e);
            throw new JspException(messages.getMessage("redirect.url",
                    e.toString()), e);
        }

        return url;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="301">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="301:7:7" line-data="    public static void saveException(PageContext pageContext, Throwable exception) {">`saveException`</SwmToken> attaches the exception to the page context, making it available for error handling or display logic downstream.

```java
    public static void saveException(PageContext pageContext, Throwable exception) {
        pageContext.setAttribute(Globals.EXCEPTION_KEY, exception, PageContext.REQUEST_SCOPE);
    }
```

---

</SwmSnippet>

## Completing the Redirect Tag Processing

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="268">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="267:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken>, after building the URL, we actually perform the redirect with <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:3:3" line-data="        this.doRedirect(this.generateRedirectURL());">`doRedirect`</SwmToken>, then return <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="270:4:4" line-data="        return (SKIP_PAGE);">`SKIP_PAGE`</SwmToken> to stop further JSP processing.

```java
        this.doRedirect(this.generateRedirectURL());

        return (SKIP_PAGE);
    }
```

---

</SwmSnippet>

# Sending the Redirect Response

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="308">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="308:5:5" line-data="    protected void doRedirect(String url)">`doRedirect`</SwmToken>, we grab the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="310:1:1" line-data="        HttpServletResponse response =">`HttpServletResponse`</SwmToken> from the page context to send the actual redirect. This requires accessing the servlet response object, which is handled by <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken>.

```java
    protected void doRedirect(String url)
        throws JspException {
        HttpServletResponse response =
            (HttpServletResponse) pageContext.getResponse();

```

---

</SwmSnippet>

## Accessing the Servlet Response

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="103">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="103:5:5" line-data="    public HttpServletResponse getResponse() {">`getResponse`</SwmToken> fetches the servlet response by casting the base context to <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:3:3" line-data="    protected ServletWebContext servletWebContext() {">`ServletWebContext`</SwmToken>. This relies on the context being set up correctly, or else it throws a ClassCastException.

```java
    public HttpServletResponse getResponse() {
        return servletWebContext().getResponse();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="67">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:5:5" line-data="    protected ServletWebContext servletWebContext() {">`servletWebContext`</SwmToken> just casts the base context to <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:3:3" line-data="    protected ServletWebContext servletWebContext() {">`ServletWebContext`</SwmToken> without checking. If the context isn't set up right, this will blow up with a ClassCastException.

```java
    protected ServletWebContext servletWebContext() {
        return (ServletWebContext) this.getBaseContext();
    }
```

---

</SwmSnippet>

## Performing the Redirect and Handling Errors

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="313">

---

Back in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:3:3" line-data="        this.doRedirect(this.generateRedirectURL());">`doRedirect`</SwmToken>, after trying to send the redirect, any <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="315:6:6" line-data="        } catch (IOException e) {">`IOException`</SwmToken> is caught and saved with TagUtils.saveException, then rethrown as a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="317:5:5" line-data="            throw new JspException(e.getMessage(), e);">`JspException`</SwmToken> for higher-level error handling.

```java
        try {
            response.sendRedirect(url);
        } catch (IOException e) {
            TagUtils.getInstance().saveException(pageContext, e);
            throw new JspException(e.getMessage(), e);
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
