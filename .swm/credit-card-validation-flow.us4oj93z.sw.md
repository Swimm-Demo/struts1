---
title: Credit Card Validation Flow
---
This document describes the flow for validating a credit card number provided by a user. The system checks if the input is blank or null (which is considered valid), validates the credit card format, and, if invalid, constructs a localized error message using resource bundles and argument substitution.

# Credit Card Validation Entry

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1134">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1134:7:7" line-data="    public static Object validateCreditCard(Object bean, ValidatorAction va,">`validateCreditCard`</SwmToken>, we grab the value from the bean for validation. If there's an exception, we immediately call <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1143:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;creditCard&quot;, e);">`processFailure`</SwmToken> to log the error and add a user error message, then return false to indicate validation can't continue.

```java
    public static Object validateCreditCard(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "creditCard", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

```

---

</SwmSnippet>

## Error Handling and Logging

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1434">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1434:7:7" line-data="    private static void processFailure(ActionMessages errors, Field field,">`processFailure`</SwmToken>, we build a log message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1438:1:3" line-data="            sysmsgs.getMessage(&quot;validation.failed&quot;, validatorName,">`sysmsgs.getMessage`</SwmToken> to include validator, field, form, and exception details, then log it. Next, we need MessageResources.getMessage to handle message formatting and localization.

```java
    private static void processFailure(ActionMessages errors, Field field,
        String formName, String validatorName, Throwable t) {
        // Log the error
        String logErrorMsg =
            sysmsgs.getMessage("validation.failed", validatorName,
                field.getProperty(), formName, t.toString());

        log.error(logErrorMsg, t);

```

---

</SwmSnippet>

### Formatting Log Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="355">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="355:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0,">`getMessage`</SwmToken> takes three arguments and passes them as an array for message formatting. This lets us inject multiple values into the log or user message templates.

```java
    public String getMessage(Locale locale, String key, Object arg0,
        Object arg1, Object arg2) {
        return this.getMessage(locale, key, new Object[] { arg0, arg1, arg2 });
    }
```

---

</SwmSnippet>

### Message Formatting and Caching

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Receive request for message with locale,
key, and argument"] --> node2["Delegate to message retrieval with
argument array"]
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 --> node8{"Is locale provided?"}
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node8 -->|"Yes"| node3{"Is message format string found for
locale and key?"}
    node8 -->|"No"| node9["Use default locale"]
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:289:290"
    node9 --> node3
    click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:299:301"
    node3 -->|"Yes"| node4["Format message with argument and return"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    node3 -->|"No"| node5{"Should missing message return null?"}
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node5 -->|"Yes"| node6["Return null"]
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node5 -->|"No"| node7["Return placeholder string"]
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Receive request for message with locale,
%% key, and argument"] --> node2["Delegate to message retrieval with
%% argument array"]
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 --> node8{"Is locale provided?"}
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node8 -->|"Yes"| node3{"Is message format string found for
%% locale and key?"}
%%     node8 -->|"No"| node9["Use default locale"]
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:289:290"
%%     node9 --> node3
%%     click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:299:301"
%%     node3 -->|"Yes"| node4["Format message with argument and return"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     node3 -->|"No"| node5{"Should missing message return null?"}
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node5 -->|"Yes"| node6["Return null"]
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node5 -->|"No"| node7["Return placeholder string"]
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> handles message formatting, caching <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> instances for performance, deals with null locales by using a default, and returns a fallback string if the message is missing. Synchronization ensures thread safety when accessing the cache.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> here caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> objects for each locale and key, handles null locales by defaulting, and returns a fallback string if the message is missing. It synchronizes access to the cache for thread safety and formats the message with the provided arguments.

```java
    public String getMessage(Locale locale, String key, Object[] args) {
        // Cache MessageFormat instances as they are accessed
        if (locale == null) {
            locale = defaultLocale;
        }

        MessageFormat format = null;
        String formatKey = messageKey(locale, key);

        synchronized (formats) {
            format = (MessageFormat) formats.get(formatKey);

            if (format == null) {
                String formatString = getMessage(locale, key);

                if (formatString == null) {
                    return returnNull ? null : ("???" + formatKey + "???");
                }

                format = new MessageFormat(escape(formatString));
                format.setLocale(locale);
                formats.put(formatKey, format);
            }
        }

        return format.format(args);
    }
```

---

</SwmSnippet>

### User Error Messaging

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1443">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1143:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;creditCard&quot;, e);">`processFailure`</SwmToken>, after logging, we grab a general system error message from sysmsgs and add it to the errors for user display. We need MessageResources.getMessage again to fetch the localized error string.

```java
        // Add general "system error" message to show to the user
        String userErrorMsg = sysmsgs.getMessage("system.error");

        errors.add(field.getKey(), new ActionMessage(userErrorMsg, false));
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="196">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="196:5:5" line-data="    public String getMessage(String key) {">`getMessage`</SwmToken> here fetches the message for a key using the default locale and no arguments, which is handy for generic error strings.

```java
    public String getMessage(String key) {
        return this.getMessage((Locale) null, key, null);
    }
```

---

</SwmSnippet>

## Credit Card Format Validation

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["User provides credit card number"] --> node2["Validate the credit card number"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1151:1151"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1151:1152"
    node2 --> node3{"Is the credit card number valid?"}
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1153:1153"
    node3 -->|"Yes"| node4["Return valid result"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1158:1158"
    node3 -->|"No"| node5["Show error message to user and return
invalid"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:1154:1158"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["User provides credit card number"] --> node2["Validate the credit card number"]
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1151:1151"
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1151:1152"
%%     node2 --> node3{"Is the credit card number valid?"}
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1153:1153"
%%     node3 -->|"Yes"| node4["Return valid result"]
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1158:1158"
%%     node3 -->|"No"| node5["Show error message to user and return
%% invalid"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:1154:1158"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1151">

---

After returning from <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1143:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;creditCard&quot;, e);">`processFailure`</SwmToken>, <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1134:7:7" line-data="    public static Object validateCreditCard(Object bean, ValidatorAction va,">`validateCreditCard`</SwmToken> checks the credit card format. If it's invalid, we add an error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1155:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken> to build the user-facing error.

```java
        result = GenericTypeValidator.formatCreditCard(value);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building Action Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Check for custom message for field/rule"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:367:368"
    node1 --> node2{"Is custom message present and not a
resource key?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:369:371"
    node2 -->|"Yes"| node3["Return direct message as action message"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:370:371"
    node2 -->|"No"| node4{"Is message key available?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:383:386"
    node4 -->|"No"| node5["Return default error message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:384:386"
    node4 -->|"Yes"| node6["Localized Argument Value Construction"]
    
    node6 --> node7["Create action message using bundle
selection and arguments"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:406"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Resource Bundle Lookup"
node6:::HeadingStyle
click node6 goToHeading "Argument Localization"
node6:::HeadingStyle
click node6 goToHeading "Localized Argument Value Construction"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Check for custom message for field/rule"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:367:368"
%%     node1 --> node2{"Is custom message present and not a
%% resource key?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:369:371"
%%     node2 -->|"Yes"| node3["Return direct message as action message"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:370:371"
%%     node2 -->|"No"| node4{"Is message key available?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:383:386"
%%     node4 -->|"No"| node5["Return default error message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:384:386"
%%     node4 -->|"Yes"| node6["Localized Argument Value Construction"]
%%     
%%     node6 --> node7["Create action message using bundle
%% selection and arguments"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:406"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Resource Bundle Lookup"
%% node6:::HeadingStyle
%% click node6 goToHeading "Argument Localization"
%% node6:::HeadingStyle
%% click node6 goToHeading "Localized Argument Value Construction"
%% node6:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out the message key and bundle, check if the message is a resource, and prep for localization. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="391:1:1" line-data="            getMessageResources(application, request, msgBundle);">`getMessageResources`</SwmToken> to fetch the right resource bundle.

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

## Resource Bundle Lookup

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="117">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, we look for the resource bundle in the request, then in the application using the module prefix, then globally. If the bundle is null, we use the default. Next, we call ModuleUtils.getModuleConfig to get the module prefix for the lookup.

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

### Module Configuration Retrieval

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="130">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:5:5" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`getModuleConfig`</SwmToken> first tries to get the module config from the request. If it's missing, it falls back to the context with an empty string, then stores it in the request for later use. This guarantees we always have a module config available.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="89:5:5" line-data="    public ModuleConfig getModuleConfig(String prefix, ServletContext context) {">`getModuleConfig`</SwmToken> here grabs the module config from the context using either the default key or the key plus prefix. If the prefix is null or '/', it returns the default config; otherwise, it fetches the module-specific config.

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

### Resource Bundle Fallback

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="135">

---

After returning from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="128:1:1" line-data="                ModuleUtils.getInstance().getModuleConfig(request, application);">`ModuleUtils`</SwmToken>, <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken> checks for the bundle globally. If it's still missing, it throws a <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="140:5:5" line-data="            throw new NullPointerException(">`NullPointerException`</SwmToken>, making sure missing resources are caught early.

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

## Locale and Argument Preparation

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="392">

---

After getting the resources, <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1155:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken> grabs the user's locale and fetches the arguments for the error message. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="394:11:11" line-data="        Arg[] args = field.getArgs(va.getName());">`getArgs`</SwmToken> to build the localized argument values.

```java
        Locale locale = RequestUtils.getUserLocale(request, null);

        Arg[] args = field.getArgs(va.getName());
```

---

</SwmSnippet>

## Argument Localization

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start preparing argument messages"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:420:422"
    
    subgraph loop1["For each argument (up to 4)"]
        node1 --> node3{"Is argument present?"}
        click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:433"
        node3 -->|"No"| node9["Skip to next argument"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:432:433"
        node3 -->|"Yes"| node4{"Is argument a resource key?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:435:439"
        node4 -->|"Yes"| node5["Lookup localized message for argument"]
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:436:436"
        node4 -->|"No"| node6["Use argument value directly"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:438:438"
        node5 --> node9
        node6 --> node9
        node9 --> node3
    end
    node3 --> node7["Return argument messages"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:442:443"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start preparing argument messages"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:420:422"
%%     
%%     subgraph loop1["For each argument (up to 4)"]
%%         node1 --> node3{"Is argument present?"}
%%         click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:433"
%%         node3 -->|"No"| node9["Skip to next argument"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:432:433"
%%         node3 -->|"Yes"| node4{"Is argument a resource key?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:435:439"
%%         node4 -->|"Yes"| node5["Lookup localized message for argument"]
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:436:436"
%%         node4 -->|"No"| node6["Use argument value directly"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:438:438"
%%         node5 --> node9
%%         node6 --> node9
%%         node9 --> node3
%%     end
%%     node3 --> node7["Return argument messages"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:442:443"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="420">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="420:9:9" line-data="    public static String[] getArgs(String actionName,">`getArgs`</SwmToken> grabs up to four arguments from the field, checks if each is a resource, and fetches localized messages if needed. Otherwise, it uses the argument key directly. This is fixed to four, so it's not flexible for more args.

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

## Argument Message Retrieval

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is message resource available?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:235:237"
  node1 -->|"Yes"| node2["Retrieve message for given locale and
key"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:236:236"
  node2 --> node3{"Was a message found?"}
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
  node3 -->|"Yes"| node4["Return the localized message"]
  click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
  node3 -->|"No"| node5["Return empty string"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
  node1 -->|"No"| node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is message resource available?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:235:237"
%%   node1 -->|"Yes"| node2["Retrieve message for given locale and
%% key"]
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:236:236"
%%   node2 --> node3{"Was a message found?"}
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%   node3 -->|"Yes"| node4["Return the localized message"]
%%   click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%   node3 -->|"No"| node5["Return empty string"]
%%   click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%   node1 -->|"No"| node5
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="231">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="231:7:7" line-data="    public static String getMessage(MessageResources messages, Locale locale,">`getMessage`</SwmToken> checks if the messages resource is available, then fetches the localized message for the key. If it's missing, we return an empty string.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="218">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> here fetches the message for a key and argument, defaulting to the system locale if none is provided.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Argument Value Preparation

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="395">

---

After getting the args, <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1155:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken> calls <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> to build the actual argument strings, handling localization and bundle lookup for message formatting.

```java
        String[] argValues =
            getArgValues(application, request, messages, locale, args);

```

---

</SwmSnippet>

## Localized Argument Value Construction

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are there any arguments?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return null"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:459"
    node1 -->|"Yes"| node3["Start argument processing"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:462:462"
    subgraph loop1["For each argument"]
        node3 --> node4{"Is argument a resource?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:467"
        node4 -->|"Yes"| node5{"Is custom bundle specified?"}
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
        node5 -->|"Yes"| node6["Select custom bundle"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
        node6 --> node7["Get localized message (locale, bundle)"]
        click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:475"
        node7 --> node3
        node5 -->|"No"| node8["Get message from default bundle (locale)"]
        click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:468"
        node8 --> node7
        node4 -->|"No"| node9["Use argument value directly"]
        click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:477"
        node9 --> node3
    end
    node3 --> node10["Return resolved values"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:482"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there any arguments?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return null"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:459"
%%     node1 -->|"Yes"| node3["Start argument processing"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:462:462"
%%     subgraph loop1["For each argument"]
%%         node3 --> node4{"Is argument a resource?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:467"
%%         node4 -->|"Yes"| node5{"Is custom bundle specified?"}
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%         node5 -->|"Yes"| node6["Select custom bundle"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%         node6 --> node7["Get localized message (locale, bundle)"]
%%         click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:475"
%%         node7 --> node3
%%         node5 -->|"No"| node8["Get message from default bundle (locale)"]
%%         click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:468"
%%         node8 --> node7
%%         node4 -->|"No"| node9["Use argument value directly"]
%%         click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:477"
%%         node9 --> node3
%%     end
%%     node3 --> node10["Return resolved values"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:482"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken>, we check each argument for a bundle. If present, we fetch the resource bundle for that argument, letting us localize each argument separately. Otherwise, we use the default bundle.

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

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="475">

---

After returning from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> fetches the localized message for each argument if it's a resource, or uses the key directly. This builds the final argument values for the error message.

```java
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

## Final Action Message Construction

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is message bundle (msgBundle) provided?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:400"
    node1 -->|"No"| node2["Create action message using msgKey and
argValues"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:401"
    node1 -->|"Yes"| node3["Get localized message using msgKey and
argValues"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:403"
    node3 --> node4["Create action message with localized
message"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:405:405"
    node2 --> node5["Return action message"]
    node4 --> node5
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:408"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is message bundle (<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="374:3:3" line-data="        String msgBundle = null;">`msgBundle`</SwmToken>) provided?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:400"
%%     node1 -->|"No"| node2["Create action message using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="373:3:3" line-data="        String msgKey = null;">`msgKey`</SwmToken> and
%% <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="395:5:5" line-data="        String[] argValues =">`argValues`</SwmToken>"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:401"
%%     node1 -->|"Yes"| node3["Get localized message using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="373:3:3" line-data="        String msgKey = null;">`msgKey`</SwmToken> and
%% <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="395:5:5" line-data="        String[] argValues =">`argValues`</SwmToken>"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:403"
%%     node3 --> node4["Create action message with localized
%% message"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:405:405"
%%     node2 --> node5["Return action message"]
%%     node4 --> node5
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:408"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

After returning from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1155:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken> builds the final <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken>. If there's no bundle, it uses the key and argument values; if there's a bundle, it fetches the formatted message and wraps it for display.

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
