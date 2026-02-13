---
title: Validating Short Number Input with Localization
---
This document describes how user input is validated as a short number, using the user's locale to ensure correct formatting and feedback. Blank or null values are accepted, while invalid entries result in a localized error message tailored for the user interface.

```mermaid
flowchart TD
  node1["Validating Short Format Input
(Validating Short Format Input)"]:::HeadingStyle
  click node1 goToHeading "Validating Short Format Input"
  node1 -->|"Input is blank or null"| node2["Accept input
(Validating Short Format Input)"]:::HeadingStyle
  click node2 goToHeading "Validating Short Format Input"
  node1 -->|"Input is valid short number"| node3["Accept and format input
(Validating Short Format Input)"]:::HeadingStyle
  click node3 goToHeading "Validating Short Format Input"
  node1 -->|"Input is invalid short number"| node4["Resolving the Error Message"]:::HeadingStyle
  click node4 goToHeading "Resolving the Error Message"
  node4 --> node5["Building the Final ActionMessage"]:::HeadingStyle
  click node5 goToHeading "Building the Final ActionMessage"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% flowchart TD
%%   node1["Validating Short Format Input
%% (Validating Short Format Input)"]:::HeadingStyle
%%   click node1 goToHeading "Validating Short Format Input"
%%   node1 -->|"Input is blank or null"| node2["Accept input
%% (Validating Short Format Input)"]:::HeadingStyle
%%   click node2 goToHeading "Validating Short Format Input"
%%   node1 -->|"Input is valid short number"| node3["Accept and format input
%% (Validating Short Format Input)"]:::HeadingStyle
%%   click node3 goToHeading "Validating Short Format Input"
%%   node1 -->|"Input is invalid short number"| node4["Resolving the Error Message"]:::HeadingStyle
%%   click node4 goToHeading "Resolving the Error Message"
%%   node4 --> node5["Building the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"]:::HeadingStyle
%%   click node5 goToHeading "Building the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Short Format Input

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start: Validate input value for short number"] --> node2{"Error evaluating input value?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:458:465"
  node2 -->|"Yes"| node3["Return invalid (evaluation failed)"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:464:469"
  click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:467:469"
  node2 -->|"No"| node4{"Is input value blank or null?"}
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:471:473"
  node4 -->|"Yes"| node5["Return valid (blank/null allowed)"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:472:473"
  node4 -->|"No"| node6["Validate input as short number using user's locale"]
  click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:475:477"
  node6 --> node7{"Is value a valid short number?"}
  click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:479:484"
  node7 -->|"Yes"| node8["Return formatted short number"]
  click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:484:485"
  node7 -->|"No"| node9["Record error"]
  click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:480:482"
  node9 --> node10["Return invalid (not a short number)"]
  click node10 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:484:485"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start: Validate input value for short number"] --> node2{"Error evaluating input value?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:458:465"
%%   node2 -->|"Yes"| node3["Return invalid (evaluation failed)"]
%%   click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:464:469"
%%   click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:467:469"
%%   node2 -->|"No"| node4{"Is input value blank or null?"}
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:471:473"
%%   node4 -->|"Yes"| node5["Return valid (blank/null allowed)"]
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:472:473"
%%   node4 -->|"No"| node6["Validate input as short number using user's locale"]
%%   click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:475:477"
%%   node6 --> node7{"Is value a valid short number?"}
%%   click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:479:484"
%%   node7 -->|"Yes"| node8["Return formatted short number"]
%%   click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:484:485"
%%   node7 -->|"No"| node9["Record error"]
%%   click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:480:482"
%%   node9 --> node10["Return invalid (not a short number)"]
%%   click node10 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:484:485"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="458">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="458:7:7" line-data="    public static Object validateShortLocale(Object bean, ValidatorAction va,">`validateShortLocale`</SwmToken> checks if the input value can be parsed as a short for the user's locale. If parsing fails, it adds a localized error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="481:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>. This is the entry point for validation, and we call into Resources to generate the correct error message for the UI.

```java
    public static Object validateShortLocale(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "shortLocale", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        result = GenericTypeValidator.formatShort(value, locale);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Resolving the Error Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error, check if it's a direct message or a resource, and set up everything needed for localization. We call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> next to resolve any arguments for the message, since error messages often need to include field names or values.

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

## Resolving Message Arguments

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are there arguments to process?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return null"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:459"
    node1 -->|"Yes"| node3["Process arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
    
    subgraph loop1["For each argument"]
      node3 --> node4{"Should value be localized?"}
      click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:478"
      node4 -->|"Yes"| node5{"Custom bundle specified?"}
      click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
      node5 -->|"Yes"| node6["Get message resources for bundle"]
      click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:471:473"
      node6 --> node7["Show localized label"]
      click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:475"
      node5 -->|"No"| node7
      node4 -->|"No"| node8["Show literal value"]
      click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:477"
    end
    node3 --> node9["Return array of values"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:482"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return null"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:459"
%%     node1 -->|"Yes"| node3["Process arguments"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%     
%%     subgraph loop1["For each argument"]
%%       node3 --> node4{"Should value be localized?"}
%%       click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:478"
%%       node4 -->|"Yes"| node5{"Custom bundle specified?"}
%%       click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%       node5 -->|"Yes"| node6["Get message resources for bundle"]
%%       click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:471:473"
%%       node6 --> node7["Show localized label"]
%%       click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:475"
%%       node5 -->|"No"| node7
%%       node4 -->|"No"| node8["Show literal value"]
%%       click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:477"
%%     end
%%     node3 --> node9["Return array of values"]
%%     click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:482"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the message arguments, resolving each one from the correct bundle if it's a resource. If an argument points to a different bundle, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="471:1:1" line-data="                            getMessageResources(application, request,">`getMessageResources`</SwmToken> to fetch it. This sets up the actual values to be inserted into the error message.

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

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> looks for the message bundle in several places: first in the request, then in the application with a module prefix, and finally just by the bundle key. If it can't find anything, it throws. This supports modular setups and lets you override resources per module or request.

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

## Building the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is a message bundle provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:406"
    node1 -->|"No"| node2["Create action message using default message key and arguments"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:401"
    node1 -->|"Yes"| node3["Retrieve localized message using message key and arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:403"
    node3 --> node4["Create action message with localized message"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:405:405"
    node2 --> node5["Return action message to user"]
    node4 --> node5
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:408"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is a message bundle provided?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:406"
%%     node1 -->|"No"| node2["Create action message using default message key and arguments"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:401"
%%     node1 -->|"Yes"| node3["Retrieve localized message using message key and arguments"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:403"
%%     node3 --> node4["Create action message with localized message"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:405:405"
%%     node2 --> node5["Return action message to user"]
%%     node4 --> node5
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:408"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="481:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, now that we've got the argument values, we either build an <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and args (if no bundle) or with the fully resolved message string (if a bundle is set). This is the last step before returning the message for display.

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
