---
title: Validating byte input with locale awareness
---
This document describes how form fields are validated as byte numbers, using the user's locale to interpret the input and generate localized error messages if validation fails. This process ensures validation is consistent with regional formatting and improves usability.

```mermaid
flowchart TD
  node1["Validating Byte Input with Locale Awareness
(Validating Byte Input with Locale Awareness)"]:::HeadingStyle
  click node1 goToHeading "Validating Byte Input with Locale Awareness"
  node1 --> node2{"Is field value blank or missing?"}
  node2 -->|"Yes"| node3["Validation successful
(Validating Byte Input with Locale Awareness)"]:::HeadingStyle
  click node3 goToHeading "Validating Byte Input with Locale Awareness"
  node2 -->|"No"| node4{"Is value valid byte according to locale?"}
  node4 -->|"Yes"| node3
  node4 -->|"No"| node5["Determining the Correct Error Message and Bundle"]:::HeadingStyle
  click node5 goToHeading "Determining the Correct Error Message and Bundle"
  node5 --> node6["Resolving Message Arguments and Resource Bundles"]:::HeadingStyle
  click node6 goToHeading "Resolving Message Arguments and Resource Bundles"
  node6 --> node7["Finalizing the ActionMessage for Display"]:::HeadingStyle
  click node7 goToHeading "Finalizing the ActionMessage for Display"
  node7 --> node8["Validation failed
(Validating Byte Input with Locale Awareness)"]:::HeadingStyle
  click node8 goToHeading "Validating Byte Input with Locale Awareness"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Byte Input with Locale Awareness
%% (Validating Byte Input with Locale Awareness)"]:::HeadingStyle
%%   click node1 goToHeading "Validating Byte Input with Locale Awareness"
%%   node1 --> node2{"Is field value blank or missing?"}
%%   node2 -->|"Yes"| node3["Validation successful
%% (Validating Byte Input with Locale Awareness)"]:::HeadingStyle
%%   click node3 goToHeading "Validating Byte Input with Locale Awareness"
%%   node2 -->|"No"| node4{"Is value valid byte according to locale?"}
%%   node4 -->|"Yes"| node3
%%   node4 -->|"No"| node5["Determining the Correct Error Message and Bundle"]:::HeadingStyle
%%   click node5 goToHeading "Determining the Correct Error Message and Bundle"
%%   node5 --> node6["Resolving Message Arguments and Resource Bundles"]:::HeadingStyle
%%   click node6 goToHeading "Resolving Message Arguments and Resource Bundles"
%%   node6 --> node7["Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for Display"]:::HeadingStyle
%%   click node7 goToHeading "Finalizing the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> for Display"
%%   node7 --> node8["Validation failed
%% (Validating Byte Input with Locale Awareness)"]:::HeadingStyle
%%   click node8 goToHeading "Validating Byte Input with Locale Awareness"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Byte Input with Locale Awareness

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Retrieve field value from form"] --> node2{"Did value retrieval succeed?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:361:362"
    node2 -->|"No"| node3["Record validation error and fail"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:363:365"
    node2 -->|"Yes"| node4{"Is value blank or missing?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:367:369"
    node4 -->|"Yes"| node5["Field passes validation"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:368:369"
    node4 -->|"No"| node6["Determine user's locale"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:371:371"
    node6 --> node7["Try to interpret value as a byte number using locale"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:373:373"
    node7 --> node8{"Is value a valid byte?"}
    click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:375:375"
    node8 -->|"Yes"| node9["Field passes validation"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:380:380"
    node8 -->|"No"| node10["Record validation error"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:376:378"
    node10 --> node11["Field fails validation"]
    click node11 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:380:380"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Retrieve field value from form"] --> node2{"Did value retrieval succeed?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:361:362"
%%     node2 -->|"No"| node3["Record validation error and fail"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:363:365"
%%     node2 -->|"Yes"| node4{"Is value blank or missing?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:367:369"
%%     node4 -->|"Yes"| node5["Field passes validation"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:368:369"
%%     node4 -->|"No"| node6["Determine user's locale"]
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:371:371"
%%     node6 --> node7["Try to interpret value as a byte number using locale"]
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:373:373"
%%     node7 --> node8{"Is value a valid byte?"}
%%     click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:375:375"
%%     node8 -->|"Yes"| node9["Field passes validation"]
%%     click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:380:380"
%%     node8 -->|"No"| node10["Record validation error"]
%%     click node10 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:376:378"
%%     node10 --> node11["Field fails validation"]
%%     click node11 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:380:380"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="354">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="354:7:7" line-data="    public static Object validateByteLocale(Object bean, ValidatorAction va,">`validateByteLocale`</SwmToken> starts the flow by evaluating the bean's field value, checking for blank/null, and formatting it as a byte using the user's locale. If formatting fails, it triggers an error and calls <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="377:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken> to generate a localized error message, which is then added to the errors collection.

```java
    public static Object validateByteLocale(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "byteLocale", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        result = GenericTypeValidator.formatByte(value, locale);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Determining the Correct Error Message and Bundle

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error, handle missing keys by returning a placeholder, fetch the localized resources and locale, and build the argument values for the message. This sets up everything needed to generate the right error message for the user.

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
  node1 -->|"Yes"| node3["Process each argument"]
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"

  subgraph loop1["For each argument"]
    node3 --> node4{"Is argument a resource?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
    node4 -->|"Yes"| node5{"Is a specific bundle specified?"}
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
    node5 -->|"Yes"| node6["Get localized value from specified bundle using key"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
    node5 -->|"No"| node7["Get localized value from default bundle using key"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:469"
    node4 -->|"No"| node8["Use argument's key as value"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
    node6 --> node3
    node7 --> node3
    node8 --> node3
  end
  node3 --> node9["Return array of values"]
  click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Are there arguments to process?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%   node1 -->|"No"| node2["Return null"]
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%   node1 -->|"Yes"| node3["Process each argument"]
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%% 
%%   subgraph loop1["For each argument"]
%%     node3 --> node4{"Is argument a resource?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%     node4 -->|"Yes"| node5{"Is a specific bundle specified?"}
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%     node5 -->|"Yes"| node6["Get localized value from specified bundle using key"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%     node5 -->|"No"| node7["Get localized value from default bundle using key"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:469"
%%     node4 -->|"No"| node8["Use argument's key as value"]
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%     node6 --> node3
%%     node7 --> node3
%%     node8 --> node3
%%   end
%%   node3 --> node9["Return array of values"]
%%   click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the argument array, resolving each as either a localized resource (using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> if a bundle is specified) or as a literal string. This produces the argument values needed for formatting the error message.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> looks up the message resources using the bundle name, first in the request, then in the application with the module prefix, and finally in the application with just the bundle name. If nothing is found, it throws an exception. <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="120:5:7" line-data="            bundle = Globals.MESSAGES_KEY;">`Globals.MESSAGES_KEY`</SwmToken> is used as a default if no bundle is specified.

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

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="377:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after getting the argument values, we either create an <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and arguments (if no bundle is specified) or with the fully resolved localized message (if a bundle is specified). This determines whether the error message is resolved now or later.

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
