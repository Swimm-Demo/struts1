---
title: Preparing Action Method Parameters
---
This document explains how the system prepares the parameters needed to invoke an action method when handling a web request. The flow collects the action configuration, form data, and HTTP request and response objects from the web context, assembling them into an array for the action method call.

# Collecting Action Parameters from Context

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="84">

---

In <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="84:9:9" line-data="    protected final Object[] buildClassicArguments(ServletActionContext context) {">`buildClassicArguments`</SwmToken>, we're grabbing the action mapping, form, and request from the context. We need to call <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="84:11:11" line-data="    protected final Object[] buildClassicArguments(ServletActionContext context) {">`ServletActionContext`</SwmToken> next to get the actual request and response objects, since they're not directly available here and are required for the action method call.

```java
    protected final Object[] buildClassicArguments(ServletActionContext context) {
        ActionConfig mapping = context.getActionConfig();
        ActionForm form = context.getActionForm();
        HttpServletRequest request = context.getRequest();
```

---

</SwmSnippet>

## Accessing HTTP Request from Web Context

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="94">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="94:5:5" line-data="    public HttpServletRequest getRequest() {">`getRequest`</SwmToken> just hands off to <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="95:3:3" line-data="        return servletWebContext().getRequest();">`servletWebContext`</SwmToken> to fetch the actual <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="94:3:3" line-data="    public HttpServletRequest getRequest() {">`HttpServletRequest`</SwmToken>. We need to call this because the request is tucked away inside the web context, not directly in the action context.

```java
    public HttpServletRequest getRequest() {
        return servletWebContext().getRequest();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="67">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:5:5" line-data="    protected ServletWebContext servletWebContext() {">`servletWebContext`</SwmToken> casts the base context to <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="67:3:3" line-data="    protected ServletWebContext servletWebContext() {">`ServletWebContext`</SwmToken>, assuming it's always the right type. This is a hidden dependency—if the base context isn't what we expect, things break.

```java
    protected ServletWebContext servletWebContext() {
        return (ServletWebContext) this.getBaseContext();
    }
```

---

</SwmSnippet>

## Finalizing Argument Assembly for Action Method

<SwmSnippet path="/core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" line="88">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="84:9:9" line-data="    protected final Object[] buildClassicArguments(ServletActionContext context) {">`buildClassicArguments`</SwmToken>, after fetching request and response from <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="84:11:11" line-data="    protected final Object[] buildClassicArguments(ServletActionContext context) {">`ServletActionContext`</SwmToken>, we bundle everything into an array for the action method call. We just returned from <SwmToken path="core/src/main/java/org/apache/struts/dispatcher/servlet/ServletMethodResolver.java" pos="84:11:11" line-data="    protected final Object[] buildClassicArguments(ServletActionContext context) {">`ServletActionContext`</SwmToken> to get these HTTP objects.

```java
        HttpServletResponse response = context.getResponse();
        return new Object[] { mapping, form, request, response };
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="103">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="103:5:5" line-data="    public HttpServletResponse getResponse() {">`getResponse`</SwmToken> pulls the <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="103:3:3" line-data="    public HttpServletResponse getResponse() {">`HttpServletResponse`</SwmToken> from <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="104:3:3" line-data="        return servletWebContext().getResponse();">`servletWebContext`</SwmToken>. We need this because the response is managed inside the web context, not directly in the action context.

```java
    public HttpServletResponse getResponse() {
        return servletWebContext().getResponse();
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
