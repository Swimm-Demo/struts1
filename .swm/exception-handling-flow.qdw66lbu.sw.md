---
title: Exception Handling Flow
---
This document describes how exceptions are handled by delegating to configurable handlers. When an exception occurs, the system selects the handler based on configuration, which determines the response path and prepares error information. This supports flexible error handling and keeps business logic clean.

# Delegating and Executing Exception Handling

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/commands/servlet/ExceptionHandler.java" line="50">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/commands/servlet/ExceptionHandler.java" pos="50:5:5" line-data="    protected ForwardConfig handle(ActionContext context, Exception exception,">`handle`</SwmToken> kicks off the exception handling by casting the context to access servlet objects, then looks up the right <SwmToken path="core/src/main/java/org/apache/struts/chain/commands/servlet/ExceptionHandler.java" pos="61:9:9" line-data="        org.apache.struts.action.ExceptionHandler handler =">`ExceptionHandler`</SwmToken> class using the config, and finally hands off the actual work to that handler's <SwmToken path="core/src/main/java/org/apache/struts/chain/commands/servlet/ExceptionHandler.java" pos="65:6:6" line-data="        return (handler.execute(exception, exceptionConfig,">`execute`</SwmToken> method. This lets us swap out how exceptions are handled just by changing config, and keeps the main flow clean.

```java
    protected ForwardConfig handle(ActionContext context, Exception exception,
        ExceptionConfig exceptionConfig, ActionConfig actionConfig,
        ModuleConfig moduleConfig)
        throws Exception {
        // Look up the remaining properties needed for this handler
        ServletActionContext sacontext = (ServletActionContext) context;
        ActionForm actionForm = (ActionForm) sacontext.getActionForm();
        HttpServletRequest request = sacontext.getRequest();
        HttpServletResponse response = sacontext.getResponse();

        // Handle this exception
        org.apache.struts.action.ExceptionHandler handler =
            (org.apache.struts.action.ExceptionHandler) ClassUtils
            .getApplicationInstance(exceptionConfig.getHandler());

        return (handler.execute(exception, exceptionConfig,
            (ActionMapping) actionConfig, actionForm, request, response));
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ExceptionHandler.java" line="124">

---

<SwmToken path="core/src/main/java/org/apache/struts/action/ExceptionHandler.java" pos="124:5:5" line-data="    public ActionForward execute(Exception ex, ExceptionConfig ae,">`execute`</SwmToken> figures out where to forward after an exception (either from config or the input forward), builds the error message, stores everything in the request, and then checks if the response can still be forwarded. If not, it either tries alternate handling or just logs and bails, depending on config.

```java
    public ActionForward execute(Exception ex, ExceptionConfig ae,
        ActionMapping mapping, ActionForm formInstance,
        HttpServletRequest request, HttpServletResponse response)
        throws ServletException {
        LOG.debug("ExceptionHandler executing for exception " + ex);

        ActionForward forward;
        ActionMessage error;
        String property;

        // Build the forward from the exception mapping if it exists
        // or from the form input
        if (ae.getPath() != null) {
            forward = new ActionForward(ae.getPath());
        } else {
            forward = mapping.getInputForward();
        }

        // Figure out the error
        if (ex instanceof ModuleException) {
            error = ((ModuleException) ex).getActionMessage();
            property = ((ModuleException) ex).getProperty();
        } else {
            // STR-2924
            if (ae.getKey() != null) {
                error = new ActionMessage(ae.getKey(), ex.getMessage());
                property = error.getKey();
            } else {
                error = null;
                property = null;
            }
        }

        this.logException(ex);

        // Store the exception
        request.setAttribute(Globals.EXCEPTION_KEY, ex);
        this.storeException(request, property, error, forward, ae.getScope());

        if (!response.isCommitted()) {
            return forward;
        }

        LOG.debug("Response is already committed, so forwarding will not work."
            + " Attempt alternate handling.");

        if (!silent(ae)) {
            handleCommittedResponse(ex, ae, mapping, formInstance, request,
                response, forward);
        } else {
            LOG.warn("ExceptionHandler configured with " + SILENT_IF_COMMITTED
                + " and response is committed.", ex);
        }

        return null;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
