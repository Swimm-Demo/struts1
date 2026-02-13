---
title: Validating Locale-Specific Numeric Input
---
This document describes how user input for numeric fields is validated to ensure values are interpreted as numbers according to the user's locale. If the input is invalid, a localized error message is provided to support international users.

```mermaid
flowchart TD
  node1["Validating Locale-Specific Double Values"]:::HeadingStyle
  click node1 goToHeading "Validating Locale-Specific Double Values"
  node1 -->|"Input is blank or null"| node2["Accept as valid"]
  node1 -->|"Input is not blank or null"| node3{"Is input valid according to locale?"}
  node3 -->|"Yes"| node2
  node3 -->|"No"| node4["Building the Validation Error Message"]:::HeadingStyle
  click node4 goToHeading "Building the Validation Error Message"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Locale-Specific Double Values

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Get value to validate"] --> node2{"Is value retrieval successful?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:809:810"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:808:813"
    node2 -->|"No"| node3["Record failure and return invalid"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:811:813"
    node2 -->|"Yes"| node4{"Is value blank or null?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:815:817"
    node4 -->|"Yes"| node5["Accept as valid"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:816:817"
    node4 -->|"No"| node6["Determine user's locale"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:819:820"
    node6 --> node7["Validate number format according to locale"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:821:821"
    node7 --> node8{"Is value a valid number?"}
    click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:823:828"
    node8 -->|"Yes"| node9["Return parsed number"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:828:829"
    node8 -->|"No"| node10["Record validation error and return invalid"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:824:828"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Get value to validate"] --> node2{"Is value retrieval successful?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:809:810"
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:808:813"
%%     node2 -->|"No"| node3["Record failure and return invalid"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:811:813"
%%     node2 -->|"Yes"| node4{"Is value blank or null?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:815:817"
%%     node4 -->|"Yes"| node5["Accept as valid"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:816:817"
%%     node4 -->|"No"| node6["Determine user's locale"]
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:819:820"
%%     node6 --> node7["Validate number format according to locale"]
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:821:821"
%%     node7 --> node8{"Is value a valid number?"}
%%     click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:823:828"
%%     node8 -->|"Yes"| node9["Return parsed number"]
%%     click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:828:829"
%%     node8 -->|"No"| node10["Record validation error and return invalid"]
%%     click node10 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:824:828"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="802">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="802:7:7" line-data="    public static Object validateDoubleLocale(Object bean, ValidatorAction va,">`validateDoubleLocale`</SwmToken> checks if a field value can be parsed as a double using the user's locale. If parsing fails, it adds an error message by calling <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="825:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which handles message formatting and localization. This is the entry point for validation and error reporting.

```java
    public static Object validateDoubleLocale(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "doubleLocale", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        result = GenericTypeValidator.formatDouble(value, locale);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building the Validation Error Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error, grab the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="390:1:1" line-data="        MessageResources messages =">`MessageResources`</SwmToken> for localization, and prepare the arguments for the message. Next, we need to resolve the argument values, which might involve more resource lookups.

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
    node1{"Are there any arguments to process?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return no values"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
    node1 -->|"Yes"| node3["Prepare user-facing text for arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
    subgraph loop1["For each argument"]
        node3 --> node4{"Is argument a resource key?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
        node4 -->|"Yes"| node5{"Is custom bundle specified?"}
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
        node5 -->|"Yes"| node6["Get localized user-facing text from custom bundle (using locale)"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
        node5 -->|"No"| node7["Get localized user-facing text from default bundle (using locale)"]
        click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:469"
        node6 --> node8["Add text to result"]
        node7 --> node8
        node4 -->|"No"| node9["Use literal value as user-facing text"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
        node9 --> node8
        node8 --> node3
    end
    node3 --> node10["Return all user-facing texts"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there any arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return no values"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Yes"| node3["Prepare user-facing text for arguments"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%     subgraph loop1["For each argument"]
%%         node3 --> node4{"Is argument a resource key?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%         node4 -->|"Yes"| node5{"Is custom bundle specified?"}
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%         node5 -->|"Yes"| node6["Get localized user-facing text from custom bundle (using locale)"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%         node5 -->|"No"| node7["Get localized user-facing text from default bundle (using locale)"]
%%         click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:469"
%%         node6 --> node8["Add text to result"]
%%         node7 --> node8
%%         node4 -->|"No"| node9["Use literal value as user-facing text"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%         node9 --> node8
%%         node8 --> node3
%%     end
%%     node3 --> node10["Return all user-facing texts"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the message arguments, resolving each one using the right resource bundle if needed. It calls <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> to make sure each argument is localized correctly. This sets up the argument values for the final error message.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> looks for the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> bundle by checking the request, then the application with a module prefix, and finally the application globally. If nothing is found, it throws. This fallback lets modules have their own messages or use shared ones.

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

## Finalizing and Returning the Action Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="825:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, now that we have the argument values, we either create an <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and args (if no bundle) or with the fully formatted message (if a bundle is set). This wraps up the error message creation before returning it.

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
