---
title: Resetting Checkbox Tag State
---
This document explains how a checkbox tag is reset after use. All expression fields are cleared to ensure the tag can be reused without carrying over previous values, supporting correct behavior in dynamic web pages.

# Resetting Checkbox Tag State

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="789">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="789:5:5" line-data="    public void release() {">`release`</SwmToken>, we start by calling the superclass's release method to make sure any cleanup logic from higher up the hierarchy runs first. This keeps resource management consistent. Next, we move to ELResourceTag.release to clear out expression fields specific to that layer, so nothing lingers between tag uses.

```java
    public void release() {
        super.release();
```

---

</SwmSnippet>

## Clearing Resource Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="108">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="108:5:5" line-data="    public void release() {">`release`</SwmToken>, we call the superclass's release first, then clear out <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, and <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> using their setters. This wipes out any leftover EL expressions, making sure the tag doesn't reuse stale values.

```java
    public void release() {
        super.release();
        setIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="85">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken> just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>—no extra logic, just a plain setter.

```java
    public void setIdExpr(String idExpr) {
        this.idExpr = idExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="111">

---

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="789:5:5" line-data="    public void release() {">`release`</SwmToken>, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, we immediately call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="111:1:1" line-data="        setInputExpr(null);">`setInputExpr`</SwmToken>(null) to clear the next expression field, making sure all relevant state is reset.

```java
        setInputExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="93">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> just assigns the input to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setInputExpr(String inputExpr) {
        this.inputExpr = inputExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="112">

---

Back in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="789:5:5" line-data="    public void release() {">`release`</SwmToken>, after <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> too. This finishes wiping out all EL expression fields tied to the resource tag.

```java
        setNameExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="101">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

## Clearing Checkbox-Specific Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="791">

---

Back in `ELCheckboxTag.release`, after clearing the resource tag fields, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="518:9:9" line-data="    public void setAccesskeyExpr(String accesskeyExpr) {">`accesskeyExpr`</SwmToken> to make sure no old access key values stick around for the next tag usage.

```java
        setAccesskeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="518">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="518:5:5" line-data="    public void setAccesskeyExpr(String accesskeyExpr) {">`setAccesskeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="518:9:9" line-data="    public void setAccesskeyExpr(String accesskeyExpr) {">`accesskeyExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setAccesskeyExpr(String accesskeyExpr) {
        this.accesskeyExpr = accesskeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="792">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="518:9:9" line-data="    public void setAccesskeyExpr(String accesskeyExpr) {">`accesskeyExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="526:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken> to make sure no old alt text is reused on the next tag instance.

```java
        setAltExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="526">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="526:5:5" line-data="    public void setAltExpr(String altExpr) {">`setAltExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="526:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setAltExpr(String altExpr) {
        this.altExpr = altExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="793">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="526:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="534:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken> to avoid leaking old localization keys into the next tag instance.

```java
        setAltKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="534">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="534:5:5" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`setAltKeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="534:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setAltKeyExpr(String altKeyExpr) {
        this.altKeyExpr = altKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="794">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="534:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="542:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken> to prevent old resource bundle references from leaking into the next tag usage.

```java
        setBundleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="542">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="542:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="542:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setBundleExpr(String bundleExpr) {
        this.bundleExpr = bundleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="795">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="542:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="550:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken> to avoid carrying over old directionality settings to the next tag instance.

```java
        setDirExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="550">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="550:5:5" line-data="    public void setDirExpr(String dirExpr) {">`setDirExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="550:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setDirExpr(String dirExpr) {
        this.dirExpr = dirExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="796">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="550:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="558:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken> to make sure the disabled state doesn't leak into the next tag instance.

```java
        setDisabledExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="558">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="558:5:5" line-data="    public void setDisabledExpr(String disabledExpr) {">`setDisabledExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="558:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setDisabledExpr(String disabledExpr) {
        this.disabledExpr = disabledExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="797">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="558:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="566:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken> to avoid leaking old error message keys into the next tag usage.

```java
        setErrorKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="566">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="566:5:5" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`setErrorKeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="566:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setErrorKeyExpr(String errorKeyExpr) {
        this.errorKeyExpr = errorKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="798">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="566:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="574:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken> to prevent old error styling from leaking into the next tag instance.

```java
        setErrorStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="574">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="574:5:5" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`setErrorStyleExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="574:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setErrorStyleExpr(String errorStyleExpr) {
        this.errorStyleExpr = errorStyleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="799">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="574:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="582:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken> to avoid leaking old CSS class names into the next tag instance.

```java
        setErrorStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="582">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="582:5:5" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`setErrorStyleClassExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="582:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setErrorStyleClassExpr(String errorStyleClassExpr) {
        this.errorStyleClassExpr = errorStyleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="800">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="582:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="590:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken> to prevent old element IDs from leaking into the next tag instance.

```java
        setErrorStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="590">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="590:5:5" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`setErrorStyleIdExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="590:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setErrorStyleIdExpr(String errorStyleIdExpr) {
        this.errorStyleIdExpr = errorStyleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="801">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="590:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="598:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken> to avoid leaking old index values into the next tag instance.

```java
        setIndexedExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="598">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="598:5:5" line-data="    public void setIndexedExpr(String indexedExpr) {">`setIndexedExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="598:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setIndexedExpr(String indexedExpr) {
        this.indexedExpr = indexedExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="802">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="598:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="606:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken> to avoid leaking old language settings into the next tag instance.

```java
        setLangExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="606">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="606:5:5" line-data="    public void setLangExpr(String langExpr) {">`setLangExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="606:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setLangExpr(String langExpr) {
        this.langExpr = langExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="803">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="606:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> to avoid leaking old field names into the next tag instance.

```java
        setNameExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="614">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="804">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="614:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="622:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken> to avoid leaking old event handlers into the next tag instance.

```java
        setOnblurExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="622">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="622:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="622:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setOnblurExpr(String onblurExpr) {
        this.onblurExpr = onblurExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="805">

---

Back in `ELCheckboxTag.release`, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="622:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="630:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken> to avoid leaking old onchange handlers into the next tag instance.

```java
        setOnchangeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="630">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="630:5:5" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`setOnchangeExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="630:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>. No extra logic, just a direct setter.

```java
    public void setOnchangeExpr(String onchangeExpr) {
        this.onchangeExpr = onchangeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="806">

---

Back in ELCheckboxTag.release, after we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="630:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>, we immediately call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="806:1:1" line-data="        setOnclickExpr(null);">`setOnclickExpr`</SwmToken>(null) to wipe out any old onclick handler. This keeps the tag from reusing stale event logic between instances.

```java
        setOnclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="638">

---

SetOnclickExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="638:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnclickExpr(String onclickExpr) {
        this.onclickExpr = onclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="807">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="638:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="807:1:1" line-data="        setOndblclickExpr(null);">`setOndblclickExpr`</SwmToken>(null) to clear out any old double-click handler. This prevents stale event logic from sticking around.

```java
        setOndblclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="646">

---

SetOndblclickExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="646:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOndblclickExpr(String ondblclickExpr) {
        this.ondblclickExpr = ondblclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="808">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="646:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="808:1:1" line-data="        setOnfocusExpr(null);">`setOnfocusExpr`</SwmToken>(null) to clear out any old focus handler. This prevents stale event logic from sticking around.

```java
        setOnfocusExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="654">

---

SetOnfocusExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="654:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnfocusExpr(String onfocusExpr) {
        this.onfocusExpr = onfocusExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="809">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="654:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="809:1:1" line-data="        setOnkeydownExpr(null);">`setOnkeydownExpr`</SwmToken>(null) to clear out any old keyboard handler. This prevents stale event logic from sticking around.

```java
        setOnkeydownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="662">

---

SetOnkeydownExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="662:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnkeydownExpr(String onkeydownExpr) {
        this.onkeydownExpr = onkeydownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="810">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="662:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="810:1:1" line-data="        setOnkeypressExpr(null);">`setOnkeypressExpr`</SwmToken>(null) to clear out any old keypress handler. This prevents stale event logic from sticking around.

```java
        setOnkeypressExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="670">

---

SetOnkeypressExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="670:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnkeypressExpr(String onkeypressExpr) {
        this.onkeypressExpr = onkeypressExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="811">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="670:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="811:1:1" line-data="        setOnkeyupExpr(null);">`setOnkeyupExpr`</SwmToken>(null) to clear out any old keyup handler. This prevents stale event logic from sticking around.

```java
        setOnkeyupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="678">

---

SetOnkeyupExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="678:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnkeyupExpr(String onkeyupExpr) {
        this.onkeyupExpr = onkeyupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="812">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="678:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="812:1:1" line-data="        setOnmousedownExpr(null);">`setOnmousedownExpr`</SwmToken>(null) to clear out any old mouse down handler. This prevents stale event logic from sticking around.

```java
        setOnmousedownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="686">

---

SetOnmousedownExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="686:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnmousedownExpr(String onmousedownExpr) {
        this.onmousedownExpr = onmousedownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="813">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="686:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="813:1:1" line-data="        setOnmousemoveExpr(null);">`setOnmousemoveExpr`</SwmToken>(null) to clear out any old mouse move handler. This prevents stale event logic from sticking around.

```java
        setOnmousemoveExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="694">

---

SetOnmousemoveExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="694:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnmousemoveExpr(String onmousemoveExpr) {
        this.onmousemoveExpr = onmousemoveExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="814">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="694:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="814:1:1" line-data="        setOnmouseoutExpr(null);">`setOnmouseoutExpr`</SwmToken>(null) to clear out any old mouse out handler. This prevents stale event logic from sticking around.

```java
        setOnmouseoutExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="702">

---

SetOnmouseoutExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="702:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnmouseoutExpr(String onmouseoutExpr) {
        this.onmouseoutExpr = onmouseoutExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="815">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="702:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="815:1:1" line-data="        setOnmouseoverExpr(null);">`setOnmouseoverExpr`</SwmToken>(null) to clear out any old mouse over handler. This prevents stale event logic from sticking around.

```java
        setOnmouseoverExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="710">

---

SetOnmouseoverExpr just assigns the given value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="710:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setOnmouseoverExpr(String onmouseoverExpr) {
        this.onmouseoverExpr = onmouseoverExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="816">

---

We just returned from ELCheckboxTag.setOnmouseoverExpr. Next, in ELCheckboxTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="816:1:1" line-data="        setOnmouseupExpr(null);">`setOnmouseupExpr`</SwmToken>(null) to clear out any old mouseup handler, making sure no stale event logic sticks around for the next tag instance.

```java
        setOnmouseupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="718">

---

SetOnmouseupExpr just assigns the given string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="718:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken> field. No extra logic, just a plain setter.

```java
    public void setOnmouseupExpr(String onmouseupExpr) {
        this.onmouseupExpr = onmouseupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="817">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="718:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="817:1:1" line-data="        setPropertyExpr(null);">`setPropertyExpr`</SwmToken>(null) to wipe out any old property expression, making sure the tag doesn't reuse stale property values between instances.

```java
        setPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="726">

---

SetPropertyExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="726:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setPropertyExpr(String propertyExpr) {
        this.propertyExpr = propertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="818">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="726:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="818:1:1" line-data="        setStyleExpr(null);">`setStyleExpr`</SwmToken>(null) to clear out any old style expression, making sure the tag doesn't reuse stale styling between instances.

```java
        setStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="734">

---

SetStyleExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="734:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setStyleExpr(String styleExpr) {
        this.styleExpr = styleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="819">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="734:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="819:1:1" line-data="        setStyleClassExpr(null);">`setStyleClassExpr`</SwmToken>(null) to clear out any old CSS class expression, making sure the tag doesn't reuse stale class names between instances.

```java
        setStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="742">

---

SetStyleClassExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="742:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setStyleClassExpr(String styleClassExpr) {
        this.styleClassExpr = styleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="820">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="742:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="820:1:1" line-data="        setStyleIdExpr(null);">`setStyleIdExpr`</SwmToken>(null) to clear out any old style ID expression, making sure the tag doesn't reuse stale element IDs between instances.

```java
        setStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="750">

---

SetStyleIdExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="750:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setStyleIdExpr(String styleIdExpr) {
        this.styleIdExpr = styleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="821">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="750:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="821:1:1" line-data="        setTabindexExpr(null);">`setTabindexExpr`</SwmToken>(null) to clear out any old tabindex expression, making sure the tag doesn't reuse stale tabindex values between instances.

```java
        setTabindexExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="758">

---

SetTabindexExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="758:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setTabindexExpr(String tabindexExpr) {
        this.tabindexExpr = tabindexExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="822">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="758:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="822:1:1" line-data="        setTitleExpr(null);">`setTitleExpr`</SwmToken>(null) to clear out any old title expression, making sure the tag doesn't reuse stale title values between instances.

```java
        setTitleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="766">

---

SetTitleExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="766:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setTitleExpr(String titleExpr) {
        this.titleExpr = titleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="823">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="766:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="823:1:1" line-data="        setTitleKeyExpr(null);">`setTitleKeyExpr`</SwmToken>(null) to clear out any old title key expression, making sure the tag doesn't reuse stale localization keys between instances.

```java
        setTitleKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="774">

---

SetTitleKeyExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="774:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setTitleKeyExpr(String titleKeyExpr) {
        this.titleKeyExpr = titleKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="824">

---

Back in ELCheckboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="774:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="824:1:1" line-data="        setValueExpr(null);">`setValueExpr`</SwmToken>(null) to wipe out any old value expression. This finishes the reset, making sure the tag doesn't reuse stale values between instances.

```java
        setValueExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" line="782">

---

SetValueExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELCheckboxTag.java" pos="782:9:9" line-data="    public void setValueExpr(String valueExpr) {">`valueExpr`</SwmToken>. No extra logic, just a standard setter.

```java
    public void setValueExpr(String valueExpr) {
        this.valueExpr = valueExpr;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
