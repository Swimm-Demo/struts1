---
title: Validating Numeric Field Range
---
This document describes how the system validates that a numeric field value is within a specified range. If the value is missing or within bounds, validation passes. If not, a localized error message is generated and presented to the user.

# Validating Numeric Range for Field Value

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node2{"Is value blank or null?"}
  click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:930:931"
  node2 -->|"Yes"| node6["Validation passes"]
  click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:957:958"
  node2 -->|"No"| node3{"Is minimum > maximum? (min, max)"}
  click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:939:942"
  node3 -->|"Yes"| node7["Configuration error: minimum greater than maximum"]
  click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:940:942"
  node7 --> node8["Validation fails"]
  click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:954:955"
  node3 -->|"No"| node4{"Is value within range? (min ≤ value ≤ max)"}
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:944:949"
  node4 -->|"Yes"| node6
  node4 -->|"No"| node5["Validation error: value out of range"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:945:949"
  node5 --> node8
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node2{"Is value blank or null?"}
%%   click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:930:931"
%%   node2 -->|"Yes"| node6["Validation passes"]
%%   click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:957:958"
%%   node2 -->|"No"| node3{"Is minimum > maximum? (min, max)"}
%%   click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:939:942"
%%   node3 -->|"Yes"| node7["Configuration error: minimum greater than maximum"]
%%   click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:940:942"
%%   node7 --> node8["Validation fails"]
%%   click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:954:955"
%%   node3 -->|"No"| node4{"Is value within range? (min ≤ value ≤ max)"}
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:944:949"
%%   node4 -->|"Yes"| node6
%%   node4 -->|"No"| node5["Validation error: value out of range"]
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:945:949"
%%   node5 --> node8
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="923">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="923:7:7" line-data="    public static boolean validateLongRange(Object bean, ValidatorAction va,">`validateLongRange`</SwmToken> checks if the field value is within the specified min/max range. If the value is out of bounds, it calls <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="946:1:3" line-data="                        Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken> to generate a localized error message for the client, which is then added to the errors collection. This is why we need to call <SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath> next—to build the actual message that gets shown to the user.

```java
    public static boolean validateLongRange(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        String value = null;

        try {
            value = evaluateBean(bean, field);
            if (!GenericValidator.isBlankOrNull(value)) {
                String minVar =
                    Resources.getVarValue("min", field, validator, request, true);
                String maxVar =
                    Resources.getVarValue("max", field, validator, request, true);
                long longValue = Long.parseLong(value);
                long min = Long.parseLong(minVar);
                long max = Long.parseLong(maxVar);
    
                if (min > max) {
                    throw new IllegalArgumentException(sysmsgs.getMessage(
                            "invalid.range", minVar, maxVar));
                }
    
                if (!GenericValidator.isInRange(longValue, min, max)) {
                    errors.add(field.getKey(),
                        Resources.getActionMessage(validator, request, va, field));
    
                    return false;
                }
            }
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "longRange", e);

            return false;
        }

        return true;
    }
```

---

</SwmSnippet>

# Building the Error Message for Validation Failure

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, the function figures out which message key and bundle to use for the error, grabs the relevant message resources and locale, and then prepares the arguments for the message. We need to call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> next to resolve any dynamic arguments for the error message.

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
    node1["Check if there are arguments to process"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No arguments"| node2["Return no values"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
    node1 -->|"Arguments exist"| node3["Process each argument"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"

    subgraph loop1["For each argument"]
        node3 --> node4{"Is argument a resource key?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:477"
        node4 -->|"Yes"| node5{"Is custom bundle specified?"}
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
        node5 -->|"Yes"| node6["Fetch message from custom bundle"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:471:473"
        node5 -->|"No"| node7["Fetch message from default bundle"]
        click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:470"
        node6 --> node8["Store resolved value"]
        click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:476"
        node7 --> node8
        node4 -->|"No"| node9["Use literal value"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
        node9 --> node8
        node8 --> node3
    end
    node3 --> node10["Return resolved values"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Check if there are arguments to process"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No arguments"| node2["Return no values"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Arguments exist"| node3["Process each argument"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%% 
%%     subgraph loop1["For each argument"]
%%         node3 --> node4{"Is argument a resource key?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:477"
%%         node4 -->|"Yes"| node5{"Is custom bundle specified?"}
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%         node5 -->|"Yes"| node6["Fetch message from custom bundle"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:471:473"
%%         node5 -->|"No"| node7["Fetch message from default bundle"]
%%         click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:470"
%%         node6 --> node8["Store resolved value"]
%%         click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:476"
%%         node7 --> node8
%%         node4 -->|"No"| node9["Use literal value"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%         node9 --> node8
%%         node8 --> node3
%%     end
%%     node3 --> node10["Return resolved values"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the message arguments, resolving each one from the correct resource bundle and locale. If an argument is a resource, it fetches the message from the bundle, using a fallback mechanism to check request and application scopes. We need to call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> next to actually retrieve the bundle from the right context.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> tries to find the message bundle in the request scope first, then in the application scope using the module prefix, and finally just with the bundle key. If nothing is found, it throws an error. This fallback lets the app support both global and module-specific resources.

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

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="946:3:3" line-data="                        Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after getting <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="401:12:12" line-data="            actionMessage = new ActionMessage(msgKey, argValues);">`argValues`</SwmToken> from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, the function either builds the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and args or resolves the message string directly if a bundle is specified. This result is returned to the caller for use in error reporting.

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
