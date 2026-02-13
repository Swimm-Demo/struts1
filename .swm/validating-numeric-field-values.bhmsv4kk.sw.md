---
title: Validating Numeric Field Values
---
This document describes how the system validates a field value to ensure it is a valid number. If the value is missing, it is accepted. If the value is present but invalid, a localized error message is generated and returned to the user.

```mermaid
flowchart TD
  node1["Validating Double Field Values"]:::HeadingStyle
  click node1 goToHeading "Validating Double Field Values"
  node1 -->|"Value is blank or missing"| node4["Accept value"]
  node1 -->|"Value is present"| node2{"Is value a valid number?"}
  node2 -->|"Yes"| node4
  node2 -->|"No"| node3["Building Error Messages for Validation"]:::HeadingStyle
  click node3 goToHeading "Building Error Messages for Validation"
  click node3 goToHeading "Resolving Message Arguments and Resource Bundles"
  click node3 goToHeading "Finalizing the ActionMessage for the Client"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Double Field Values"]:::HeadingStyle
%%   click node1 goToHeading "Validating Double Field Values"
%%   node1 -->|"Value is blank or missing"| node4["Accept value"]
%%   node1 -->|"Value is present"| node2{"Is value a valid number?"}
%%   node2 -->|"Yes"| node4
%%   node2 -->|"No"| node3["Building Error Messages for Validation"]:::HeadingStyle
%%   click node3 goToHeading "Building Error Messages for Validation"
%%   click node3 goToHeading "Resolving Message Arguments and Resource Bundles"
%%   click node3 goToHeading "Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for the Client"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Double Field Values

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node0["Extract value to validate from data"]
  click node0 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:767:768"
  node0 --> node1{"Is value blank or missing?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:773:774"
  node1 -->|"Yes"| node2["Accept: No value to validate (return TRUE)"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:774:775"
  node1 -->|"No"| node3{"Is value a valid number?"}
  click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:777:778"
  node3 -->|"Yes"| node4["Accept: Value is a valid number (return number)"]
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:784:785"
  node3 -->|"No"| node5["Reject: Value is not a valid number (return FALSE and report error)"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:779:783"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node0["Extract value to validate from data"]
%%   click node0 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:767:768"
%%   node0 --> node1{"Is value blank or missing?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:773:774"
%%   node1 -->|"Yes"| node2["Accept: No value to validate (return TRUE)"]
%%   click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:774:775"
%%   node1 -->|"No"| node3{"Is value a valid number?"}
%%   click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:777:778"
%%   node3 -->|"Yes"| node4["Accept: Value is a valid number (return number)"]
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:784:785"
%%   node3 -->|"No"| node5["Reject: Value is not a valid number (return FALSE and report error)"]
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:779:783"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="760">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="760:7:7" line-data="    public static Object validateDouble(Object bean, ValidatorAction va,">`validateDouble`</SwmToken> starts the flow by extracting the field value from the bean, checks for blank/null, and tries to parse it as a Double. If parsing fails, it adds an error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="781:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which is needed to generate a localized error for the client. The return type signals whether validation passed, failed, or produced a valid Double.

```java
    public static Object validateDouble(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "double", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        result = GenericTypeValidator.formatDouble(value);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building Error Messages for Validation

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, the function figures out which error message to show by checking for a Msg object on the field. If it's not a resource, it uses its key directly. Otherwise, it builds the message key and bundle, grabs the right resources and locale, and prepares arguments for formatting. This sets up the error message for the client, ready for localization.

```java
    public static ActionMessage getActionMessage(Validator validator,
        HttpServletRequest request, ValidatorAction va, Field field) {
        Msg msg = field.getMessage(va.getName());

        if ((msg != null) && !msg.isResource()) {
            return new ActionMessage(msg.getKey(), false);
        }

        String msgKey = null;
        String msgBundle = null;

        if (msg == null) {
            msgKey = va.getMsg();
        } else {
            msgKey = msg.getKey();
            msgBundle = msg.getBundle();
        }

        if ((msgKey == null) || (msgKey.length() == 0)) {
            return new ActionMessage("??? " + va.getName() + "."
                + field.getProperty() + " ???", false);
        }

        ServletContext application =
            (ServletContext) validator.getParameterValue(SERVLET_CONTEXT_PARAM);
        MessageResources messages =
            getMessageResources(application, request, msgBundle);
        Locale locale = RequestUtils.getUserLocale(request, null);

        Arg[] args = field.getArgs(va.getName());
        String[] argValues =
            getArgValues(application, request, messages, locale, args);

```

---

</SwmSnippet>

## Resolving Message Arguments and Resource Bundles

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are there arguments to process?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return null"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
    node1 -->|"Yes"| node3["Process arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
    
    subgraph loop1["For each argument"]
      node3 --> node4{"Is argument a resource?"}
      click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
      node4 -->|"Yes"| node5{"Custom bundle specified?"}
      click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
      node5 -->|"Yes"| node6["Resolve localized message from custom bundle"]
      click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:471:473"
      node5 -->|"No"| node7["Resolve localized message from default bundle"]
      click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:470"
      node4 -->|"No"| node8["Use direct value"]
      click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
    end
    node3 --> node9["Return array of display values"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return null"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Yes"| node3["Process arguments"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%     
%%     subgraph loop1["For each argument"]
%%       node3 --> node4{"Is argument a resource?"}
%%       click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%       node4 -->|"Yes"| node5{"Custom bundle specified?"}
%%       click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%       node5 -->|"Yes"| node6["Resolve localized message from custom bundle"]
%%       click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:471:473"
%%       node5 -->|"No"| node7["Resolve localized message from default bundle"]
%%       click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:470"
%%       node4 -->|"No"| node8["Use direct value"]
%%       click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%     end
%%     node3 --> node9["Return array of display values"]
%%     click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the Arg array, checks if each Arg needs localization, and fetches the string from the right bundle and locale if needed. If not, it just uses the key. It calls <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> to grab the bundle, so arguments can be localized or static as required.

```java
    private static String[] getArgValues(ServletContext application,
        HttpServletRequest request, MessageResources defaultMessages,
        Locale locale, Arg[] args) {
        if ((args == null) || (args.length == 0)) {
            return null;
        }

        String[] values = new String[args.length];

        for (int i = 0; i < args.length; i++) {
            if (args[i] != null) {
                if (args[i].isResource()) {
                    MessageResources messages = defaultMessages;

                    if (args[i].getBundle() != null) {
                        messages =
                            getMessageResources(application, request,
                                args[i].getBundle());
                    }

                    values[i] = messages.getMessage(locale, args[i].getKey());
                } else {
                    values[i] = args[i].getKey();
                }
            }
        }

        return values;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="117">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> tries to find the resource bundle in the request, then in the application scope with the module prefix, then just the bundle name. If none found, it throws. This fallback lets modules have their own bundles and avoids missing messages.

```java
    public static MessageResources getMessageResources(
        ServletContext application, HttpServletRequest request, String bundle) {
        if (bundle == null) {
            bundle = Globals.MESSAGES_KEY;
        }

        MessageResources resources =
            (MessageResources) request.getAttribute(bundle);

        if (resources == null) {
            ModuleConfig moduleConfig =
                ModuleUtils.getInstance().getModuleConfig(request, application);

            resources =
                (MessageResources) application.getAttribute(bundle
                    + moduleConfig.getPrefix());
        }

        if (resources == null) {
            resources = (MessageResources) application.getAttribute(bundle);
        }

        if (resources == null) {
            throw new NullPointerException(
                "No message resources found for bundle: " + bundle);
        }

        return resources;
    }
```

---

</SwmSnippet>

## Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for the Client

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="781:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after returning from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, the function either builds the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the message key and arguments or fetches a localized string from the bundle and uses that. This wraps up the error message, ready for the client, using the localized arguments we just resolved.

```java
        ActionMessage actionMessage = null;

        if (msgBundle == null) {
            actionMessage = new ActionMessage(msgKey, argValues);
        } else {
            String message = messages.getMessage(locale, msgKey, argValues);

            actionMessage = new ActionMessage(message, false);
        }

        return actionMessage;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
