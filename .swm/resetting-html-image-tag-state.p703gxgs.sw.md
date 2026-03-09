---
title: Resetting HTML Image Tag State
---
This document outlines the process for resetting HTML image tag state as part of tag lifecycle management. All expression fields are cleared to ensure each tag instance starts with a clean state.

# Resetting HTML Image Tag State

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="855">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="855:5:5" line-data="    public void release() {">`release`</SwmToken>, we start by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="856:1:5" line-data="        super.release();">`super.release()`</SwmToken> to make sure any cleanup from the parent class happens first. After that, we move on to clearing out all the expression-related fields in this tag. Next up, we call the release logic in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>, which is the superclass, to make sure its state is reset before we touch the fields specific to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="37:4:4" line-data="public class ELImageTag extends ImageTag {">`ELImageTag`</SwmToken>.

```java
    public void release() {
        super.release();
```

---

</SwmSnippet>

## Clearing Resource Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="108">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="108:5:5" line-data="    public void release() {">`release`</SwmToken>, we call the superclass's release method to handle any cleanup it needs, then we clear out the expression fields (<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>) by setting them to null. This makes sure the tag doesn't keep any old references around.

```java
    public void release() {
        super.release();
        setIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="85">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken> just assigns the given value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> field. No checks, no logic—just a plain setter.

```java
    public void setIdExpr(String idExpr) {
        this.idExpr = idExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="111">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> with <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken>. Now, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="111:1:1" line-data="        setInputExpr(null);">`setInputExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken> field as well, keeping the cleanup consistent across all expression fields in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>.

```java
        setInputExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="93">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken> field to whatever value you pass in. No extra logic, just a direct assignment.

```java
    public void setInputExpr(String inputExpr) {
        this.inputExpr = inputExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="112">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="112:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken>(null) to finish wiping out all expression fields in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>. This makes sure nothing from a previous use sticks around.

```java
        setNameExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="101">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the input value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

## Resetting Image Tag Expression Fields

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="857">

---

We just finished the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken> cleanup. Now, in ELImageTag.release, we start clearing each expression-related field, beginning with <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="857:1:1" line-data="        setAccesskeyExpr(null);">`setAccesskeyExpr`</SwmToken>(null), to make sure no old values stick around.

```java
        setAccesskeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="560">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="560:5:5" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`setAccesskeyExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="560:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken> field to the given value. No extra logic, just a direct setter.

```java
    public void setAccesskeyExpr(String accessKeyExpr) {
        this.accessKeyExpr = accessKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="858">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="560:5:5" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`setAccesskeyExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="858:1:1" line-data="        setAlignExpr(null);">`setAlignExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="568:9:9" line-data="    public void setAlignExpr(String alignExpr) {">`alignExpr`</SwmToken> field. We’re just going down the list, making sure every expression field is reset in ELImageTag.release.

```java
        setAlignExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="568">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="568:5:5" line-data="    public void setAlignExpr(String alignExpr) {">`setAlignExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="568:9:9" line-data="    public void setAlignExpr(String alignExpr) {">`alignExpr`</SwmToken> field. No logic, just a standard setter.

```java
    public void setAlignExpr(String alignExpr) {
        this.alignExpr = alignExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="859">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="568:5:5" line-data="    public void setAlignExpr(String alignExpr) {">`setAlignExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="859:1:1" line-data="        setAltExpr(null);">`setAltExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="576:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken> field. Just making sure every expression property is wiped in ELImageTag.release.

```java
        setAltExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="576">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="576:5:5" line-data="    public void setAltExpr(String altExpr) {">`setAltExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="576:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setAltExpr(String altExpr) {
        this.altExpr = altExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="860">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="576:5:5" line-data="    public void setAltExpr(String altExpr) {">`setAltExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="860:1:1" line-data="        setAltKeyExpr(null);">`setAltKeyExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="584:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setAltKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="584">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="584:5:5" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`setAltKeyExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="584:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setAltKeyExpr(String altKeyExpr) {
        this.altKeyExpr = altKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="861">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="584:5:5" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`setAltKeyExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="861:1:1" line-data="        setBorderExpr(null);">`setBorderExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="592:9:9" line-data="    public void setBorderExpr(String borderExpr) {">`borderExpr`</SwmToken> field. Just moving through each expression property in ELImageTag.release.

```java
        setBorderExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="592">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="592:5:5" line-data="    public void setBorderExpr(String borderExpr) {">`setBorderExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="592:9:9" line-data="    public void setBorderExpr(String borderExpr) {">`borderExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setBorderExpr(String borderExpr) {
        this.borderExpr = borderExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="862">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="592:5:5" line-data="    public void setBorderExpr(String borderExpr) {">`setBorderExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="862:1:1" line-data="        setBundleExpr(null);">`setBundleExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="600:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setBundleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="600">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="600:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="600:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setBundleExpr(String bundleExpr) {
        this.bundleExpr = bundleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="863">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="600:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="863:1:1" line-data="        setDirExpr(null);">`setDirExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="608:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setDirExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="608">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="608:5:5" line-data="    public void setDirExpr(String dirExpr) {">`setDirExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="608:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setDirExpr(String dirExpr) {
        this.dirExpr = dirExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="864">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="608:5:5" line-data="    public void setDirExpr(String dirExpr) {">`setDirExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="864:1:1" line-data="        setDisabledExpr(null);">`setDisabledExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="616:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setDisabledExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="616">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="616:5:5" line-data="    public void setDisabledExpr(String disabledExpr) {">`setDisabledExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="616:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setDisabledExpr(String disabledExpr) {
        this.disabledExpr = disabledExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="865">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="616:5:5" line-data="    public void setDisabledExpr(String disabledExpr) {">`setDisabledExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="865:1:1" line-data="        setIndexedExpr(null);">`setIndexedExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="624:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setIndexedExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="624">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="624:5:5" line-data="    public void setIndexedExpr(String indexedExpr) {">`setIndexedExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="624:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setIndexedExpr(String indexedExpr) {
        this.indexedExpr = indexedExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="866">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="624:5:5" line-data="    public void setIndexedExpr(String indexedExpr) {">`setIndexedExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="866:1:1" line-data="        setLangExpr(null);">`setLangExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="632:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setLangExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="632">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="632:5:5" line-data="    public void setLangExpr(String langExpr) {">`setLangExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="632:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setLangExpr(String langExpr) {
        this.langExpr = langExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="867">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="632:5:5" line-data="    public void setLangExpr(String langExpr) {">`setLangExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="867:1:1" line-data="        setLocaleExpr(null);">`setLocaleExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="640:9:9" line-data="    public void setLocaleExpr(String localeExpr) {">`localeExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setLocaleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="640">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="640:5:5" line-data="    public void setLocaleExpr(String localeExpr) {">`setLocaleExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="640:9:9" line-data="    public void setLocaleExpr(String localeExpr) {">`localeExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setLocaleExpr(String localeExpr) {
        this.localeExpr = localeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="868">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="640:5:5" line-data="    public void setLocaleExpr(String localeExpr) {">`setLocaleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="868:1:1" line-data="        setModuleExpr(null);">`setModuleExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="648:9:9" line-data="    public void setModuleExpr(String moduleExpr) {">`moduleExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setModuleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="648">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="648:5:5" line-data="    public void setModuleExpr(String moduleExpr) {">`setModuleExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="648:9:9" line-data="    public void setModuleExpr(String moduleExpr) {">`moduleExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setModuleExpr(String moduleExpr) {
        this.moduleExpr = moduleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="869">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="648:5:5" line-data="    public void setModuleExpr(String moduleExpr) {">`setModuleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="869:1:1" line-data="        setOnblurExpr(null);">`setOnblurExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="656:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setOnblurExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="656">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="656:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="656:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setOnblurExpr(String onblurExpr) {
        this.onblurExpr = onblurExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="870">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="656:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="870:1:1" line-data="        setOnchangeExpr(null);">`setOnchangeExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="664:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setOnchangeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="664">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="664:5:5" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`setOnchangeExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="664:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setOnchangeExpr(String onchangeExpr) {
        this.onchangeExpr = onchangeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="871">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="664:5:5" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`setOnchangeExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="871:1:1" line-data="        setOnclickExpr(null);">`setOnclickExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="672:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken> field. Just going through each expression property in ELImageTag.release.

```java
        setOnclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="672">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="672:5:5" line-data="    public void setOnclickExpr(String onclickExpr) {">`setOnclickExpr`</SwmToken> just assigns the value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="672:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken> field. No logic, just a direct setter.

```java
    public void setOnclickExpr(String onclickExpr) {
        this.onclickExpr = onclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="872">

---

Back in ELImageTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="672:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="872:1:1" line-data="        setOndblclickExpr(null);">`setOndblclickExpr`</SwmToken>(null) to make sure the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="680:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken> field is also reset. This keeps all event handler expressions clean between uses.

```java
        setOndblclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="680">

---

SetOndblclickExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="680:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct assignment.

```java
    public void setOndblclickExpr(String ondblclickExpr) {
        this.ondblclickExpr = ondblclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="873">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="680:5:5" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`setOndblclickExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="873:1:1" line-data="        setOnfocusExpr(null);">`setOnfocusExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="688:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken> field too. Just making sure every event handler expression is wiped clean.

```java
        setOnfocusExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="688">

---

SetOnfocusExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="688:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnfocusExpr(String onfocusExpr) {
        this.onfocusExpr = onfocusExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="874">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="688:5:5" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`setOnfocusExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="874:1:1" line-data="        setOnkeydownExpr(null);">`setOnkeydownExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="696:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken> field. Just keeping all event handler expressions reset.

```java
        setOnkeydownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="696">

---

SetOnkeydownExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="696:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnkeydownExpr(String onkeydownExpr) {
        this.onkeydownExpr = onkeydownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="875">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="696:5:5" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`setOnkeydownExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="875:1:1" line-data="        setOnkeypressExpr(null);">`setOnkeypressExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="704:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken> field. Just keeping all keyboard event handler expressions reset.

```java
        setOnkeypressExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="704">

---

SetOnkeypressExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="704:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnkeypressExpr(String onkeypressExpr) {
        this.onkeypressExpr = onkeypressExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="876">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="704:5:5" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`setOnkeypressExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="876:1:1" line-data="        setOnkeyupExpr(null);">`setOnkeyupExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="712:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken> field. Just keeping all keyboard event handler expressions reset.

```java
        setOnkeyupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="712">

---

SetOnkeyupExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="712:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnkeyupExpr(String onkeyupExpr) {
        this.onkeyupExpr = onkeyupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="877">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="712:5:5" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`setOnkeyupExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="877:1:1" line-data="        setOnmousedownExpr(null);">`setOnmousedownExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="720:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken> field. Just keeping all mouse event handler expressions reset.

```java
        setOnmousedownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="720">

---

SetOnmousedownExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="720:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnmousedownExpr(String onmousedownExpr) {
        this.onmousedownExpr = onmousedownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="878">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="720:5:5" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`setOnmousedownExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="878:1:1" line-data="        setOnmousemoveExpr(null);">`setOnmousemoveExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="728:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken> field. Just keeping all mouse event handler expressions reset.

```java
        setOnmousemoveExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="728">

---

SetOnmousemoveExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="728:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnmousemoveExpr(String onmousemoveExpr) {
        this.onmousemoveExpr = onmousemoveExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="879">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="728:5:5" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`setOnmousemoveExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="879:1:1" line-data="        setOnmouseoutExpr(null);">`setOnmouseoutExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="736:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken> field. Just keeping all mouse event handler expressions reset.

```java
        setOnmouseoutExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="736">

---

SetOnmouseoutExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="736:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnmouseoutExpr(String onmouseoutExpr) {
        this.onmouseoutExpr = onmouseoutExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="880">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="736:5:5" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`setOnmouseoutExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="880:1:1" line-data="        setOnmouseoverExpr(null);">`setOnmouseoverExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="744:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken> field. Just keeping all mouse event handler expressions reset.

```java
        setOnmouseoverExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="744">

---

SetOnmouseoverExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="744:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnmouseoverExpr(String onmouseoverExpr) {
        this.onmouseoverExpr = onmouseoverExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="881">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="744:5:5" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`setOnmouseoverExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="881:1:1" line-data="        setOnmouseupExpr(null);">`setOnmouseupExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="752:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken> field. Just keeping all mouse event handler expressions reset.

```java
        setOnmouseupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="752">

---

SetOnmouseupExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="752:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setOnmouseupExpr(String onmouseupExpr) {
        this.onmouseupExpr = onmouseupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="882">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="752:5:5" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`setOnmouseupExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="882:1:1" line-data="        setPageExpr(null);">`setPageExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="760:9:9" line-data="    public void setPageExpr(String pageExpr) {">`pageExpr`</SwmToken> field. Just making sure all expression fields are reset.

```java
        setPageExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="760">

---

SetPageExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="760:9:9" line-data="    public void setPageExpr(String pageExpr) {">`pageExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setPageExpr(String pageExpr) {
        this.pageExpr = pageExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="883">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="760:5:5" line-data="    public void setPageExpr(String pageExpr) {">`setPageExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="883:1:1" line-data="        setPageKeyExpr(null);">`setPageKeyExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="768:9:9" line-data="    public void setPageKeyExpr(String pageKeyExpr) {">`pageKeyExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setPageKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="768">

---

SetPageKeyExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="768:9:9" line-data="    public void setPageKeyExpr(String pageKeyExpr) {">`pageKeyExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setPageKeyExpr(String pageKeyExpr) {
        this.pageKeyExpr = pageKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="884">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="768:5:5" line-data="    public void setPageKeyExpr(String pageKeyExpr) {">`setPageKeyExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="884:1:1" line-data="        setPropertyExpr(null);">`setPropertyExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="776:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="776">

---

SetPropertyExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="776:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setPropertyExpr(String propertyExpr) {
        this.propertyExpr = propertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="885">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="776:5:5" line-data="    public void setPropertyExpr(String propertyExpr) {">`setPropertyExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="885:1:1" line-data="        setSrcExpr(null);">`setSrcExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="784:9:9" line-data="    public void setSrcExpr(String srcExpr) {">`srcExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setSrcExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="784">

---

SetSrcExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="784:9:9" line-data="    public void setSrcExpr(String srcExpr) {">`srcExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setSrcExpr(String srcExpr) {
        this.srcExpr = srcExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="886">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="784:5:5" line-data="    public void setSrcExpr(String srcExpr) {">`setSrcExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="886:1:1" line-data="        setSrcKeyExpr(null);">`setSrcKeyExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="792:9:9" line-data="    public void setSrcKeyExpr(String srcKeyExpr) {">`srcKeyExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setSrcKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="792">

---

SetSrcKeyExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="792:9:9" line-data="    public void setSrcKeyExpr(String srcKeyExpr) {">`srcKeyExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setSrcKeyExpr(String srcKeyExpr) {
        this.srcKeyExpr = srcKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="887">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="792:5:5" line-data="    public void setSrcKeyExpr(String srcKeyExpr) {">`setSrcKeyExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="887:1:1" line-data="        setStyleExpr(null);">`setStyleExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="800:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="800">

---

SetStyleExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="800:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setStyleExpr(String styleExpr) {
        this.styleExpr = styleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="888">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="800:5:5" line-data="    public void setStyleExpr(String styleExpr) {">`setStyleExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="888:1:1" line-data="        setStyleClassExpr(null);">`setStyleClassExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="808:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="808">

---

SetStyleClassExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="808:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setStyleClassExpr(String styleClassExpr) {
        this.styleClassExpr = styleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="889">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="808:5:5" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`setStyleClassExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="889:1:1" line-data="        setStyleIdExpr(null);">`setStyleIdExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="816:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="816">

---

SetStyleIdExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="816:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setStyleIdExpr(String styleIdExpr) {
        this.styleIdExpr = styleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="890">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="816:5:5" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`setStyleIdExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="890:1:1" line-data="        setTabindexExpr(null);">`setTabindexExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="824:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setTabindexExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="824">

---

SetTabindexExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="824:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setTabindexExpr(String tabindexExpr) {
        this.tabindexExpr = tabindexExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="891">

---

After <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="824:5:5" line-data="    public void setTabindexExpr(String tabindexExpr) {">`setTabindexExpr`</SwmToken>(null), ELImageTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="891:1:1" line-data="        setTitleExpr(null);">`setTitleExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="832:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken> field. Just keeping all related expression fields reset.

```java
        setTitleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="832">

---

SetTitleExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="832:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setTitleExpr(String titleExpr) {
        this.titleExpr = titleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="892">

---

Back in ELImageTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="832:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="892:1:1" line-data="        setTitleKeyExpr(null);">`setTitleKeyExpr`</SwmToken>(null) to make sure the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="840:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken> field is also reset. This prevents any old title key values from sticking around and affecting future tag usage.

```java
        setTitleKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="840">

---

SetTitleKeyExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="840:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

```java
    public void setTitleKeyExpr(String titleKeyExpr) {
        this.titleKeyExpr = titleKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="893">

---

Back in ELImageTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="840:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="893:1:1" line-data="        setValueExpr(null);">`setValueExpr`</SwmToken>(null) to finish wiping out all expression fields. This makes sure <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="848:9:9" line-data="    public void setValueExpr(String valueExpr) {">`valueExpr`</SwmToken> doesn't carry over any old data between uses.

```java
        setValueExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" line="848">

---

SetValueExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELImageTag.java" pos="848:9:9" line-data="    public void setValueExpr(String valueExpr) {">`valueExpr`</SwmToken> field to whatever String you pass in. No logic, just a direct setter.

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
