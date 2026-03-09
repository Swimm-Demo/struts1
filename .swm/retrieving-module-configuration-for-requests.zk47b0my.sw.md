---
title: Retrieving Module Configuration for Requests
---
This document describes how the system retrieves the appropriate module configuration for each web request. The process ensures that every request is matched with the correct configuration, defaulting when necessary, so that subsequent actions have the required settings.

# Delegating Module Configuration Retrieval

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="786">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="786:5:5" line-data="    public ModuleConfig getModuleConfig(String module, PageContext pageContext) {">`getModuleConfig`</SwmToken>, we hand off the retrieval of the module configuration to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="788:1:1" line-data="            ModuleUtils.getInstance().getModuleConfig(module,">`ModuleUtils`</SwmToken>, passing the module name, request, and servlet context. This keeps the lookup logic consistent and avoids duplicating config resolution code.

```java
    public ModuleConfig getModuleConfig(String module, PageContext pageContext) {
        ModuleConfig config =
            ModuleUtils.getInstance().getModuleConfig(module,
                (HttpServletRequest) pageContext.getRequest(),
                pageContext.getServletContext());
```

---

</SwmSnippet>

## Resolving Module Configuration Based on Prefix

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="108">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="108:5:5" line-data="    public ModuleConfig getModuleConfig(String prefix,">`getModuleConfig`</SwmToken> in <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="788:1:1" line-data="            ModuleUtils.getInstance().getModuleConfig(module,">`ModuleUtils`</SwmToken> checks if a prefix is given. If so, it looks up the config using the prefix and context; if not, it uses the request and context. This lets us handle both explicit and default module lookups.

```java
    public ModuleConfig getModuleConfig(String prefix,
        HttpServletRequest request, ServletContext context) {
        ModuleConfig moduleConfig = null;

        if (prefix != null) {
            //lookup module stored with the given prefix.
            moduleConfig = this.getModuleConfig(prefix, context);
        } else {
            //return the current module if no prefix was supplied.
            moduleConfig = this.getModuleConfig(request, context);
        }

        return moduleConfig;
    }
```

---

</SwmSnippet>

## Fallback and Request Attribute Setting for <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="786:3:3" line-data="    public ModuleConfig getModuleConfig(String module, PageContext pageContext) {">`ModuleConfig`</SwmToken>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="130">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:5:5" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`getModuleConfig`</SwmToken> tries to get the config from the request first. If that's missing, it grabs the default config from the context using an empty string, then attaches it to the request for downstream access. This fallback ensures something is always returned.

```java
    public ModuleConfig getModuleConfig(HttpServletRequest request,
        ServletContext context) {
        ModuleConfig moduleConfig = this.getModuleConfig(request);

        if (moduleConfig == null) {
            moduleConfig = this.getModuleConfig("", context);
            request.setAttribute(Globals.MODULE_KEY, moduleConfig);
        }

        return moduleConfig;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="89">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:5:5" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`getModuleConfig`</SwmToken> checks if the prefix is null or "/" and uses the default module key for those cases. Otherwise, it appends the prefix to the key. This lets us fetch the right config from the servlet context based on module identity.

```java
    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {
        if ((prefix == null) || "/".equals(prefix)) {
            return (ModuleConfig) context.getAttribute(Globals.MODULE_KEY);
        } else {
            return (ModuleConfig) context.getAttribute(Globals.MODULE_KEY
                + prefix);
        }
    }
```

---

</SwmSnippet>

## Post-Retrieval Handling and Context Linking

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="788">

---

Back in TagUtils.getModuleConfig, after getting the config from <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="788:1:1" line-data="            ModuleUtils.getInstance().getModuleConfig(module,">`ModuleUtils`</SwmToken>, we need to tie it to the current request context, which is handled by <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken>. This ensures the config is accessible for any actions that follow.

```java
            ModuleUtils.getInstance().getModuleConfig(module,
                (HttpServletRequest) pageContext.getRequest(),
                pageContext.getServletContext());

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="94">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="94:5:5" line-data="    public HttpServletRequest getRequest() {">`getRequest`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken> just returns the current request from the web context. This lets other parts of the flow access request-scoped data, which is needed for linking module config to the request.

```java
    public HttpServletRequest getRequest() {
        return servletWebContext().getRequest();
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="792">

---

After coming back from <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken>, TagUtils.getModuleConfig checks if the config is null. If so, it throws a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="794:5:5" line-data="            throw new NullPointerException(&quot;Module &#39;&quot; + module + &quot;&#39; not found.&quot;);">`NullPointerException`</SwmToken>, stopping the flow if the module isn't found. Otherwise, it returns the config for downstream use.

```java
        // ModuleConfig not found
        if (config == null) {
            throw new NullPointerException("Module '" + module + "' not found.");
        }

        return config;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
