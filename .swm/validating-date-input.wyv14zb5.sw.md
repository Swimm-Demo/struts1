---
title: Validating Date Input
---
This document outlines the process for validating date input from beans. The flow checks if the provided date value is blank, and if not, attempts to parse it using the appropriate date pattern. Valid values are accepted, while invalid ones result in an error. This ensures that only properly formatted date values are processed.

```mermaid
flowchart TD
  node1["Validating Date Input from Beans
Check if date value is blank or null
(Validating Date Input from Beans)"]:::HeadingStyle
  click node1 goToHeading "Validating Date Input from Beans"
  node1 -->|"Yes"| node2["Validating Date Input from Beans
Accept as valid
(Validating Date Input from Beans)"]:::HeadingStyle
  click node2 goToHeading "Validating Date Input from Beans"
  node1 -->|"No"| node3["Validating Date Input from Beans
Attempt to parse date
(Validating Date Input from Beans)"]:::HeadingStyle
  click node3 goToHeading "Validating Date Input from Beans"
  node3 --> node4["Validating Date Input from Beans
Is date valid?
(Validating Date Input from Beans)"]:::HeadingStyle
  click node4 goToHeading "Validating Date Input from Beans"
  node4 -->|"Yes"| node2
  node4 -->|"No"| node5["Validating Date Input from Beans
Record error and reject value
(Validating Date Input from Beans)"]:::HeadingStyle
  click node5 goToHeading "Validating Date Input from Beans"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Validating Date Input from Beans

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start date validation"] --> node2{"Is value blank or null?"}
    click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:854:856"
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:884:885"
    node2 -->|"Yes"| node3["Accept as valid"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:885:885"
    node2 -->|"No"| node4{"Is date pattern provided?"}
    click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:868:880"
    node4 -->|"Yes"| node5["Try to parse date with provided pattern (strict if specified)"]
    click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:892:893"
    node4 -->|"No"| node6["Try to parse date with default pattern"]
    click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:890:891"
    node5 --> node7{"Is date valid?"}
    node6 --> node7
    click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:899:904"
    node7 -->|"Yes"| node8["Accept as valid"]
    click node8 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:904:904"
    node7 -->|"No"| node9["Record error for field and reject value"]
    click node9 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:900:902"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start date validation"] --> node2{"Is value blank or null?"}
%%     click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:854:856"
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:884:885"
%%     node2 -->|"Yes"| node3["Accept as valid"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:885:885"
%%     node2 -->|"No"| node4{"Is date pattern provided?"}
%%     click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:868:880"
%%     node4 -->|"Yes"| node5["Try to parse date with provided pattern (strict if specified)"]
%%     click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:892:893"
%%     node4 -->|"No"| node6["Try to parse date with default pattern"]
%%     click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:890:891"
%%     node5 --> node7{"Is date valid?"}
%%     node6 --> node7
%%     click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:899:904"
%%     node7 -->|"Yes"| node8["Accept as valid"]
%%     click node8 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:904:904"
%%     node7 -->|"No"| node9["Record error for field and reject value"]
%%     click node9 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:900:902"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="854">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="854:7:7" line-data="    public static Object validateDate(Object bean, ValidatorAction va,">`validateDate`</SwmToken> kicks off the date validation by pulling the date string from the bean using the field definition. It checks for a date pattern in resources, falling back to a strict pattern if needed, and toggles strictness accordingly. If the input is blank, it exits early as valid. Otherwise, it parses the date using either a locale-based format or the specified pattern/strictness. If parsing fails, it logs an error and adds a message to the errors collection. The function returns false on failure or the parsed date object on success, so downstream code can use the parsed value directly.

```java
    public static Object validateDate(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object result = null;
        String value = null;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "date", e);
            return Boolean.FALSE;
        }

        boolean isStrict = false;
        String datePattern =
            Resources.getVarValue("datePattern", field, validator, request,
                false);

        if (GenericValidator.isBlankOrNull(datePattern)) {
            datePattern =
                Resources.getVarValue("datePatternStrict", field, validator,
                    request, false);

            if (!GenericValidator.isBlankOrNull(datePattern)) {
                isStrict = true;
            }
        }

        Locale locale = RequestUtils.getUserLocale(request, null);

        if (GenericValidator.isBlankOrNull(value)) {
            return Boolean.TRUE;
        }

        try {
            if (GenericValidator.isBlankOrNull(datePattern)) {
                result = GenericTypeValidator.formatDate(value, locale);
            } else {
                result =
                    GenericTypeValidator.formatDate(value, datePattern, isStrict);
            }
        } catch (Exception e) {
            log.error(e.getMessage(), e);
        }

        if (result == null) {
            errors.add(field.getKey(),
                Resources.getActionMessage(validator, request, va, field));
        }

        return (result == null) ? Boolean.FALSE : result;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
