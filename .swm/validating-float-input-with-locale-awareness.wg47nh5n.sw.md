---
title: Validating Float Input with Locale Awareness
---
This document describes how user input is validated as a floating-point number, taking into account locale-specific formatting. When a user submits a value, the system checks if it is blank or missing, and if not, verifies whether it is a valid float for the user's locale. If the input is invalid, a localized error message is constructed and presented to the user.

```mermaid
flowchart TD
  node1["Validating Float Input with Locale Awareness"]:::HeadingStyle
  click node1 goToHeading "Validating Float Input with Locale Awareness"
  node1 --> node2{"Is input blank or missing?"}
  node2 -->|"Yes"| node3["Validation passes"]
  node2 -->|"No"| node4{"Is input a valid float for user's locale?"}
  node4 -->|"Yes"| node3
  node4 -->|"No"| node5["Building the Error Message for the User"]:::HeadingStyle
  click node5 goToHeading "Building the Error Message for the User"
  node5 --> node6["Resolving Message Arguments and Resource Bundles"]:::HeadingStyle
  click node6 goToHeading "Resolving Message Arguments and Resource Bundles"
  node6 --> node7["Finalizing the ActionMessage for Display"]:::HeadingStyle
  click node7 goToHeading "Finalizing the ActionMessage for Display"
  node7 --> node8["User receives localized error message"]
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Float Input with Locale Awareness"]:::HeadingStyle
%%   click node1 goToHeading "Validating Float Input with Locale Awareness"
%%   node1 --> node2{"Is input blank or missing?"}
%%   node2 -->|"Yes"| node3["Validation passes"]
%%   node2 -->|"No"| node4{"Is input a valid float for user's locale?"}
%%   node4 -->|"Yes"| node3
%%   node4 -->|"No"| node5["Building the Error Message for the User"]:::HeadingStyle
%%   click node5 goToHeading "Building the Error Message for the User"
%%   node5 --> node6["Resolving Message Arguments and Resource Bundles"]:::HeadingStyle
%%   click node6 goToHeading "Resolving Message Arguments and Resource Bundles"
%%   node6 --> node7["Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for Display"]:::HeadingStyle
%%   click node7 goToHeading "Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for Display"
%%   node7 --> node8["User receives localized error message"]
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Float Input with Locale Awareness

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Retrieve user input value"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:722:724"
    node1 --> node2{"Did value retrieval fail?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:722:727"
    node2 -->|"Yes"| node3["Reject as invalid (return FALSE)"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:725:727"
    node2 -->|"No"| node4{"Is value blank or missing?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:729:731"
    node4 -->|"Yes"| node5["Accept as valid (return TRUE)"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:730:731"
    node4 -->|"No"| node6["Get user's locale"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:733:733"
    node6 --> node7{"Is value a valid float in user's locale?"}
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:735:737"
    node7 -->|"Yes"| node8["Accept as valid float (return parsed value)"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:742:742"
    node7 -->|"No"| node9["Record validation error and reject (return FALSE)"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:738:742"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Retrieve user input value"]
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:722:724"
%%     node1 --> node2{"Did value retrieval fail?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:722:727"
%%     node2 -->|"Yes"| node3["Reject as invalid (return FALSE)"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:725:727"
%%     node2 -->|"No"| node4{"Is value blank or missing?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:729:731"
%%     node4 -->|"Yes"| node5["Accept as valid (return TRUE)"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:730:731"
%%     node4 -->|"No"| node6["Get user's locale"]
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:733:733"
%%     node6 --> node7{"Is value a valid float in user's locale?"}
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:735:737"
%%     node7 -->|"Yes"| node8["Accept as valid float (return parsed value)"]
%%     click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:742:742"
%%     node7 -->|"No"| node9["Record validation error and reject (return FALSE)"]
%%     click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:738:742"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="716">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="716:7:7" line-data="    public static Object validateFloatLocale(Object bean, ValidatorAction va,">`validateFloatLocale`</SwmToken> checks if the input is blank/null, then tries to parse it as a float using the user's locale. If parsing fails, it adds a localized error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="739:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which is why we need to call Resources next—to generate the right feedback for the user.

```java
    public static Object validateFloatLocale(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "floatLocale", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        result = GenericTypeValidator.formatFloat(value, locale);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building the Error Message for the User

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use, check if it's a direct string or a resource, and prep everything needed to fetch the actual message text. Next, we need to call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> to resolve any arguments for the message, which might depend on the user's locale and the bundle setup.

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
  node1 -->|"No"| node2["Return no values"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
  node1 -->|"Yes"| node3["Resolve argument values"]
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:462:482"
  subgraph loop1["For each argument"]
    node3 --> node4{"Should value be localized?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:465:478"
    node4 -->|"Yes"| node5{"Use custom resource bundle?"}
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
    node5 -->|"Yes"| node6["Get display text from custom bundle"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
    node5 -->|"No"| node7["Get display text from default bundle"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:469"
    node6 --> node8["Add value to result"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:476"
    node7 --> node8
    node4 -->|"No"| node9["Add argument value directly to result"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
    node9 --> node8
    node8 --> node3
  end
  node3 --> node10["Return array of resolved values"]
  click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Are there arguments to process?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%   node1 -->|"No"| node2["Return no values"]
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%   node1 -->|"Yes"| node3["Resolve argument values"]
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:462:482"
%%   subgraph loop1["For each argument"]
%%     node3 --> node4{"Should value be localized?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:465:478"
%%     node4 -->|"Yes"| node5{"Use custom resource bundle?"}
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%     node5 -->|"Yes"| node6["Get display text from custom bundle"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%     node5 -->|"No"| node7["Get display text from default bundle"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:469"
%%     node6 --> node8["Add value to result"]
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:476"
%%     node7 --> node8
%%     node4 -->|"No"| node9["Add argument value directly to result"]
%%     click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%     node9 --> node8
%%     node8 --> node3
%%   end
%%   node3 --> node10["Return array of resolved values"]
%%   click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the message arguments, fetching their values from the right resource bundle and locale. If an argument is a resource, it grabs the message from the bundle, otherwise it just uses the key. We call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> to make sure we're pulling from the correct bundle, which might change depending on module context.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> tries to find the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> by checking the request, then the application with module prefix, then just the bundle name. If nothing is found, it throws. This covers modular setups and ensures we always get the right resource bundle for the message.

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

## Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for Display

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

We just got <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="401:12:12" line-data="            actionMessage = new ActionMessage(msgKey, argValues);">`argValues`</SwmToken> back from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, so now we build the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken>. If there's no bundle, we use the key and args directly; if there is, we fetch the full message from the bundle. This wraps up the message prep for display to the user.

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
