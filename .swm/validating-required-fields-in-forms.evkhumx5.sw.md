---
title: Validating Required Fields in Forms
---
This document describes how the system checks that all required fields in a user-submitted form are filled in. If a required field is missing or blank, the user receives an error message explaining what needs to be corrected. If the system cannot access the field, a general error message is shown.

```mermaid
flowchart TD
  node1["Checking for Required Field Value"]:::HeadingStyle
  click node1 goToHeading "Checking for Required Field Value"
  node1 --> node2{"Was field value extracted?"}
  node2 -->|"No"| node3["User receives error message"]
  node2 -->|"Yes"| node4["Handling Blank or Null Field Values"]:::HeadingStyle
  click node4 goToHeading "Handling Blank or Null Field Values"
  node4 --> node5{"Is value blank or null?"}
  node5 -->|"Yes"| node3
  node5 -->|"No"| node6["Form submission can proceed"]
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Checking for Required Field Value

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="122">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="122:7:7" line-data="    public static boolean validateRequired(Object bean, ValidatorAction va,">`validateRequired`</SwmToken>, we try to extract the field value from the bean. If that fails (e.g., property missing, reflection error), we immediately call <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="130:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;required&quot;, e);">`processFailure`</SwmToken> to log the error and add a system error message for the user, then return false to halt further validation for this field.

```java
    public static boolean validateRequired(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "required", e);
            return false;
        }

```

---

</SwmSnippet>

## Logging and Reporting Validation Failures

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1434">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1434:7:7" line-data="    private static void processFailure(ActionMessages errors, Field field,">`processFailure`</SwmToken>, we build a formatted log message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1438:1:3" line-data="            sysmsgs.getMessage(&quot;validation.failed&quot;, validatorName,">`sysmsgs.getMessage`</SwmToken>, which pulls the error template and fills in details like validator name, field, and exception. This prepares a clear log entry before we log the error and move on to user-facing messaging.

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

### Formatting the Log/Error Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="355">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="355:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0,">`getMessage`</SwmToken> with three arguments just wraps them into an Object array and calls the main <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="355:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0,">`getMessage`</SwmToken> method, so we can handle messages with up to three placeholders easily.

```java
    public String getMessage(Locale locale, String key, Object arg0,
        Object arg1, Object arg2) {
        return this.getMessage(locale, key, new Object[] { arg0, arg1, arg2 });
    }
```

---

</SwmSnippet>

### Retrieving and Formatting Localized Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  nodeA["Receive locale, key, and argument"] --> nodeB["Wrap argument in array"]
  click nodeA openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
  click nodeB openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:325:325"
  nodeB --> node1{"Is locale provided?"}
  click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node1 -->|"No"| node2["Use default locale"]
  click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node1 -->|"Yes"| node3["Use provided locale"]
  click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
  node2 --> node4{"Is message for key found?"}
  node3 --> node4
  click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:299:311"
  node4 -->|"Yes"| node5["Format message with argument"]
  click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:311"
  node4 -->|"No"| node6{"Should return null?"}
  click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
  node6 -->|"Yes"| node7["Return null"]
  click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:302"
  node6 -->|"No"| node8["Return placeholder message"]
  click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   nodeA["Receive locale, key, and argument"] --> nodeB["Wrap argument in array"]
%%   click nodeA openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%   click nodeB openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:325:325"
%%   nodeB --> node1{"Is locale provided?"}
%%   click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node1 -->|"No"| node2["Use default locale"]
%%   click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node1 -->|"Yes"| node3["Use provided locale"]
%%   click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%   node2 --> node4{"Is message for key found?"}
%%   node3 --> node4
%%   click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:299:311"
%%   node4 -->|"Yes"| node5["Format message with argument"]
%%   click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:311"
%%   node4 -->|"No"| node6{"Should return null?"}
%%   click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%   node6 -->|"Yes"| node7["Return null"]
%%   click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:302"
%%   node6 -->|"No"| node8["Return placeholder message"]
%%   click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> (with Object\[\] args) handles message formatting and caching. It checks for a cached <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> for the locale/key combo, creates and caches one if missing, and formats the message. If the message string is missing, it returns a fallback string like '???key???'. The escape step ensures special characters in the format string don't break formatting.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> (with Object\[\] args) manages the full message formatting pipeline: it uses a synchronized cache for <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> objects (per locale/key), handles missing messages with a fallback string, escapes the format string, and finally formats the message with the provided arguments.

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

### Adding a User-Facing Error Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1443">

---

Back in FieldChecks.processFailure, after logging, we fetch a generic system error message (localized) for the user and add it to the errors collection. This keeps technical details out of the UI. The message is fetched using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1444:7:9" line-data="        String userErrorMsg = sysmsgs.getMessage(&quot;system.error&quot;);">`sysmsgs.getMessage`</SwmToken>, which calls into <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken>.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="196:5:5" line-data="    public String getMessage(String key) {">`getMessage`</SwmToken> (with just a key) is a shortcut to fetch a message string with no arguments, defaulting to the default locale. Used for simple, static messages like 'system error'.

```java
    public String getMessage(String key) {
        return this.getMessage((Locale) null, key, null);
    }
```

---

</SwmSnippet>

## Handling Blank or Null Field Values

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="134">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="122:7:7" line-data="    public static boolean validateRequired(Object bean, ValidatorAction va,">`validateRequired`</SwmToken>, if the value is blank or null, we add a field-specific error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="136:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>. This generates a localized, user-friendly message and adds it to the errors collection.

```java
        if (GenericValidator.isBlankOrNull(value)) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));

            return false;
        } else {
            return true;
        }
    }
```

---

</SwmSnippet>

# Building the Field Error Message

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Check for custom message for field and
rule"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:367:369"
    node1 --> node2{"Custom message exists and is not a
resource?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:369:371"
    node2 -->|"Yes"| node3["Return custom message as action message"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:370:371"
    node2 -->|"No"| node4{"Is message key available?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:383:386"
    node4 -->|"No"| node5["Return default error message for field
and rule"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:384:386"
    node4 -->|"Yes"| node6["Resolving Argument Values for the Error Message"]
    

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Resolving Argument Values for the Error Message"
node6:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Check for custom message for field and
%% rule"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:367:369"
%%     node1 --> node2{"Custom message exists and is not a
%% resource?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:369:371"
%%     node2 -->|"Yes"| node3["Return custom message as action message"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:370:371"
%%     node2 -->|"No"| node4{"Is message key available?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:383:386"
%%     node4 -->|"No"| node5["Return default error message for field
%% and rule"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:384:386"
%%     node4 -->|"Yes"| node6["Resolving Argument Values for the Error Message"]
%%     
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Resolving Argument Values for the Error Message"
%% node6:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out which message key and bundle to use for the error. If the field has a custom message, we use that; otherwise, we use the validator's default. Then we grab the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="390:1:1" line-data="        MessageResources messages =">`MessageResources`</SwmToken> instance for localization.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="117">

---

GetMessageResources handles the lookup for the right <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> instance. It checks the request first, then the application with module prefix, then just the bundle name. If nothing is found, it throws. The default bundle is <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="120:5:7" line-data="            bundle = Globals.MESSAGES_KEY;">`Globals.MESSAGES_KEY`</SwmToken>.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="392">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="136:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, after getting <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken>, we grab the user's locale and fetch the field arguments for message formatting. Next, we need to resolve the actual argument values, possibly localizing them.

```java
        Locale locale = RequestUtils.getUserLocale(request, null);

        Arg[] args = field.getArgs(va.getName());
```

---

</SwmSnippet>

## Resolving Field Argument Placeholders

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Prepare messages for validation error
display"] --> node2["Resolve up to 4 arguments for the field
and action"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:420:423"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:424:429"
    
    subgraph loop1["For each argument (up to 4)"]
      node2 --> node3{"Is there an argument to display?"}
      click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:432"
      node3 -->|"No"| node8["Next argument"]
      node3 -->|"Yes"| node4{"Is argument a resource key?"}
      click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:435:439"
      node4 -->|"Yes"| node5["Resolve to localized message for user"]
      click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:436:436"
      node4 -->|"No"| node6["Use argument value directly"]
      click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:438:438"
      node5 --> node8
      node6 --> node8
      node8["Next argument"] --> node9{"More arguments to process?"}
      click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:440"
      node9 -->|"Yes"| node3
      node9 -->|"No"| node7["Return prepared messages"]
      click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:440"
    end
    node7["Return prepared messages"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:442:443"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Prepare messages for validation error
%% display"] --> node2["Resolve up to 4 arguments for the field
%% and action"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:420:423"
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:424:429"
%%     
%%     subgraph loop1["For each argument (up to 4)"]
%%       node2 --> node3{"Is there an argument to display?"}
%%       click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:432"
%%       node3 -->|"No"| node8["Next argument"]
%%       node3 -->|"Yes"| node4{"Is argument a resource key?"}
%%       click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:435:439"
%%       node4 -->|"Yes"| node5["Resolve to localized message for user"]
%%       click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:436:436"
%%       node4 -->|"No"| node6["Use argument value directly"]
%%       click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:438:438"
%%       node5 --> node8
%%       node6 --> node8
%%       node8["Next argument"] --> node9{"More arguments to process?"}
%%       click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:440"
%%       node9 -->|"Yes"| node3
%%       node9 -->|"No"| node7["Return prepared messages"]
%%       click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:440"
%%     end
%%     node7["Return prepared messages"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:442:443"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="420">

---

GetArgs builds an array of up to 4 argument strings for the message. For each argument, if it's a resource, we fetch the localized string; otherwise, we use the key as-is. Only 4 arguments are supported, which is a hidden limit.

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

## Fetching Localized Argument Strings

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are message resources available?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:235:237"
    node1 -->|"Yes"| node2["Retrieve message for key and locale"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:236:236"
    node2 --> node3{"Was a message found?"}
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node3 -->|"Yes"| node4["Return message"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node3 -->|"No"| node5["Return empty string"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node1 -->|"No"| node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are message resources available?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:235:237"
%%     node1 -->|"Yes"| node2["Retrieve message for key and locale"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:236:236"
%%     node2 --> node3{"Was a message found?"}
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node3 -->|"Yes"| node4["Return message"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node3 -->|"No"| node5["Return empty string"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node1 -->|"No"| node5
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="231">

---

GetMessage checks if messages is null, then fetches the localized string for the key. If not found, it returns an empty string. This is used for argument substitution in error messages.

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

GetMessage (with key and one argument) is a shortcut to fetch a localized string for a single argument, defaulting to the default locale. Used for argument substitution.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Resolving Argument Values for the Error Message

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="395">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="136:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, after getting the argument definitions, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> to resolve and localize the actual argument values. This handles cases where arguments are themselves resource keys.

```java
        String[] argValues =
            getArgValues(application, request, messages, locale, args);

```

---

</SwmSnippet>

## Resolving and Localizing Argument Values

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Are there arguments to process?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
  node1 -->|"No"| node2["Return null"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
  node1 -->|"Yes"| node3["Resolve argument values"]
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
  subgraph loop1["For each argument"]
    node3 --> node4{"Is argument a resource?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:477"
    node4 -->|"Yes"| node5{"Custom bundle specified?"}
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
    node5 -->|"Yes"| node6["Use custom bundle to get message"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
    node5 -->|"No"| node7["Use default bundle to get message"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:468"
    node4 -->|"No"| node8["Use literal value"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
  end
  node3 --> node9["Return resolved values"]
  click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Are there arguments to process?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%   node1 -->|"No"| node2["Return null"]
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%   node1 -->|"Yes"| node3["Resolve argument values"]
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%   subgraph loop1["For each argument"]
%%     node3 --> node4{"Is argument a resource?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:477"
%%     node4 -->|"Yes"| node5{"Custom bundle specified?"}
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%     node5 -->|"Yes"| node6["Use custom bundle to get message"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%     node5 -->|"No"| node7["Use default bundle to get message"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:468"
%%     node4 -->|"No"| node8["Use literal value"]
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%   end
%%   node3 --> node9["Return resolved values"]
%%   click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken>, for each argument, if it's a resource and specifies a bundle, we fetch <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="456:6:6" line-data="        HttpServletRequest request, MessageResources defaultMessages,">`MessageResources`</SwmToken> for that bundle and resolve the value. Otherwise, we use the default messages or the key directly. This supports arguments from different bundles.

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

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, for each argument, we either fetch the localized string (if it's a resource) or use the key directly. This produces the final array of argument values for message formatting.

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

## Constructing the Final <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1446:14:14" line-data="        errors.add(field.getKey(), new ActionMessage(userErrorMsg, false));">`ActionMessage`</SwmToken>

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node2{"Is a message bundle provided?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:406"
    node2 -->|"No"| node3["Create action message using message key
and arguments"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:401"
    node2 -->|"Yes"| node4["Get localized message using bundle,
locale, key, and arguments"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:403"
    node4 --> node5["Create action message with localized
message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:405:405"
    node3 --> node6["Return action message"]
    node5 --> node6["Return action message"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:408"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Is a message bundle provided?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:406"
%%     node2 -->|"No"| node3["Create action message using message key
%% and arguments"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:401"
%%     node2 -->|"Yes"| node4["Get localized message using bundle,
%% locale, key, and arguments"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:403"
%%     node4 --> node5["Create action message with localized
%% message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:405:405"
%%     node3 --> node6["Return action message"]
%%     node5 --> node6["Return action message"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:408"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="136:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken>, after resolving argument values, we either create an <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and arguments (if no bundle) or with the resolved message string (if bundle is set). This is the final step before returning the error message.

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
