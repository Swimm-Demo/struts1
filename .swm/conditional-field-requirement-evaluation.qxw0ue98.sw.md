---
title: Conditional Field Requirement Evaluation
---
This document explains how dynamic form validation determines whether a field must be filled in, depending on the values of other fields. The process evaluates conditions across related fields and applies logical rules to decide if the main field is required. If the field is required and not filled, validation fails; otherwise, validation passes.

```mermaid
flowchart TD
  node1["Conditional Field Requirement Evaluation
Evaluate if field is required based on other fields
(Conditional Field Requirement Evaluation)"]:::HeadingStyle
  click node1 goToHeading "Conditional Field Requirement Evaluation"
  node1 --> node2{"Conditional Field Requirement Evaluation
Is the field required?
(Conditional Field Requirement Evaluation)"}:::HeadingStyle
  click node2 goToHeading "Conditional Field Requirement Evaluation"
  node2 -->|"Yes"| node3{"Conditional Field Requirement Evaluation
Is the field filled in?
(Conditional Field Requirement Evaluation)"}:::HeadingStyle
  click node3 goToHeading "Conditional Field Requirement Evaluation"
  node3 -->|"Yes"| node4["Conditional Field Requirement Evaluation
Validation passes
(Conditional Field Requirement Evaluation)"]:::HeadingStyle
  click node4 goToHeading "Conditional Field Requirement Evaluation"
  node3 -->|"No"| node5["Conditional Field Requirement Evaluation
Validation fails
(Conditional Field Requirement Evaluation)"]:::HeadingStyle
  click node5 goToHeading "Conditional Field Requirement Evaluation"
  node2 -->|"No"| node4
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Conditional Field Requirement Evaluation

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Determine logical relationship (AND/OR) between dependencies"]
  click node1 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:175:180"
  subgraph loop1["For each dependency"]
    node2["Check if dependency condition is met (null, not null, equal)"]
    click node2 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:185:238"
    node2 --> node3["Update required status based on logical relationship (AND/OR)"]
    click node3 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:231:235"
  end
  loop1 --> node4{"Is the field required after evaluating all dependencies?"}
  click node4 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:240:249"
  node4 -->|"Yes"| node5{"Is the field filled in?"}
  click node5 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:241:248"
  node5 -->|"Yes"| node6["Validation passes"]
  click node6 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:247:248"
  node5 -->|"No"| node7["Fail validation and add error"]
  click node7 openCode "core/src/main/java/org/apache/struts/validator/FieldChecks.java:242:245"
  node4 -->|"No"| node6
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Determine logical relationship (AND/OR) between dependencies"]
%%   click node1 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:175:180"
%%   subgraph loop1["For each dependency"]
%%     node2["Check if dependency condition is met (null, not null, equal)"]
%%     click node2 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:185:238"
%%     node2 --> node3["Update required status based on logical relationship (AND/OR)"]
%%     click node3 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:231:235"
%%   end
%%   loop1 --> node4{"Is the field required after evaluating all dependencies?"}
%%   click node4 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:240:249"
%%   node4 -->|"Yes"| node5{"Is the field filled in?"}
%%   click node5 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:241:248"
%%   node5 -->|"Yes"| node6["Validation passes"]
%%   click node6 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:247:248"
%%   node5 -->|"No"| node7["Fail validation and add error"]
%%   click node7 openCode "<SwmPath>[core/…/validator/FieldChecks.java](core/src/main/java/org/apache/struts/validator/FieldChecks.java)</SwmPath>:242:245"
%%   node4 -->|"No"| node6
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="159">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="159:7:7" line-data="    public static boolean validateRequiredIf(Object bean, ValidatorAction va,">`validateRequiredIf`</SwmToken>, the function starts by pulling the main field value and setting up the required flag based on the <SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="175:3:3" line-data="        String fieldJoin = &quot;AND&quot;;">`fieldJoin`</SwmToken> variable (defaults to AND). It then loops through dependent fields, which are expected to be named in a specific pattern inside the Field object (like 'field\[0\]', '<SwmToken path="core/src/main/java/org/apache/struts/validator/FieldChecks.java" pos="188:12:12" line-data="            String dependTest = field.getVarValue(&quot;fieldTest[&quot; + i + &quot;]&quot;);">`fieldTest`</SwmToken>\[0\]', etc.). For each dependent field, it figures out the property, test type, test value, and whether it's indexed, then evaluates the condition and updates the required flag using AND/OR logic. This setup is what determines if the main field should be required based on the state of other fields.

```java
    public static boolean validateRequiredIf(Object bean, ValidatorAction va,
        Field field, ActionMessages errors, Validator validator,
        HttpServletRequest request) {
        Object form =
            validator.getParameterValue(org.apache.commons.validator.Validator.BEAN_PARAM);
        String value = null;
        boolean required = false;

        try {
            value = evaluateBean(bean, field);
        } catch (Exception e) {
            processFailure(errors, field, validator.getFormName(), "requiredif", e);
            return false;
        }

        int i = 0;
        String fieldJoin = "AND";

        if (!GenericValidator.isBlankOrNull(field.getVarValue("fieldJoin"))) {
            fieldJoin = field.getVarValue("fieldJoin");
        }

        if (fieldJoin.equalsIgnoreCase("AND")) {
            required = true;
        }

        while (!GenericValidator.isBlankOrNull(field.getVarValue("field[" + i
                    + "]"))) {
            String dependProp = field.getVarValue("field[" + i + "]");
            String dependTest = field.getVarValue("fieldTest[" + i + "]");
            String dependTestValue = field.getVarValue("fieldValue[" + i + "]");
            String dependIndexed = field.getVarValue("fieldIndexed[" + i + "]");

            if (dependIndexed == null) {
                dependIndexed = "false";
            }

            String dependVal = null;
            boolean thisRequired = false;

            if (field.isIndexed() && dependIndexed.equalsIgnoreCase("true")) {
                String key = field.getKey();

                if ((key.indexOf("[") > -1) && (key.indexOf("]") > -1)) {
                    String ind = key.substring(0, key.indexOf(".") + 1);

                    dependProp = ind + dependProp;
                }
            }

            dependVal = ValidatorUtils.getValueAsString(form, dependProp);

            if (dependTest.equals(FIELD_TEST_NULL)) {
                if ((dependVal != null) && (dependVal.length() > 0)) {
                    thisRequired = false;
                } else {
                    thisRequired = true;
                }
            }

            if (dependTest.equals(FIELD_TEST_NOTNULL)) {
                if ((dependVal != null) && (dependVal.length() > 0)) {
                    thisRequired = true;
                } else {
                    thisRequired = false;
                }
            }

            if (dependTest.equals(FIELD_TEST_EQUAL)) {
                thisRequired = dependTestValue.equalsIgnoreCase(dependVal);
            }

            if (fieldJoin.equalsIgnoreCase("AND")) {
                required = required && thisRequired;
            } else {
                required = required || thisRequired;
            }

            i++;
        }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/FieldChecks.java" line="240">

---

After evaluating all the dependent field conditions, if the main field is determined to be required and it's blank, the function adds an error and returns false. If it's filled or not required, it just returns true. So, validation only fails if the field is required and empty.

```java
        if (required) {
            if (GenericValidator.isBlankOrNull(value)) {
                errors.add(field.getKey(),
                    Resources.getActionMessage(validator, request, va, field));

                return false;
            } else {
                return true;
            }
        }

        return true;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
