---
title: Validating Field Length and Providing Localized Feedback
---
This document outlines how the system validates that a field value does not exceed its maximum allowed length. If the value is too long, a localized error message is generated and returned alongside the validation result.

```mermaid
flowchart TD
  node1["Validating Field Length Constraints"]:::HeadingStyle
  click node1 goToHeading "Validating Field Length Constraints"
  node1 --> node2{"Is there a value to validate?"}
  node2 -->|"Yes"| node3{"Does value exceed maximum length?"}
  node3 -->|"Yes"| node4["Resolving and Formatting Validation Messages"]:::HeadingStyle
  click node4 goToHeading "Resolving and Formatting Validation Messages"
  node4 --> node5["Fetching Message Arguments and Resource Bundles"]:::HeadingStyle
  click node5 goToHeading "Fetching Message Arguments and Resource Bundles"
  node5 --> node6["Constructing the Final ActionMessage"]:::HeadingStyle
  click node6 goToHeading "Constructing the Final ActionMessage"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Field Length Constraints"]:::HeadingStyle
%%   click node1 goToHeading "Validating Field Length Constraints"
%%   node1 --> node2{"Is there a value to validate?"}
%%   node2 -->|"Yes"| node3{"Does value exceed maximum length?"}
%%   node3 -->|"Yes"| node4["Resolving and Formatting Validation Messages"]:::HeadingStyle
%%   click node4 goToHeading "Resolving and Formatting Validation Messages"
%%   node4 --> node5["Fetching Message Arguments and Resource Bundles"]:::HeadingStyle
%%   click node5 goToHeading "Fetching Message Arguments and Resource Bundles"
%%   node5 --> node6["Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"]:::HeadingStyle
%%   click node6 goToHeading "Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Field Length Constraints

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Check value length"] --> node2{"Is there a value to check?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1215:1252"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1221:1222"
    node2 -->|"Yes"| node3{"Is line end length provided?"}
    node2 -->|"No"| node7["Return success"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1229:1231"
    node3 -->|"No"| node4{"Is value length ≤ maximum allowed length?"}
    node3 -->|"Yes"| node5{"Is value length (with line end) ≤ maximum allowed length?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1232:1233"
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1234:1235"
    node4 -->|"Yes"| node7
    node4 -->|"No"| node6["Record error: value exceeds maximum allowed length"]
    node5 -->|"Yes"| node7
    node5 -->|"No"| node6
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1238:1242"
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1251:1252"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Check value length"] --> node2{"Is there a value to check?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1215:1252"
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1221:1222"
%%     node2 -->|"Yes"| node3{"Is line end length provided?"}
%%     node2 -->|"No"| node7["Return success"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1229:1231"
%%     node3 -->|"No"| node4{"Is value length ≤ maximum allowed length?"}
%%     node3 -->|"Yes"| node5{"Is value length (with line end) ≤ maximum allowed length?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1232:1233"
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1234:1235"
%%     node4 -->|"Yes"| node7
%%     node4 -->|"No"| node6["Record error: value exceeds maximum allowed length"]
%%     node5 -->|"Yes"| node7
%%     node5 -->|"No"| node6
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1238:1242"
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1251:1252"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1215">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1215:7:7" line-data="    public static boolean validateMaxLength(Object bean, ValidatorAction va,">`validateMaxLength`</SwmToken> checks if a field value meets the max length requirement, using parameters pulled from Resources for flexibility. If the value is too long, it adds an error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1240:1:3" line-data="                        Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which handles message formatting and localization. This is why we need to call Resources next—to resolve error messages and config values dynamically.

```java
    public static boolean validateMaxLength(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        String value = null;

        try {
            value = evaluateBean(bean, field);
            if (value != null) {
                String maxVar =
                    Resources.getVarValue("maxlength", field, validator,
                        request, true);
                int max = Integer.parseInt(maxVar);

                boolean isValid = false;
                String endLth = Resources.getVarValue("lineEndLength", field,
                    validator, request, false);
                if (GenericValidator.isBlankOrNull(endLth)) {
                    isValid = GenericValidator.maxLength(value, max);
                } else {
                    isValid = GenericValidator.maxLength(value, max,
                        Integer.parseInt(endLth));
                }

                if (!isValid) {
                    errors.add(field.getKey(),
                        Resources.getActionMessage(validator, request, va, field));

                    return false;
                }
            }
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "maxlength", e);

            return false;
        }

        return true;
    }
```

---

</SwmSnippet>

# Resolving and Formatting Validation Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error, check if it's a resource, and handle localization. If the key is missing, we return a placeholder message. We need to call further into Resources to fetch argument values and message resources for proper formatting and localization.

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

## Fetching Message Arguments and Resource Bundles

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are there any arguments to process?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return null"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
    node1 -->|"Yes"| node3["Begin resolving argument values"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:462:463"
    subgraph loop1["For each argument"]
        node3 --> node4{"Is argument a resource key?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
        node4 -->|"Yes"| node5{"Is custom bundle specified?"}
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
        node5 -->|"Yes"| node6["Get resource bundle"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:471:473"
        node6 --> node7["Resolve localized value (using locale and key)"]
        click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:475"
        node5 -->|"No"| node8["Use default bundle"]
        click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:468"
        node8 --> node7
        node4 -->|"No"| node9["Use literal value (key)"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
    end
    node3 -->|"After all arguments processed"| node10["Return resolved values"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there any arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return null"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Yes"| node3["Begin resolving argument values"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:462:463"
%%     subgraph loop1["For each argument"]
%%         node3 --> node4{"Is argument a resource key?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%         node4 -->|"Yes"| node5{"Is custom bundle specified?"}
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%         node5 -->|"Yes"| node6["Get resource bundle"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:471:473"
%%         node6 --> node7["Resolve localized value (using locale and key)"]
%%         click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:475"
%%         node5 -->|"No"| node8["Use default bundle"]
%%         click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:468"
%%         node8 --> node7
%%         node4 -->|"No"| node9["Use literal value (key)"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%     end
%%     node3 -->|"After all arguments processed"| node10["Return resolved values"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through message arguments, fetching localized values from the right resource bundle for each one. If an argument specifies a bundle, we grab <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="456:6:6" line-data="        HttpServletRequest request, MessageResources defaultMessages,">`MessageResources`</SwmToken> for it using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken>. This step ensures all message arguments are properly localized before formatting the final error message.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> grabs the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> by checking request attributes, then application attributes with module prefixes, and finally falls back to the global bundle. This lets Struts handle modular and global resource bundles, throwing if nothing is found.

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

## Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1240:3:3" line-data="                        Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after getting <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="401:12:12" line-data="            actionMessage = new ActionMessage(msgKey, argValues);">`argValues`</SwmToken> from Resources, we either build an <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and args or fetch the localized message string if a bundle is set. This wraps up the message resolution and formatting, ready for display or logging.

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
