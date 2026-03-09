---
title: Validating Short Integer Input
---
This document describes how a field value is validated to ensure it is a short integer. If the value is invalid, the system generates a localized error message for the user, ensuring feedback is clear and in the user's language.

```mermaid
flowchart TD
  node1["Validating Short Integer Input"]:::HeadingStyle
  click node1 goToHeading "Validating Short Integer Input"
  node1 --> node2{"Is value a valid short integer?"}
  node2 -->|"Yes"| node3["Validation passes"]
  node2 -->|"No"| node4["Building the Error Message Object"]:::HeadingStyle
  click node4 goToHeading "Building the Error Message Object"
  node4 --> node5["Finalizing the Action Message"]:::HeadingStyle
  click node5 goToHeading "Finalizing the Action Message"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Short Integer Input

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Retrieve value to validate"] --> node2{"Exception during retrieval?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:423:424"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:422:427"
    node2 -->|"Yes"| node3["Mark as invalid"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:425:427"
    node2 -->|"No"| node4{"Is value blank or null?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:429:431"
    node4 -->|"Yes"| node5["Return valid (blank/null)"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:430:431"
    node4 -->|"No"| node6{"Is value a valid short integer?"}
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:433:435"
    node6 -->|"Yes"| node7["Return valid (short value)"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:440:441"
    node6 -->|"No"| node8["Record validation error"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:436:438"
    node8 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Retrieve value to validate"] --> node2{"Exception during retrieval?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:423:424"
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:422:427"
%%     node2 -->|"Yes"| node3["Mark as invalid"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:425:427"
%%     node2 -->|"No"| node4{"Is value blank or null?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:429:431"
%%     node4 -->|"Yes"| node5["Return valid (blank/null)"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:430:431"
%%     node4 -->|"No"| node6{"Is value a valid short integer?"}
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:433:435"
%%     node6 -->|"Yes"| node7["Return valid (short value)"]
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:440:441"
%%     node6 -->|"No"| node8["Record validation error"]
%%     click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:436:438"
%%     node8 --> node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="416">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="416:7:7" line-data="    public static Object validateShort(Object bean, ValidatorAction va,">`validateShort`</SwmToken> checks if a field value can be parsed as a Short. If parsing fails, it adds a localized error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="437:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, which is why we need to call into Resources next—to fetch the right error message for the user.

```java
    public static Object validateShort(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "short", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        result = GenericTypeValidator.formatShort(value);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building the Error Message Object

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Determine custom message for validation"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:365:367"
    node1 --> node2{"Is custom message present and not a
resource?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:369:371"
    node2 -->|"Yes"| node3["Show custom message to user"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:370:371"
    node2 -->|"No"| node4{"Is message key missing or empty?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:383:386"
    node4 -->|"Yes"| node5["Show generic error message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:384:386"
    node4 -->|"No"| node6{"Is bundle specified?"}
    
    node6 -->|"No"| node7["Show localized message with arguments"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:402"
    node6 -->|"Yes"| node8["Show localized message from bundle"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:405"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Finalizing the Action Message"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Determine custom message for validation"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:365:367"
%%     node1 --> node2{"Is custom message present and not a
%% resource?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:369:371"
%%     node2 -->|"Yes"| node3["Show custom message to user"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:370:371"
%%     node2 -->|"No"| node4{"Is message key missing or empty?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:383:386"
%%     node4 -->|"Yes"| node5["Show generic error message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:384:386"
%%     node4 -->|"No"| node6{"Is bundle specified?"}
%%     
%%     node6 -->|"No"| node7["Show localized message with arguments"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:402"
%%     node6 -->|"Yes"| node8["Show localized message from bundle"]
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:405"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Finalizing the Action Message"
%% node6:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error, then fetch the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="390:1:1" line-data="        MessageResources messages =">`MessageResources`</SwmToken> object so we can look up the actual localized message text. That's why we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="391:1:1" line-data="            getMessageResources(application, request, msgBundle);">`getMessageResources`</SwmToken> next.

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
```

---

</SwmSnippet>

## Locating the Message Resource Bundle

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="117">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, we try to find the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> object by checking the request, then the application with a module prefix, then just the application. If we need a module prefix, we call ModuleUtils.getModuleConfig to get it.

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

```

---

</SwmSnippet>

### Resolving the Module Configuration

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="130">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:5:5" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`getModuleConfig`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:7:7" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`HttpServletRequest`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="131:1:1" line-data="        ServletContext context) {">`ServletContext`</SwmToken>`)` checks for a <SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:3:3" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`ModuleConfig`</SwmToken> in the request, and if not found, pulls it from the context and attaches it to the request for later use.

```java
    public ModuleConfig getModuleConfig(HttpServletRequest request,
        ServletContext context) {
        ModuleConfig moduleConfig = this.getModuleConfig(request);

        if (moduleConfig == null) {
            moduleConfig = this.getModuleConfig("", context);
            request.setAttribute(Globals.MODULE_KEY, moduleConfig);
        }

        return moduleConfig;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="89">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:5:5" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`getModuleConfig`</SwmToken>`(`<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:7:7" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`String`</SwmToken>`, `<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:12:12" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`ServletContext`</SwmToken>`)` grabs the <SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:3:3" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`ModuleConfig`</SwmToken> from the context, using just the base key for the default module or appending the prefix for others. This relies on a naming convention for context attributes.

```java
    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {
        if ((prefix == null) || "/".equals(prefix)) {
            return (ModuleConfig) context.getAttribute(Globals.MODULE_KEY);
        } else {
            return (ModuleConfig) context.getAttribute(Globals.MODULE_KEY
                + prefix);
        }
    }
```

---

</SwmSnippet>

### Finalizing Resource Lookup

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are message resources already
available?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:135:137"
    node1 -->|"No"| node2["Retrieve resources from application
context for bundle"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:136:137"
    node1 -->|"Yes"| node3{"Are resources found for bundle?"}
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:139:142"
    node2 --> node3
    node3 -->|"Yes"| node4["Return message resources"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:144:145"
    node3 -->|"No"| node5["Throw error: No message resources found
for bundle '<bundle>' "]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:140:142"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are message resources already
%% available?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:135:137"
%%     node1 -->|"No"| node2["Retrieve resources from application
%% context for bundle"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:136:137"
%%     node1 -->|"Yes"| node3{"Are resources found for bundle?"}
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:139:142"
%%     node2 --> node3
%%     node3 -->|"Yes"| node4["Return message resources"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:144:145"
%%     node3 -->|"No"| node5["Throw error: No message resources found
%% for bundle '<bundle>' "]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:140:142"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="135">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, after getting the module prefix from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="128:1:1" line-data="                ModuleUtils.getInstance().getModuleConfig(request, application);">`ModuleUtils`</SwmToken>, we try the application scope with and without the prefix. If nothing is found, we throw a <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="140:5:5" line-data="            throw new NullPointerException(">`NullPointerException`</SwmToken>. This covers all the fallback steps for resource lookup.

```java
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

## Preparing Locale and Arguments

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="392">

---

After getting <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken>, <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="437:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken> grabs the user's locale and the argument objects for the message. Next, we need to resolve these arguments into display values, so we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="394:11:11" line-data="        Arg[] args = field.getArgs(va.getName());">`getArgs`</SwmToken>.

```java
        Locale locale = RequestUtils.getUserLocale(request, null);

        Arg[] args = field.getArgs(va.getName());
```

---

</SwmSnippet>

## Resolving Argument Placeholders

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start preparing user-facing argument
messages"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:420:423"
    
    subgraph loop1["For each argument position (0-3)"]
        node1 --> node2{"Is argument present?"}
        click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:433"
        node2 -->|"No"| node9["Skip this argument"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:432:433"
        node2 -->|"Yes"| node3{"Is argument a resource key?"}
        click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:435:439"
        node3 -->|"Resource key"| node4["Resolve to localized message"]
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:436:436"
        node3 -->|"Literal value"| node5["Use literal value as message"]
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:438:439"
        node4 --> node6["Assign resolved message"]
        node5 --> node6
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:436:439"
        node6 --> node8{"More arguments?"}
        node9 --> node8
        click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:440"
    end
    node8 -->|"No"| node7["Return argument messages"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:442:443"
    node8 -->|"Yes"| node2
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start preparing user-facing argument
%% messages"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:420:423"
%%     
%%     subgraph loop1["For each argument position (0-3)"]
%%         node1 --> node2{"Is argument present?"}
%%         click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:433"
%%         node2 -->|"No"| node9["Skip this argument"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:432:433"
%%         node2 -->|"Yes"| node3{"Is argument a resource key?"}
%%         click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:435:439"
%%         node3 -->|"Resource key"| node4["Resolve to localized message"]
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:436:436"
%%         node3 -->|"Literal value"| node5["Use literal value as message"]
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:438:439"
%%         node4 --> node6["Assign resolved message"]
%%         node5 --> node6
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:436:439"
%%         node6 --> node8{"More arguments?"}
%%         node9 --> node8
%%         click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:440"
%%     end
%%     node8 -->|"No"| node7["Return argument messages"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:442:443"
%%     node8 -->|"Yes"| node2
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="420">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="420:9:9" line-data="    public static String[] getArgs(String actionName,">`getArgs`</SwmToken> pulls up to 4 arguments from the field, localizing each if needed using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="436:8:8" line-data="                argMessages[i] = getMessage(messages, locale, args[i].getKey());">`getMessage`</SwmToken>. This means we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="436:8:8" line-data="                argMessages[i] = getMessage(messages, locale, args[i].getKey());">`getMessage`</SwmToken> for any argument marked as a resource.

```java
    public static String[] getArgs(String actionName,
        MessageResources messages, Locale locale, Field field) {
        String[] argMessages = new String[4];

        Arg[] args =
            new Arg[] {
                field.getArg(actionName, 0), field.getArg(actionName, 1),
                field.getArg(actionName, 2), field.getArg(actionName, 3)
            };

        for (int i = 0; i < args.length; i++) {
            if (args[i] == null) {
                continue;
            }

            if (args[i].isResource()) {
                argMessages[i] = getMessage(messages, locale, args[i].getKey());
            } else {
                argMessages[i] = args[i].getKey();
            }
        }

        return argMessages;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="231">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="231:7:7" line-data="    public static String getMessage(MessageResources messages, Locale locale,">`getMessage`</SwmToken> just fetches a localized string for a key from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="231:9:9" line-data="    public static String getMessage(MessageResources messages, Locale locale,">`MessageResources`</SwmToken>. If nothing is found, it returns an empty string, not null.

```java
    public static String getMessage(MessageResources messages, Locale locale,
        String key) {
        String message = null;

        if (messages != null) {
            message = messages.getMessage(locale, key);
        }

        return (message == null) ? "" : message;
    }
```

---

</SwmSnippet>

## Finalizing the Action Message

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Gather argument values (argValues) for
message template (msgKey)"] --> node2{"Is a specific message bundle (msgBundle)
provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:395:396"
    node2 -->|"No"| node3["Create action message using msgKey and
argValues"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:402"
    node2 -->|"Yes"| node4["Resolve localized message string using
msgBundle, locale, msgKey, and argValues"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:404"
    node4 --> node5["Create action message with resolved,
localized message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:405:405"
    node3 --> node6["Return action message"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:409"
    node5 --> node6["Return action message"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:409"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Gather argument values (<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="395:5:5" line-data="        String[] argValues =">`argValues`</SwmToken>) for
%% message template (<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="373:3:3" line-data="        String msgKey = null;">`msgKey`</SwmToken>)"] --> node2{"Is a specific message bundle (<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="374:3:3" line-data="        String msgBundle = null;">`msgBundle`</SwmToken>)
%% provided?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:395:396"
%%     node2 -->|"No"| node3["Create action message using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="373:3:3" line-data="        String msgKey = null;">`msgKey`</SwmToken> and
%% <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="395:5:5" line-data="        String[] argValues =">`argValues`</SwmToken>"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:402"
%%     node2 -->|"Yes"| node4["Resolve localized message string using
%% <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="374:3:3" line-data="        String msgBundle = null;">`msgBundle`</SwmToken>, locale, <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="373:3:3" line-data="        String msgKey = null;">`msgKey`</SwmToken>, and <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="395:5:5" line-data="        String[] argValues =">`argValues`</SwmToken>"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:404"
%%     node4 --> node5["Create action message with resolved,
%% localized message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:405:405"
%%     node3 --> node6["Return action message"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:409"
%%     node5 --> node6["Return action message"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:409"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="395">

---

After getting the argument values from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, we build the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken>. If there's a bundle, we resolve the message with the arguments first; otherwise, we just pass the key and values to the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:5:5" line-data="    public static ActionMessage getActionMessage(Validator validator,">`ActionMessage`</SwmToken> constructor.

```java
        String[] argValues =
            getArgValues(application, request, messages, locale, args);

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken> loops through the Arg objects, and for each resource-type argument, it fetches the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="456:6:6" line-data="        HttpServletRequest request, MessageResources defaultMessages,">`MessageResources`</SwmToken> (possibly from a different bundle) and resolves the localized value. <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="195:3:5" line-data="        // Non-resource variable">`Non-resource`</SwmToken> arguments just use their key directly.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="437:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after resolving argument values, we either build the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and values (no bundle) or resolve the message string first if a bundle is set. This is the last step before returning the message.

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
