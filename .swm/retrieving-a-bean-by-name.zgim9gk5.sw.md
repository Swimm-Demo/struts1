---
title: Retrieving a Bean by Name
---
This document describes how a bean is retrieved by name, either from a specific scope or by searching all available contexts. This enables dynamic component rendering and data sharing across the application. The input is the bean name and an optional scope, and the output is the bean object.

# Resolving a Bean Reference

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="127">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="127:7:7" line-data="    public static Object retrieveBean(String beanName, String scopeName, PageContext pageContext)">`retrieveBean`</SwmToken>, we check if <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="127:16:16" line-data="    public static Object retrieveBean(String beanName, String scopeName, PageContext pageContext)">`scopeName`</SwmToken> is null to decide whether to do a broad search for the bean. If it is, we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="131:3:3" line-data="            return findAttribute(beanName, pageContext);">`findAttribute`</SwmToken> to look across all possible places (component context, then all scopes) for the bean, instead of just a specific scope.

```java
    public static Object retrieveBean(String beanName, String scopeName, PageContext pageContext)
        throws JspException {

        if (scopeName == null) {
            return findAttribute(beanName, pageContext);
        }

```

---

</SwmSnippet>

## Searching for a Bean in All Contexts

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="148">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="148:7:7" line-data="    public static Object findAttribute(String beanName, PageContext pageContext) {">`findAttribute`</SwmToken>, we grab the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="149:1:1" line-data="        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`ComponentContext`</SwmToken> from the request. This lets us check for the bean in the tile/component-specific context before falling back to the usual page scopes.

```java
    public static Object findAttribute(String beanName, PageContext pageContext) {
        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="149">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="131:3:3" line-data="            return findAttribute(beanName, pageContext);">`findAttribute`</SwmToken>, after getting the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="149:1:1" line-data="        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`ComponentContext`</SwmToken>, we use it to look for the bean. If it's not there (or the context is null), we move on to check the regular <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="149:11:11" line-data="        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`pageContext`</SwmToken> scopes.

```java
        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());

```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/ComponentContext.java" line="187">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentContext.java" pos="187:7:7" line-data="    static public ComponentContext getContext(ServletRequest request) {">`getContext`</SwmToken> checks for a JSP exception in the request and returns null if one is found, otherwise it fetches the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentContext.java" pos="187:5:5" line-data="    static public ComponentContext getContext(ServletRequest request) {">`ComponentContext`</SwmToken> from the request. This avoids using a context when the page is in an error state.

```java
    static public ComponentContext getContext(ServletRequest request) {
       if (request.getAttribute("javax.servlet.jsp.jspException") != null) {
           return null;
        }        return (ComponentContext) request.getAttribute(
            ComponentConstants.COMPONENT_CONTEXT);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="151">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="152:9:9" line-data="            Object attribute = compContext.findAttribute(beanName, pageContext);">`findAttribute`</SwmToken>, after getting the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="149:1:1" line-data="        ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`ComponentContext`</SwmToken> (or null), we try to find the bean in it. If not found, we fall back to searching all the standard <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="152:14:14" line-data="            Object attribute = compContext.findAttribute(beanName, pageContext);">`pageContext`</SwmToken> scopes.

```java
        if (compContext != null) {
            Object attribute = compContext.findAttribute(beanName, pageContext);
            if (attribute != null) {
                return attribute;
            }
        }

        // Search in pageContext scopes
        return pageContext.findAttribute(beanName);
    }
```

---

</SwmSnippet>

## Scoped Bean Lookup

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="134">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="127:7:7" line-data="    public static Object retrieveBean(String beanName, String scopeName, PageContext pageContext)">`retrieveBean`</SwmToken>, if a specific scope was given, we resolve it and call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="137:6:6" line-data="        //return pageContext.getAttribute( beanName, scope );">`getAttribute`</SwmToken> to fetch the bean from that exact scope, instead of searching everywhere.

```java
        // Default value doesn't matter because we have already check it
        int scope = getScope(scopeName, PageContext.PAGE_SCOPE);

        //return pageContext.getAttribute( beanName, scope );
        return getAttribute(beanName, scope, pageContext);
    }
```

---

</SwmSnippet>

# Fetching a Bean from a Specific Scope

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="170">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="170:7:7" line-data="    public static Object getAttribute(String beanName, int scope, PageContext pageContext) {">`getAttribute`</SwmToken>, if the scope is <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="171:10:10" line-data="        if (scope == ComponentConstants.COMPONENT_SCOPE) {">`COMPONENT_SCOPE`</SwmToken>, we fetch the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="172:1:1" line-data="            ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`ComponentContext`</SwmToken> from the request to look for the bean there, since it's not in the usual scopes.

```java
    public static Object getAttribute(String beanName, int scope, PageContext pageContext) {
        if (scope == ComponentConstants.COMPONENT_SCOPE) {
            ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" line="172">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="173:5:5" line-data="            return compContext.getAttribute(beanName);">`getAttribute`</SwmToken>, after getting the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="172:1:1" line-data="            ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());">`ComponentContext`</SwmToken>, we fetch the bean from it if we're in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="171:10:10" line-data="        if (scope == ComponentConstants.COMPONENT_SCOPE) {">`COMPONENT_SCOPE`</SwmToken>. For any other scope, we just use <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/util/TagUtils.java" pos="175:3:5" line-data="        return pageContext.getAttribute(beanName, scope);">`pageContext.getAttribute`</SwmToken>.

```java
            ComponentContext compContext = ComponentContext.getContext(pageContext.getRequest());
            return compContext.getAttribute(beanName);
        }
        return pageContext.getAttribute(beanName, scope);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
