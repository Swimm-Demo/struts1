---
title: Validating numeric input with locale awareness
---
This document describes how numeric input from users is validated as long numbers, taking into account locale-specific formatting. The flow ensures that values are interpreted correctly based on the user's locale and provides a localized error message if validation fails.

```mermaid
flowchart TD
  node1["Validating Long Values with Locale Awareness"]:::HeadingStyle
  click node1 goToHeading "Validating Long Values with Locale Awareness"
  node1 --> node2{"Is input blank or missing?"}
  node2 -->|"Yes"| node5["Input is valid"]
  node2 -->|"No"| node3{"Is input a valid long number?"}
  node3 -->|"Yes"| node5
  node3 -->|"No"| node4["Resolving and Formatting Validation Messages"]:::HeadingStyle
  click node4 goToHeading "Resolving and Formatting Validation Messages"
  node4 --> node6["Constructing the Final ActionMessage"]:::HeadingStyle
  click node6 goToHeading "Constructing the Final ActionMessage"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Long Values with Locale Awareness"]:::HeadingStyle
%%   click node1 goToHeading "Validating Long Values with Locale Awareness"
%%   node1 --> node2{"Is input blank or missing?"}
%%   node2 -->|"Yes"| node5["Input is valid"]
%%   node2 -->|"No"| node3{"Is input a valid long number?"}
%%   node3 -->|"Yes"| node5
%%   node3 -->|"No"| node4["Resolving and Formatting Validation Messages"]:::HeadingStyle
%%   click node4 goToHeading "Resolving and Formatting Validation Messages"
%%   node4 --> node6["Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"]:::HeadingStyle
%%   click node6 goToHeading "Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Long Values with Locale Awareness

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Obtain user input value"] --> node2{"Error getting value?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:637:638"
  node2 -->|"Yes"| node3["Input is invalid"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:636:641"
  click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:640:641"
  node2 -->|"No"| node4{"Is input blank or missing?"}
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:643:645"
  node4 -->|"Yes"| node5["Input is valid"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:644:645"
  node4 -->|"No"| node6["Parse input as long using user's locale"]
  click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:647:649"
  node6 --> node7{"Is input a valid long?"}
  click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:651:654"
  node7 -->|"Yes"| node8["Input is valid"]
  click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:656:657"
  node7 -->|"No"| node9["Record error"]
  click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:652:654"
  node9 --> node10["Input is invalid"]
  click node10 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:656:657"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Obtain user input value"] --> node2{"Error getting value?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:637:638"
%%   node2 -->|"Yes"| node3["Input is invalid"]
%%   click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:636:641"
%%   click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:640:641"
%%   node2 -->|"No"| node4{"Is input blank or missing?"}
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:643:645"
%%   node4 -->|"Yes"| node5["Input is valid"]
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:644:645"
%%   node4 -->|"No"| node6["Parse input as long using user's locale"]
%%   click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:647:649"
%%   node6 --> node7{"Is input a valid long?"}
%%   click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:651:654"
%%   node7 -->|"Yes"| node8["Input is valid"]
%%   click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:656:657"
%%   node7 -->|"No"| node9["Record error"]
%%   click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:652:654"
%%   node9 --> node10["Input is invalid"]
%%   click node10 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:656:657"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="630">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="630:7:7" line-data="    public static Object validateLongLocale(Object bean, ValidatorAction va,">`validateLongLocale`</SwmToken> starts the flow by evaluating the bean property, checking for blank/null, and formatting the value as a long using the user's locale. If parsing fails, it adds a localized error message by calling <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="653:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which is why we need to jump to that function next.

```java
    public static Object validateLongLocale(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "longLocale", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        result = GenericTypeValidator.formatLong(value, locale);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Resolving and Formatting Validation Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use based on the Field and <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="366:6:6" line-data="        HttpServletRequest request, ValidatorAction va, Field field) {">`ValidatorAction`</SwmToken>. If the Msg isn't a resource, we return its key. Otherwise, we resolve the key and bundle, handle missing keys with a placeholder, grab message resources and locale, and prep argument values for formatting. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> to resolve those arguments.

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
    node1 -->|"Yes"| node3["Process each argument"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
    
    subgraph loop1["For each argument"]
        node3 --> node4{"Is argument a resource key?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
        node4 -->|"Resource key"| node5{"Is a custom bundle specified?"}
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
        node5 -->|"Yes"| node6["Lookup value in custom bundle (locale)"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:471:473"
        node5 -->|"No"| node7["Lookup value in default bundle (locale)"]
        click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:470"
        node6 --> node8["Assign value"]
        node7 --> node8
        node4 -->|"Literal value"| node9["Use literal value"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
        node9 --> node8
        node8 --> node3
    end
    node3 --> node10["Return values"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there any arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return no values"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Yes"| node3["Process each argument"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%     
%%     subgraph loop1["For each argument"]
%%         node3 --> node4{"Is argument a resource key?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%         node4 -->|"Resource key"| node5{"Is a custom bundle specified?"}
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%         node5 -->|"Yes"| node6["Lookup value in custom bundle (locale)"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:471:473"
%%         node5 -->|"No"| node7["Lookup value in default bundle (locale)"]
%%         click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:470"
%%         node6 --> node8["Assign value"]
%%         node7 --> node8
%%         node4 -->|"Literal value"| node9["Use literal value"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%         node9 --> node8
%%         node8 --> node3
%%     end
%%     node3 --> node10["Return values"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the argument array, resolving each one either from a resource bundle (using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> if a bundle is specified) or directly from the key. This ensures all arguments are localized or static as needed before formatting the message.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> grabs the resource bundle by checking the request, then the application with a module prefix, then just the bundle name. If nothing is found, it throws. Using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="120:5:7" line-data="            bundle = Globals.MESSAGES_KEY;">`Globals.MESSAGES_KEY`</SwmToken> as a default ensures there's always a fallback for message lookups.

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

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="653:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after resolving argument values, we build the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken>. If there's no bundle, we use the key and args; if there is, we fetch the localized message and mark it as not a key. This lets us handle both deferred and immediate message resolution depending on context.

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
