---
title: Editing User Registration Flow
---
This document describes the flow for handling user registration edit requests. The system receives a registration edit request, determines the action, checks the user's session, prepares the registration form (including multipart data support), and forwards the user to the appropriate page. A token is set to prevent double submissions.

# Delegating Action Execution

<SwmSnippet path="/extras/src/main/java/org/apache/struts/actions/MappingDispatchAction.java" line="162">

---

<SwmToken path="extras/src/main/java/org/apache/struts/actions/MappingDispatchAction.java" pos="162:5:5" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`execute`</SwmToken> just hands off to the superclass, which figures out which action method to call next. This is where the flow jumps to the actual action logic, like <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="92:6:6" line-data="            log.debug(&quot;EditRegistrationAction:  Processing &quot; + action +">`EditRegistrationAction`</SwmToken>, so the framework can route requests based on configuration.

```java
    public ActionForward execute(ActionMapping mapping, ActionForm form,
        HttpServletRequest request, HttpServletResponse response)
        throws Exception {
        // Use the overridden getMethodName.
        return super.execute(mapping, form, request, response);
    }
```

---

</SwmSnippet>

# Processing Registration Edit Request

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" line="80">

---

In <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="80:5:5" line-data="    public ActionForward execute(ActionMapping mapping,">`execute`</SwmToken>, we grab the session and pull the 'action' parameter from the request. If the request is multipart, the parameter access goes through <SwmToken path="core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" pos="38:4:4" line-data="public class MultipartRequestWrapper extends HttpServletRequestWrapper {">`MultipartRequestWrapper`</SwmToken>, which knows how to handle file uploads and similar cases.

```java
    public ActionForward execute(ActionMapping mapping,
                 ActionForm form,
                 HttpServletRequest request,
                 HttpServletResponse response)
    throws Exception {

    // Extract attributes we will need
    HttpSession session = request.getSession();
    String action = request.getParameter("action");
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" line="75">

---

<SwmToken path="core/src/main/java/org/apache/struts/upload/MultipartRequestWrapper.java" pos="75:5:5" line-data="    public String getParameter(String name) {">`getParameter`</SwmToken> tries the normal request first, but if that's missing (like with multipart forms), it checks its own parameters map. It assumes the map has string arrays and just grabs the first value if present.

```java
    public String getParameter(String name) {
        String value = getRequest().getParameter(name);

        if (value == null) {
            String[] mValue = (String[]) parameters.get(name);

            if ((mValue != null) && (mValue.length > 0)) {
                value = mValue[0];
            }
        }

        return value;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" line="89">

---

Back in EditRegistrationAction.execute, after getting the action, we check if it's missing and default to 'Create'. If the action isn't 'Create' and there's no user in the session, we forward to the logon page using <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="105:4:6" line-data="        return (mapping.findForward(&quot;logon&quot;));">`mapping.findForward`</SwmToken>. That's why the next step is <SwmToken path="extras/src/main/java/org/apache/struts/actions/MappingDispatchAction.java" pos="162:7:7" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionMapping`</SwmToken>.

```java
    if (action == null)
        action = "Create";
        if (log.isDebugEnabled()) {
            log.debug("EditRegistrationAction:  Processing " + action +
                        " action");
        }

    // Is there a currently logged on user?
    User user = null;
    if (!"Create".equals(action)) {
        user = (User) session.getAttribute(Constants.USER_KEY);
        if (user == null) {
                if (log.isDebugEnabled()) {
                    log.debug(" User is not logged on in session "
                              + session.getId());
                }
        return (mapping.findForward("logon"));
        }
    }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionMapping.java" line="70">

---

<SwmToken path="core/src/main/java/org/apache/struts/action/ActionMapping.java" pos="70:5:5" line-data="    public ActionForward findForward(String forwardName) {">`findForward`</SwmToken> looks for a forward config locally, then in the module config if needed. It expects the result to be an <SwmToken path="core/src/main/java/org/apache/struts/action/ActionMapping.java" pos="70:3:3" line-data="    public ActionForward findForward(String forwardName) {">`ActionForward`</SwmToken> and logs a warning if nothing is found.

```java
    public ActionForward findForward(String forwardName) {
        ForwardConfig config = findForwardConfig(forwardName);

        if (config == null) {
            config = getModuleConfig().findForwardConfig(forwardName);
        }

        // TODO: remove warning since findRequiredForward takes care of use case? 
        if (config == null) {
            if (log.isWarnEnabled()) {
                log.warn("Unable to find '" + forwardName + "' forward.");
            }
        }

        return ((ActionForward) config);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" line="109">

---

Just returned from <SwmToken path="extras/src/main/java/org/apache/struts/actions/MappingDispatchAction.java" pos="162:7:7" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionMapping`</SwmToken>, EditRegistrationAction.execute checks if the form is missing and creates a new <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="112:11:11" line-data="                log.trace(&quot; Creating new RegistrationForm bean under key &quot;">`RegistrationForm`</SwmToken>. It uses <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="113:3:7" line-data="                          + mapping.getAttribute());">`mapping.getAttribute()`</SwmToken> to decide the attribute name and stores it in either request or session scope, depending on the mapping.

```java
    // Populate the user registration form
    if (form == null) {
            if (log.isTraceEnabled()) {
                log.trace(" Creating new RegistrationForm bean under key "
                          + mapping.getAttribute());
            }
        form = new RegistrationForm();
            if ("request".equals(mapping.getScope()))
                request.setAttribute(mapping.getAttribute(), form);
            else
                session.setAttribute(mapping.getAttribute(), form);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="321">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="321:5:5" line-data="    public String getAttribute() {">`getAttribute`</SwmToken> returns the attribute if set, otherwise falls back to the action's name. This fallback isn't clear from the method name, but it's how the framework decides what key to use for storing the form.

```java
    public String getAttribute() {
        if (this.attribute == null) {
            return (this.name);
        } else {
            return (this.attribute);
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" line="121">

---

Just returned from <SwmToken path="core/src/main/java/org/apache/struts/action/ActionMapping.java" pos="25:10:10" line-data="import org.apache.struts.config.ActionConfig;">`ActionConfig`</SwmToken>, EditRegistrationAction.execute now copies user properties into the <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="121:1:1" line-data="    RegistrationForm regform = (RegistrationForm) form;">`RegistrationForm`</SwmToken> using <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="127:1:1" line-data="                PropertyUtils.copyProperties(regform, user);">`PropertyUtils`</SwmToken>. This is where we need to call <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="41:8:8" line-data="public class ActionConfig extends BaseConfig {">`BaseConfig`</SwmToken> next, since property copying is handled there.

```java
    RegistrationForm regform = (RegistrationForm) form;
    if (user != null) {
            if (log.isTraceEnabled()) {
                log.trace(" Populating form from " + user);
            }
            try {
                PropertyUtils.copyProperties(regform, user);
```

---

</SwmSnippet>

## Copying and Setting Configuration Properties

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="155">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/BaseConfig.java" pos="155:5:5" line-data="    protected Properties copyProperties() {">`copyProperties`</SwmToken> creates a new Properties object and copies all key-value pairs from the original. This avoids accidental changes to the shared properties.

```java
    protected Properties copyProperties() {
        Properties copy = new Properties();

        Enumeration keys = properties.propertyNames();

        while (keys.hasMoreElements()) {
            String key = (String) keys.nextElement();

            copy.setProperty(key, properties.getProperty(key));
        }

        return copy;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="88">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/BaseConfig.java" pos="88:5:5" line-data="    public void setProperty(String key, String value) {">`setProperty`</SwmToken> checks if the config is locked before allowing changes. If it's already configured, it throws, so properties can't be changed at the wrong time.

```java
    public void setProperty(String key, String value) {
        throwIfConfigured();
        properties.setProperty(key, value);
    }
```

---

</SwmSnippet>

## Finalizing Registration Edit and Forwarding

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" line="128">

---

Just returned from <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="41:8:8" line-data="public class ActionConfig extends BaseConfig {">`BaseConfig`</SwmToken>, EditRegistrationAction.execute clears sensitive fields, sets a token to prevent double submits, and finally forwards to the 'success' page using <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/EditRegistrationAction.java" pos="153:4:6" line-data="    return (mapping.findForward(&quot;success&quot;));">`mapping.findForward`</SwmToken>. That's why <SwmToken path="extras/src/main/java/org/apache/struts/actions/MappingDispatchAction.java" pos="162:7:7" line-data="    public ActionForward execute(ActionMapping mapping, ActionForm form,">`ActionMapping`</SwmToken> is next.

```java
                regform.setAction(action);
                regform.setPassword(null);
                regform.setPassword2(null);
            } catch (InvocationTargetException e) {
                Throwable t = e.getTargetException();
                if (t == null)
                    t = e;
                log.error("RegistrationForm.populate", t);
                throw new ServletException("RegistrationForm.populate", t);
            } catch (Throwable t) {
                log.error("RegistrationForm.populate", t);
                throw new ServletException("RegistrationForm.populate", t);
            }
    }

        // Set a transactional control token to prevent double posting
        if (log.isTraceEnabled()) {
            log.trace(" Setting transactional control token");
        }
        saveToken(request);

    // Forward control to the edit user registration page
        if (log.isTraceEnabled()) {
            log.trace(" Forwarding to 'success' page");
        }
    return (mapping.findForward("success"));

    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
