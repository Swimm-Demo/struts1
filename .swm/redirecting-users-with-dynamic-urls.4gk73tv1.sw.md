---
title: Redirecting users with dynamic URLs
---
This document explains how, when a redirect tag completes, a destination URL is dynamically generated using the tag's configuration and current context, and the user is redirected to this URL.

# Handling Redirect Tag Output

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="267">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="267:5:5" line-data="    public int doEndTag() throws JspException {">`doEndTag`</SwmToken> kicks off the redirect process when the tag finishes. It calls <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:7:7" line-data="        this.doRedirect(this.generateRedirectURL());">`generateRedirectURL`</SwmToken> to build the target URL based on the tag's configuration, then immediately performs the redirect. We need to call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="268:7:7" line-data="        this.doRedirect(this.generateRedirectURL());">`generateRedirectURL`</SwmToken> here because the actual destination can depend on dynamic parameters and context, not just a static value.

```java
    public int doEndTag() throws JspException {
        this.doRedirect(this.generateRedirectURL());

        return (SKIP_PAGE);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" line="279">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="279:5:5" line-data="    protected String generateRedirectURL()">`generateRedirectURL`</SwmToken> builds the actual redirect URL. It first collects all relevant parameters from the tag's attributes and context using <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="282:7:7" line-data="            TagUtils.getInstance().computeParameters(pageContext, paramId,">`computeParameters`</SwmToken>, then passes everything to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/logic/RedirectTag.java" pos="289:11:11" line-data="            url = TagUtils.getInstance().computeURLWithCharEncoding(pageContext,">`computeURLWithCharEncoding`</SwmToken> to assemble and encode the final URL. If anything goes wrong, it catches the error and wraps it for the JSP engine.

```java
    protected String generateRedirectURL()
        throws JspException {
        Map params =
            TagUtils.getInstance().computeParameters(pageContext, paramId,
                paramName, paramProperty, paramScope, name, property, scope,
                transaction);

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

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
