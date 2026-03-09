---
title: Validating Integer Input
---
This document describes how the system validates that a provided value is an integer when processing user input. If the value is missing or blank, it is accepted as valid. If the value is present but not a valid integer, a user-friendly error message is generated and returned.

```mermaid
flowchart TD
  node1["Validating Integer Input"]:::HeadingStyle
  click node1 goToHeading "Validating Integer Input"
  node1 --> node2["Handling Value Extraction Errors and Blanks"]:::HeadingStyle
  click node2 goToHeading "Handling Value Extraction Errors and Blanks"
  node2 --> node3{"Is value blank or missing?"}
  node3 -->|"Yes"| node5["Accept as valid"]
  node3 -->|"No"| node4["Parsing and Reporting Integer Validation Results"]:::HeadingStyle
  click node4 goToHeading "Parsing and Reporting Integer Validation Results"
  node4 --> node6{"Is value a valid integer?"}
  node6 -->|"Yes"| node5
  node6 -->|"No"| node7["Logging and Reporting Validation Failures"]:::HeadingStyle
  click node7 goToHeading "Logging and Reporting Validation Failures"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Integer Input

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Retrieve value to validate"] --> node2{"Was value retrieved?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:502:509"
  node2 -->|"No"| node3["Logging and Reporting Validation Failures"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:509:513"
  
  node2 -->|"Yes"| node4{"Is value blank or a valid integer?"}
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:515:526"
  node4 -->|"Yes"| node5["Accept as valid"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:516:517"
  node4 -->|"No"| node3

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Logging and Reporting Validation Failures"
node3:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Retrieve value to validate"] --> node2{"Was value retrieved?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:502:509"
%%   node2 -->|"No"| node3["Logging and Reporting Validation Failures"]
%%   click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:509:513"
%%   
%%   node2 -->|"Yes"| node4{"Is value blank or a valid integer?"}
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:515:526"
%%   node4 -->|"Yes"| node5["Accept as valid"]
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:516:517"
%%   node4 -->|"No"| node3
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Logging and Reporting Validation Failures"
%% node3:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="502">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="502:7:7" line-data="    public static Object validateInteger(Object bean, ValidatorAction va,">`validateInteger`</SwmToken>, we're prepping to validate an integer by extracting the relevant value from the bean. We call <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="509:5:5" line-data="            value = evaluateBean(bean, field);">`evaluateBean`</SwmToken> next to handle both String and non-String beans, so we always get a String value to work with.

```java
    public static Object validateInteger(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
```

---

</SwmSnippet>

## Extracting Field Value as String

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="389">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="389:7:7" line-data="    private static String evaluateBean(Object bean, Field field) throws Exception {">`evaluateBean`</SwmToken> checks if the bean is a String and returns it, otherwise it pulls the property value using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="395:5:5" line-data="            value = getValueAsString(bean, field.getProperty());">`getValueAsString`</SwmToken>. This handles cases where the bean is a complex object, not just a String.

```java
    private static String evaluateBean(Object bean, Field field) throws Exception {
        String value;

        if (isString(bean)) {
            value = (String) bean;
        } else {
            value = getValueAsString(bean, field.getProperty());
        }

        return value;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="87">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="87:7:7" line-data="    private static String getValueAsString(Object bean, String property) ">`getValueAsString`</SwmToken> fetches the property value from the bean and normalizes it: null stays null, empty arrays/collections become empty strings, and everything else is converted to a string. This keeps downstream validation logic simple.

```java
    private static String getValueAsString(Object bean, String property) 
            throws Exception {
        
        Object value = PropertyUtils.getProperty(bean, property);
        if (value == null) {
            return null;
        }

        if (value instanceof String[]) {
            return ((String[]) value).length > 0 ? value.toString() : "";

        } else if (value instanceof Collection) {
            return ((Collection) value).isEmpty() ? "" : value.toString();

        } else {
            return value.toString();
        }
    }
```

---

</SwmSnippet>

## Handling Value Extraction Errors and Blanks

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="510">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="502:7:7" line-data="    public static Object validateInteger(Object bean, ValidatorAction va,">`validateInteger`</SwmToken>, after getting the value from <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="389:7:7" line-data="    private static String evaluateBean(Object bean, Field field) throws Exception {">`evaluateBean`</SwmToken>, if an exception happens, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="511:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;integer&quot;, e);">`processFailure`</SwmToken> to log and report the error. If the value is blank or null, we skip further checks and return success.

```java
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "integer", e);
            return Boolean.FALSE;
        }

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

```

---

</SwmSnippet>

## Logging and Reporting Validation Failures

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1434">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="1434:7:7" line-data="    private static void processFailure(ActionMessages errors, Field field,">`processFailure`</SwmToken>, we build a formatted log message using <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken>, including details like validator name, field, form, and the exception. This keeps logs structured and localizable.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="355:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0,">`getMessage`</SwmToken> here just wraps up to three arguments into an array and passes them to the main message formatting logic.

```java
    public String getMessage(Locale locale, String key, Object arg0,
        Object arg1, Object arg2) {
        return this.getMessage(locale, key, new Object[] { arg0, arg1, arg2 });
    }
```

---

</SwmSnippet>

### Formatting Messages with One Argument

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Receive request for message (locale,
key, argument)"] --> node2["Convert argument to array"]
    click node1 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    click node2 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:324:326"
    node2 --> node3{"Is locale provided?"}
    click node3 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node3 -->|"Yes"| node4["Use provided locale"]
    node3 -->|"No"| node5["Use default locale"]
    click node4 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    click node5 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:288:290"
    node4 --> node6{"Is message format available for locale
and key?"}
    node5 --> node6
    click node6 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:295:308"
    node6 -->|"Yes"| node9["Format and return message with argument"]
    click node9 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:311:312"
    node6 -->|"No"| node7{"Does message exist for locale and key?"}
    click node7 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:299:303"
    node7 -->|"Yes"| node8["Create and store message format"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:305:308"
    node8 --> node9
    node7 -->|"No"| node10{"Should return null?"}
    click node10 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node10 -->|"Yes"| node11["Return null"]
    click node11 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"
    node10 -->|"No"| node12["Return placeholder message"]
    click node12 openCode "core/src/main/java/org/apache/struts/util/MessageResources.java:302:303"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Receive request for message (locale,
%% key, argument)"] --> node2["Convert argument to array"]
%%     click node1 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     click node2 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:324:326"
%%     node2 --> node3{"Is locale provided?"}
%%     click node3 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node3 -->|"Yes"| node4["Use provided locale"]
%%     node3 -->|"No"| node5["Use default locale"]
%%     click node4 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     click node5 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:288:290"
%%     node4 --> node6{"Is message format available for locale
%% and key?"}
%%     node5 --> node6
%%     click node6 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:295:308"
%%     node6 -->|"Yes"| node9["Format and return message with argument"]
%%     click node9 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:311:312"
%%     node6 -->|"No"| node7{"Does message exist for locale and key?"}
%%     click node7 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:299:303"
%%     node7 -->|"Yes"| node8["Create and store message format"]
%%     click node8 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:305:308"
%%     node8 --> node9
%%     node7 -->|"No"| node10{"Should return null?"}
%%     click node10 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node10 -->|"Yes"| node11["Return null"]
%%     click node11 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%%     node10 -->|"No"| node12["Return placeholder message"]
%%     click node12 openCode "<SwmPath>[core/…/util/MessageResources.java](core/src/main/java/org/apache/struts/util/MessageResources.java)</SwmPath>:302:303"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="324">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="324:5:5" line-data="    public String getMessage(Locale locale, String key, Object arg0) {">`getMessage`</SwmToken> here is just a shortcut for formatting messages with one argument, passing it as an array to the main formatter.

```java
    public String getMessage(Locale locale, String key, Object arg0) {
        return this.getMessage(locale, key, new Object[] { arg0 });
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="286">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="286:5:5" line-data="    public String getMessage(Locale locale, String key, Object[] args) {">`getMessage`</SwmToken> here does the heavy lifting: it caches <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="287:5:5" line-data="        // Cache MessageFormat instances as they are accessed">`MessageFormat`</SwmToken> instances per locale and key, handles missing locales and messages, escapes format strings, and finally formats the message with the given arguments.

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

### Adding User-Facing Error Messages

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="1443">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="511:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;integer&quot;, e);">`processFailure`</SwmToken>, after logging, we fetch a generic system error message from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:5:5" line-data="    public static MessageResources getMessageResources(">`MessageResources`</SwmToken> and add it to the errors for the user. This keeps user-facing messages clean and non-technical.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="196:5:5" line-data="    public String getMessage(String key) {">`getMessage`</SwmToken> here is just a shortcut for fetching a message string with no arguments and default locale.

```java
    public String getMessage(String key) {
        return this.getMessage((Locale) null, key, null);
    }
```

---

</SwmSnippet>

## Parsing and Reporting Integer Validation Results

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Validate input value as integer"] --> node2{"Is value a valid integer?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:519:520"
    node2 -->|"Yes"| node3["Return integer value"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:521:526"
    node2 -->|"No"| node4["Record error"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:522:524"
    node4 --> node5["Return failure (Boolean.FALSE)"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:526:526"
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:526:526"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Validate input value as integer"] --> node2{"Is value a valid integer?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:519:520"
%%     node2 -->|"Yes"| node3["Return integer value"]
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:521:526"
%%     node2 -->|"No"| node4["Record error"]
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:522:524"
%%     node4 --> node5["Return failure (<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="512:3:5" line-data="            return Boolean.FALSE;">`Boolean.FALSE`</SwmToken>)"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:526:526"
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:526:526"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="519">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="502:7:7" line-data="    public static Object validateInteger(Object bean, ValidatorAction va,">`validateInteger`</SwmToken>, after <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="511:1:1" line-data="            processFailure(errors, field, validator.getFormName(), &quot;integer&quot;, e);">`processFailure`</SwmToken>, we try to parse the value as an integer. If parsing fails, we add a validation error message using <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="523:1:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`Resources.getActionMessage`</SwmToken> and return <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="526:13:15" line-data="        return (result == null) ? Boolean.FALSE : result;">`Boolean.FALSE`</SwmToken>.

```java
        result = GenericTypeValidator.formatInt(value);

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

# Building Validation Error Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Check for custom message on field/rule"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:367:371"
    node1 --> node2{"Custom message present and not a
resource?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:369:371"
    node2 -->|"Yes"| node3["Return custom message as action message"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:370:371"
    node2 -->|"No"| node4{"Is message key present?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:383:386"
    node4 -->|"No"| node5["Return default error message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:384:386"
    node4 -->|"Yes"| node6["Building Argument Message Array"]
    
    node6 --> node7["Finalizing Argument Localization"]
    
    node7 --> node8{"Is message bundle specified?"}
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:400:406"
    node8 -->|"No"| node9["Create action message from key and
arguments"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:402"
    node8 -->|"Yes"| node10["Fetching Localized Argument Messages"]
    

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Resolving Message Resource Bundles"
node6:::HeadingStyle
click node6 goToHeading "Building Argument Message Array"
node6:::HeadingStyle
click node7 goToHeading "Finalizing Argument Localization"
node7:::HeadingStyle
click node10 goToHeading "Fetching Localized Argument Messages"
node10:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Check for custom message on field/rule"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:367:371"
%%     node1 --> node2{"Custom message present and not a
%% resource?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:369:371"
%%     node2 -->|"Yes"| node3["Return custom message as action message"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:370:371"
%%     node2 -->|"No"| node4{"Is message key present?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:383:386"
%%     node4 -->|"No"| node5["Return default error message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:384:386"
%%     node4 -->|"Yes"| node6["Building Argument Message Array"]
%%     
%%     node6 --> node7["Finalizing Argument Localization"]
%%     
%%     node7 --> node8{"Is message bundle specified?"}
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:406"
%%     node8 -->|"No"| node9["Create action message from key and
%% arguments"]
%%     click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:402"
%%     node8 -->|"Yes"| node10["Fetching Localized Argument Messages"]
%%     
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Resolving Message Resource Bundles"
%% node6:::HeadingStyle
%% click node6 goToHeading "Building Argument Message Array"
%% node6:::HeadingStyle
%% click node7 goToHeading "Finalizing Argument Localization"
%% node7:::HeadingStyle
%% click node10 goToHeading "Fetching Localized Argument Messages"
%% node10:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="365">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="365:7:7" line-data="    public static ActionMessage getActionMessage(Validator validator,">`getActionMessage`</SwmToken>, we figure out if the message is a resource or a literal, pick the right key and bundle, and fetch <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="390:1:1" line-data="        MessageResources messages =">`MessageResources`</SwmToken> from the application context if needed.

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

## Resolving Message Resource Bundles

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is a bundle name provided?"}
  click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:119:121"
  node1 -->|"Yes/No"| node2["Determine bundle name (provided or
default)"]
  click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:119:121"
  node2 --> node3{"Are message resources in the request?"}
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:123:124"
  node3 -->|"Yes"| node4["Return resources from request"]
  click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:124:124"
  node3 -->|"No"| node5{"Are resources in application for
current module?"}
  click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:126:133"
  node5 -->|"Yes"| node6["Return resources from application
(module)"]
  click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:130:133"
  node5 -->|"No"| node7{"Are resources in application for
default bundle?"}
  click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:135:136"
  node7 -->|"Yes"| node8["Return resources from application
(default)"]
  click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:136:137"
  node7 -->|"No"| node9["Raise error: No message resources
found"]
  click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:139:142"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is a bundle name provided?"}
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:119:121"
%%   node1 -->|"Yes/No"| node2["Determine bundle name (provided or
%% default)"]
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:119:121"
%%   node2 --> node3{"Are message resources in the request?"}
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:123:124"
%%   node3 -->|"Yes"| node4["Return resources from request"]
%%   click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:124:124"
%%   node3 -->|"No"| node5{"Are resources in application for
%% current module?"}
%%   click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:126:133"
%%   node5 -->|"Yes"| node6["Return resources from application
%% (module)"]
%%   click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:130:133"
%%   node5 -->|"No"| node7{"Are resources in application for
%% default bundle?"}
%%   click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:135:136"
%%   node7 -->|"Yes"| node8["Return resources from application
%% (default)"]
%%   click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:136:137"
%%   node7 -->|"No"| node9["Raise error: No message resources
%% found"]
%%   click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:139:142"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="117">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, we try to get the message bundle from the request, then fall back to the application context using the module prefix, and finally just the bundle name. This covers all the ways resources might be scoped in a modular app.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/ModuleUtils.java" line="130">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/ModuleUtils.java" pos="130:5:5" line-data="    public ModuleConfig getModuleConfig(HttpServletRequest request,">`getModuleConfig`</SwmToken> first tries to get the config from the request, and if that's missing, it pulls the default from the context and attaches it to the request for later use.

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

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="135">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="117:7:7" line-data="    public static MessageResources getMessageResources(">`getMessageResources`</SwmToken>, after trying all the fallback options, if we still can't find the resources, we throw a <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="140:5:5" line-data="            throw new NullPointerException(">`NullPointerException`</SwmToken>. This makes missing resource bundles obvious during development or deployment.

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

## Preparing Message Arguments

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="392">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="523:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after getting the resources, we grab the user's locale and fetch any arguments from the field for message formatting.

```java
        Locale locale = RequestUtils.getUserLocale(request, null);

        Arg[] args = field.getArgs(va.getName());
```

---

</SwmSnippet>

## Building Argument Message Array

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Prepare up to four argument messages for
validation error"]
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:420:423"
    
    subgraph loop1["For each argument (up to 4)"]
        node2{"Is argument defined?"}
        click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:430:433"
        node2 -->|"No"| node7["Next argument"]
        node2 -->|"Yes"| node4{"Is argument a resource reference?"}
        click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:435:439"
        node4 -->|"Yes"| node5["Use localized resource message"]
        click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:436:437"
        node4 -->|"No"| node6["Use literal value"]
        click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:438:439"
        node5 --> node7
        node6 --> node7
        node7["Next argument"]
    end
    node1 --> node2
    node7 -->|"After all arguments processed"| node8["Return prepared argument messages"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:442:443"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Prepare up to four argument messages for
%% validation error"]
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:420:423"
%%     
%%     subgraph loop1["For each argument (up to 4)"]
%%         node2{"Is argument defined?"}
%%         click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:430:433"
%%         node2 -->|"No"| node7["Next argument"]
%%         node2 -->|"Yes"| node4{"Is argument a resource reference?"}
%%         click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:435:439"
%%         node4 -->|"Yes"| node5["Use localized resource message"]
%%         click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:436:437"
%%         node4 -->|"No"| node6["Use literal value"]
%%         click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:438:439"
%%         node5 --> node7
%%         node6 --> node7
%%         node7["Next argument"]
%%     end
%%     node1 --> node2
%%     node7 -->|"After all arguments processed"| node8["Return prepared argument messages"]
%%     click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:442:443"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="420">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="420:9:9" line-data="    public static String[] getArgs(String actionName,">`getArgs`</SwmToken> builds an array of up to four argument messages, localizing each if it's a resource, or just using the key if not. This is all based on the field's argument definitions.

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

## Fetching Localized Argument Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node2{"Is message resource provided?"}
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:235:237"
    node2 -->|"Yes"| node3["Retrieve message for key and locale"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:236:236"
    node2 -->|"No"| node7["Return empty string"]
    click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node3 --> node5{"Is message found?"}
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node5 -->|"Yes"| node6["Return message"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:239:239"
    node5 -->|"No"| node7
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Is message resource provided?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:235:237"
%%     node2 -->|"Yes"| node3["Retrieve message for key and locale"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:236:236"
%%     node2 -->|"No"| node7["Return empty string"]
%%     click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node3 --> node5{"Is message found?"}
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node5 -->|"Yes"| node6["Return message"]
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:239:239"
%%     node5 -->|"No"| node7
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="231">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="231:7:7" line-data="    public static String getMessage(MessageResources messages, Locale locale,">`getMessage`</SwmToken> here fetches a localized message for a given key from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="231:9:9" line-data="    public static String getMessage(MessageResources messages, Locale locale,">`MessageResources`</SwmToken>, returning an empty string if the key isn't found.

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

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="218:5:5" line-data="    public String getMessage(String key, Object arg0) {">`getMessage`</SwmToken> here is just a shortcut for fetching a message with one argument and default locale.

```java
    public String getMessage(String key, Object arg0) {
        return this.getMessage((Locale) null, key, arg0);
    }
```

---

</SwmSnippet>

## Resolving Argument Values

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="395">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="523:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after building the argument array, we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken> to resolve any resource-based arguments, possibly using different bundles or more localization.

```java
        String[] argValues =
            getArgValues(application, request, messages, locale, args);

```

---

</SwmSnippet>

## Finalizing Argument Localization

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Are there arguments to process?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:458:460"
    node1 -->|"No"| node2["Return no values"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:459:460"
    node1 -->|"Yes"| node3["Process each argument"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:464:480"
    subgraph loop1["For each argument"]
      node3 --> node4{"Is argument a resource?"}
      click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:466:477"
      node4 -->|"Yes"| node5{"Custom bundle specified?"}
      click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:469:473"
      node5 -->|"Yes"| node6["Resolve value from custom bundle"]
      click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:470:473"
      node5 -->|"No"| node7["Resolve value from default messages"]
      click node7 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:467:469"
      node6 --> node8["Assign value to result"]
      click node8 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:475:476"
      node7 --> node8
      node4 -->|"No"| node9["Assign literal value to result"]
      click node9 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:477:478"
      node8 --> node3
      node9 --> node3
    end
    node3 -->|"All arguments processed"| node10["Return values"]
    click node10 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:482:483"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Are there arguments to process?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:458:460"
%%     node1 -->|"No"| node2["Return no values"]
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:459:460"
%%     node1 -->|"Yes"| node3["Process each argument"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:464:480"
%%     subgraph loop1["For each argument"]
%%       node3 --> node4{"Is argument a resource?"}
%%       click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:466:477"
%%       node4 -->|"Yes"| node5{"Custom bundle specified?"}
%%       click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:469:473"
%%       node5 -->|"Yes"| node6["Resolve value from custom bundle"]
%%       click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:470:473"
%%       node5 -->|"No"| node7["Resolve value from default messages"]
%%       click node7 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:467:469"
%%       node6 --> node8["Assign value to result"]
%%       click node8 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:475:476"
%%       node7 --> node8
%%       node4 -->|"No"| node9["Assign literal value to result"]
%%       click node9 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:477:478"
%%       node8 --> node3
%%       node9 --> node3
%%     end
%%     node3 -->|"All arguments processed"| node10["Return values"]
%%     click node10 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:482:483"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="455">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="455:9:9" line-data="    private static String[] getArgValues(ServletContext application,">`getArgValues`</SwmToken>, we loop through each argument, and if it's a resource, we resolve it using the right bundle and locale. Otherwise, we just use the key. This lets us mix and match resource bundles for arguments.

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

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="396:1:1" line-data="            getArgValues(application, request, messages, locale, args);">`getArgValues`</SwmToken>, for each resource argument, we fetch the localized message using the right bundle and locale. <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="195:3:5" line-data="        // Non-resource variable">`Non-resource`</SwmToken> arguments just use their key.

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
    node2 -->|"No"| node3["Create default action message (uses
message key and arguments)"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:401:401"
    node2 -->|"Yes"| node4["Retrieve localized message from bundle
using locale, message key, and arguments"]
    click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:403:403"
    node4 --> node5["Create action message with localized
message"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:405:405"
    node3 --> node6["Return action message"]
    node5 --> node6
    click node6 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:408:408"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Is a message bundle provided?"}
%%     click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:400:406"
%%     node2 -->|"No"| node3["Create default action message (uses
%% message key and arguments)"]
%%     click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:401:401"
%%     node2 -->|"Yes"| node4["Retrieve localized message from bundle
%% using locale, message key, and arguments"]
%%     click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:403:403"
%%     node4 --> node5["Create action message with localized
%% message"]
%%     click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:405:405"
%%     node3 --> node6["Return action message"]
%%     node5 --> node6
%%     click node6 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:408:408"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="398">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="523:3:3" line-data="                Resources.getActionMessage(validator, request, va, field));">`getActionMessage`</SwmToken>, after resolving argument values, we either build the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="398:1:1" line-data="        ActionMessage actionMessage = null;">`ActionMessage`</SwmToken> with the key and arguments (if no bundle), or with the fully resolved message string (if a bundle is specified).

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
