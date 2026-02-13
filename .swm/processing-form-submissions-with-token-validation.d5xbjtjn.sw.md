---
title: Processing Form Submissions with Token Validation
---
This document describes how user form submissions are processed to prevent duplicate submissions and ensure users are directed to the correct page. The system checks for cancellation, validates the submission token, and determines whether to return the user to the form with an error or forward them to the success page.

# Handling Form Submission and Token Checks

<SwmSnippet path="/apps/cookbook/src/main/java/examples/token/ProcessTokenAction.java" line="67">

---

In `ProcessTokenAction.execute`, we start by handling a user pressing 'Cancel'—if that's the case, we just forward them home and stop. Otherwise, we check for duplicate submissions by validating the token. If the token isn't valid, we log an error for the UI. Next, we need to call into <SwmToken path="scripting/src/main/java/org/apache/struts/scripting/ScriptAction.java" pos="115:4:4" line-data="public class ScriptAction extends Action {">`ScriptAction`</SwmToken> to actually perform the token validation logic, since that's where the check is implemented.

```java
    public ActionForward execute(
        ActionMapping mapping,
        ActionForm form,
        HttpServletRequest request,
        HttpServletResponse response)
        throws Exception {

        // If user pressed 'Cancel' button,
        // return to home page
        if (isCancelled(request)) {
            return mapping.findForward("home");
        }

        ActionErrors errors = new ActionErrors();

        // Prevent unintentional duplication submissions by checking
        // that we have not received this token previously
        if (!isTokenValid(request)) {
            errors.add(
                ActionMessages.GLOBAL_MESSAGE,
                new ActionMessage("errors.token"));
        }
```

---

</SwmSnippet>

## Delegating Token Validation Logic

<SwmSnippet path="/scripting/src/main/java/org/apache/struts/scripting/ScriptAction.java" line="442">

---

<SwmToken path="scripting/src/main/java/org/apache/struts/scripting/ScriptAction.java" pos="442:5:5" line-data="    public boolean isTokenValid(HttpServletRequest req) {">`isTokenValid`</SwmToken> in <SwmToken path="scripting/src/main/java/org/apache/struts/scripting/ScriptAction.java" pos="115:4:4" line-data="public class ScriptAction extends Action {">`ScriptAction`</SwmToken> just hands off the token validation to its superclass. No extra logic here—it's just delegating, so we move on to the core implementation in <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken>.

```java
    public boolean isTokenValid(HttpServletRequest req) {
        return super.isTokenValid(req);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" line="244">

---

<SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="244:5:5" line-data="    public boolean isTokenValid(boolean reset) {">`isTokenValid`</SwmToken> in <SwmToken path="core/src/main/java/org/apache/struts/chain/contexts/ServletActionContext.java" pos="39:4:4" line-data="public class ServletActionContext extends WebActionContext {">`ServletActionContext`</SwmToken> just forwards the request and reset flag to the token object, which does the real validation. The only thing that matters here is whether we want to reset the token after checking.

```java
    public boolean isTokenValid(boolean reset) {
        return token.isTokenValid(getRequest(), reset);
    }
```

---

</SwmSnippet>

## Handling Errors and Forwarding After Validation

<SwmSnippet path="/apps/cookbook/src/main/java/examples/token/ProcessTokenAction.java" line="89">

---

Back in `ProcessTokenAction.execute`, after coming back from <SwmToken path="scripting/src/main/java/org/apache/struts/scripting/ScriptAction.java" pos="115:4:4" line-data="public class ScriptAction extends Action {">`ScriptAction`</SwmToken>'s token validation, we reset the token. If there were errors (like a bad token), we save those errors, generate a new token, and send the user back to the form. If everything checks out, we forward them to the success page.

```java
        resetToken(request);

        // Report any errors we have discovered back to the original form
        if (!errors.isEmpty()) {
            saveErrors(request, errors);
            saveToken(request);
            return (mapping.getInputForward());
        }

        // Forward to result page
        return mapping.findForward("success");
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
