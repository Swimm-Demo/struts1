---
title: Resetting Multibox Tag State
---
This document outlines how a multibox tag is reset before reuse. All expression fields, both inherited and specific to the multibox tag, are cleared so that the tag does not retain any previous state. The process takes a tag with potential old values and produces a tag ready for reuse.

# Resetting Multibox Tag State

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="727">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="727:5:5" line-data="    public void release() {">`release`</SwmToken>, we start by calling the superclass's release method to handle any cleanup defined higher up. Next, we move to resetting expression fields in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>, which is necessary because <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="41:4:4" line-data="public class ELMultiboxTag extends MultiboxTag {">`ELMultiboxTag`</SwmToken> inherits from it and those fields need to be cleared as part of the overall reset.

```java
    public void release() {
        super.release();
```

---

</SwmSnippet>

## Clearing Resource Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="108">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="108:5:5" line-data="    public void release() {">`release`</SwmToken>, we call the superclass's release and then clear out the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> field by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="110:1:1" line-data="        setIdExpr(null);">`setIdExpr`</SwmToken>(null). This is the start of resetting all expression fields in the tag to avoid holding onto stale data.

```java
    public void release() {
        super.release();
        setIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="85">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken> just assigns the given value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> field. No validation or side effects—just a plain setter.

```java
    public void setIdExpr(String idExpr) {
        this.idExpr = idExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="111">

---

Back in ELResourceTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, we immediately clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="111:1:1" line-data="        setInputExpr(null);">`setInputExpr`</SwmToken>(null) to make sure all expression fields are reset.

```java
        setInputExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="93">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setInputExpr(String inputExpr) {
        this.inputExpr = inputExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="112">

---

Back in ELResourceTag.release, we finish by clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="552:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> with <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="112:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken>(null), making sure all expression fields are reset before the tag is reused or discarded.

```java
        setNameExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="101">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

## Resetting Multibox-Specific Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="729">

---

Back in ELMultiboxTag.release, after clearing the resource tag fields, we start resetting Multibox-specific fields by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="729:1:1" line-data="        setAccesskeyExpr(null);">`setAccesskeyExpr`</SwmToken>(null).

```java
        setAccesskeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="480">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="480:5:5" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`setAccesskeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="480:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setAccesskeyExpr(String accessKeyExpr) {
        this.accessKeyExpr = accessKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="730">

---

Back in ELMultiboxTag.release, after resetting <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="480:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken>, we clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="488:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="730:1:1" line-data="        setAltExpr(null);">`setAltExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setAltExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="488">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="488:5:5" line-data="    public void setAltExpr(String altExpr) {">`setAltExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="488:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setAltExpr(String altExpr) {
        this.altExpr = altExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="731">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="488:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="496:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="731:1:1" line-data="        setAltKeyExpr(null);">`setAltKeyExpr`</SwmToken>(null) to continue the cleanup.

```java
        setAltKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="496">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="496:5:5" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`setAltKeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="496:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setAltKeyExpr(String altKeyExpr) {
        this.altKeyExpr = altKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="732">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="496:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="504:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="732:1:1" line-data="        setBundleExpr(null);">`setBundleExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setBundleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="504">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="504:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="504:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setBundleExpr(String bundleExpr) {
        this.bundleExpr = bundleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="733">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="504:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="512:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="733:1:1" line-data="        setDisabledExpr(null);">`setDisabledExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setDisabledExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="512">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="512:5:5" line-data="    public void setDisabledExpr(String disabledExpr) {">`setDisabledExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="512:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setDisabledExpr(String disabledExpr) {
        this.disabledExpr = disabledExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="734">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="512:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="520:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="734:1:1" line-data="        setErrorKeyExpr(null);">`setErrorKeyExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setErrorKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="520">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="520:5:5" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`setErrorKeyExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="520:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setErrorKeyExpr(String errorKeyExpr) {
        this.errorKeyExpr = errorKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="735">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="520:9:9" line-data="    public void setErrorKeyExpr(String errorKeyExpr) {">`errorKeyExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="528:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="735:1:1" line-data="        setErrorStyleExpr(null);">`setErrorStyleExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setErrorStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="528">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="528:5:5" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`setErrorStyleExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="528:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setErrorStyleExpr(String errorStyleExpr) {
        this.errorStyleExpr = errorStyleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="736">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="528:9:9" line-data="    public void setErrorStyleExpr(String errorStyleExpr) {">`errorStyleExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="536:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="736:1:1" line-data="        setErrorStyleClassExpr(null);">`setErrorStyleClassExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setErrorStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="536">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="536:5:5" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`setErrorStyleClassExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="536:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setErrorStyleClassExpr(String errorStyleClassExpr) {
        this.errorStyleClassExpr = errorStyleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="737">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="536:9:9" line-data="    public void setErrorStyleClassExpr(String errorStyleClassExpr) {">`errorStyleClassExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="544:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="737:1:1" line-data="        setErrorStyleIdExpr(null);">`setErrorStyleIdExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setErrorStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="544">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="544:5:5" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`setErrorStyleIdExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="544:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setErrorStyleIdExpr(String errorStyleIdExpr) {
        this.errorStyleIdExpr = errorStyleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="738">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="544:9:9" line-data="    public void setErrorStyleIdExpr(String errorStyleIdExpr) {">`errorStyleIdExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="552:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="738:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setNameExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="552">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="552:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="552:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="739">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="552:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="560:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="739:1:1" line-data="        setOnblurExpr(null);">`setOnblurExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setOnblurExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="560">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="560:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="560:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setOnblurExpr(String onblurExpr) {
        this.onblurExpr = onblurExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="740">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="560:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="568:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="740:1:1" line-data="        setOnchangeExpr(null);">`setOnchangeExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setOnchangeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="568">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="568:5:5" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`setOnchangeExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="568:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setOnchangeExpr(String onchangeExpr) {
        this.onchangeExpr = onchangeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="741">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="568:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="576:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="741:1:1" line-data="        setOnclickExpr(null);">`setOnclickExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setOnclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="576">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="576:5:5" line-data="    public void setOnclickExpr(String onclickExpr) {">`setOnclickExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="576:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setOnclickExpr(String onclickExpr) {
        this.onclickExpr = onclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="742">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="576:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="584:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="742:1:1" line-data="        setOndblclickExpr(null);">`setOndblclickExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setOndblclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="584">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="584:5:5" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`setOndblclickExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="584:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setOndblclickExpr(String ondblclickExpr) {
        this.ondblclickExpr = ondblclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="743">

---

Back in ELMultiboxTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="584:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>, we reset <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="592:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken> by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="743:1:1" line-data="        setOnfocusExpr(null);">`setOnfocusExpr`</SwmToken>(null) to keep cleaning up the tag's state.

```java
        setOnfocusExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="592">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="592:5:5" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`setOnfocusExpr`</SwmToken> just assigns the value to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="592:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setOnfocusExpr(String onfocusExpr) {
        this.onfocusExpr = onfocusExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="744">

---

Right after returning from ELMultiboxTag.setOnfocusExpr, ELMultiboxTag.release continues by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="744:1:1" line-data="        setOnkeydownExpr(null);">`setOnkeydownExpr`</SwmToken>(null). This clears out any expression tied to the onkeydown event, making sure the tag doesn't keep any leftover state from previous usage. Resetting each event expression like this is how the release method ensures the tag is ready for reuse without stale data.

```java
        setOnkeydownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="600">

---

SetOnkeydownExpr just assigns the given string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="600:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken> field. No checks, no side effects—just a plain setter, exactly as you'd expect.

```java
    public void setOnkeydownExpr(String onkeydownExpr) {
        this.onkeydownExpr = onkeydownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="745">

---

After coming back from ELMultiboxTag.setOnkeydownExpr, ELMultiboxTag.release immediately calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="745:1:1" line-data="        setOnkeypressExpr(null);">`setOnkeypressExpr`</SwmToken>(null). This clears out any onkeypress expression, so the tag doesn't accidentally reuse old event handler data. It's just part of the systematic cleanup of all event-related fields.

```java
        setOnkeypressExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="608">

---

SetOnkeypressExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="608:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken> field to whatever string you pass in. No validation, no logic—just a direct assignment like every other setter here.

```java
    public void setOnkeypressExpr(String onkeypressExpr) {
        this.onkeypressExpr = onkeypressExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="746">

---

After returning from ELMultiboxTag.setOnkeypressExpr, ELMultiboxTag.release goes straight to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="746:1:1" line-data="        setOnkeyupExpr(null);">`setOnkeyupExpr`</SwmToken>(null). This wipes out any onkeyup expression, so the tag doesn't keep any old event handler references around. It's just clearing out all the event fields, one by one.

```java
        setOnkeyupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="616">

---

SetOnkeyupExpr just assigns the string you give it to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="616:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing more.

```java
    public void setOnkeyupExpr(String onkeyupExpr) {
        this.onkeyupExpr = onkeyupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="747">

---

After finishing ELMultiboxTag.setOnkeyupExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="747:1:1" line-data="        setOnmousedownExpr(null);">`setOnmousedownExpr`</SwmToken>(null) next. This clears the onmousedown expression, making sure the tag doesn't keep any mouse event handler state from previous uses. It's just part of the full cleanup sweep.

```java
        setOnmousedownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="624">

---

SetOnmousedownExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="624:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken> field to whatever string you pass. No validation, no side effects—just a direct assignment, standard Java bean style.

```java
    public void setOnmousedownExpr(String onmousedownExpr) {
        this.onmousedownExpr = onmousedownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="748">

---

After ELMultiboxTag.setOnmousedownExpr, ELMultiboxTag.release immediately calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="748:1:1" line-data="        setOnmousemoveExpr(null);">`setOnmousemoveExpr`</SwmToken>(null). This clears the onmousemove expression, so the tag doesn't keep any old mouse movement handler around. It's just another field in the cleanup chain.

```java
        setOnmousemoveExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="632">

---

SetOnmousemoveExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="632:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setOnmousemoveExpr(String onmousemoveExpr) {
        this.onmousemoveExpr = onmousemoveExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="749">

---

After ELMultiboxTag.setOnmousemoveExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="749:1:1" line-data="        setOnmouseoutExpr(null);">`setOnmouseoutExpr`</SwmToken>(null). This clears the onmouseout expression, so the tag doesn't keep any old mouseout handler. It's just another step in making sure all event fields are reset.

```java
        setOnmouseoutExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="640">

---

SetOnmouseoutExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="640:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken> field to the string you give it. No checks, no side effects—just a plain setter, nothing more.

```java
    public void setOnmouseoutExpr(String onmouseoutExpr) {
        this.onmouseoutExpr = onmouseoutExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="750">

---

After ELMultiboxTag.setOnmouseoutExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="750:1:1" line-data="        setOnmouseoverExpr(null);">`setOnmouseoverExpr`</SwmToken>(null). This clears the onmouseover expression, so the tag doesn't keep any old mouseover handler. It's just another field in the reset sequence.

```java
        setOnmouseoverExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="648">

---

SetOnmouseoverExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="648:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setOnmouseoverExpr(String onmouseoverExpr) {
        this.onmouseoverExpr = onmouseoverExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="751">

---

After ELMultiboxTag.setOnmouseoverExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="751:1:1" line-data="        setOnmouseupExpr(null);">`setOnmouseupExpr`</SwmToken>(null). This clears the onmouseup expression, so the tag doesn't keep any old mouseup handler. It's just another field in the reset chain.

```java
        setOnmouseupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="656">

---

SetOnmouseupExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="656:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken> field to the string you pass in. No checks, no logic—just a plain setter, nothing else.

```java
    public void setOnmouseupExpr(String onmouseupExpr) {
        this.onmouseupExpr = onmouseupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="752">

---

After ELMultiboxTag.setOnmouseupExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="752:1:1" line-data="        setPropertyExpr(null);">`setPropertyExpr`</SwmToken>(null). This clears the property expression, so the tag doesn't keep any old property binding. It's just another field in the cleanup sequence.

```java
        setPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="664">

---

SetPropertyExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="664:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setPropertyExpr(String propertyExpr) {
        this.propertyExpr = propertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="753">

---

After ELMultiboxTag.setPropertyExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="753:1:1" line-data="        setStyleExpr(null);">`setStyleExpr`</SwmToken>(null). This clears the style expression, so the tag doesn't keep any old style info. It's just another field in the reset sequence.

```java
        setStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="672">

---

SetStyleExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="672:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setStyleExpr(String styleExpr) {
        this.styleExpr = styleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="754">

---

After ELMultiboxTag.setStyleExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="754:1:1" line-data="        setStyleClassExpr(null);">`setStyleClassExpr`</SwmToken>(null). This clears the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="193:12:12" line-data="     * Instance variable mapped to &quot;styleClass&quot; tag attribute. (Mapping set in">`styleClass`</SwmToken> expression, so the tag doesn't keep any old CSS class info. It's just another field in the reset sequence.

```java
        setStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="680">

---

SetStyleClassExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="680:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setStyleClassExpr(String styleClassExpr) {
        this.styleClassExpr = styleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="755">

---

After ELMultiboxTag.setStyleClassExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="755:1:1" line-data="        setStyleIdExpr(null);">`setStyleIdExpr`</SwmToken>(null). This clears the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="199:12:12" line-data="     * Instance variable mapped to &quot;styleId&quot; tag attribute. (Mapping set in">`styleId`</SwmToken> expression, so the tag doesn't keep any old style id info. It's just another field in the reset sequence.

```java
        setStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="688">

---

SetStyleIdExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="688:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setStyleIdExpr(String styleIdExpr) {
        this.styleIdExpr = styleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="756">

---

After ELMultiboxTag.setStyleIdExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="756:1:1" line-data="        setTabindexExpr(null);">`setTabindexExpr`</SwmToken>(null). This clears the tabindex expression, so the tag doesn't keep any old tabindex info. It's just another field in the reset sequence.

```java
        setTabindexExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="696">

---

SetTabindexExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="696:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setTabindexExpr(String tabindexExpr) {
        this.tabindexExpr = tabindexExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="757">

---

After ELMultiboxTag.setTabindexExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="757:1:1" line-data="        setTitleExpr(null);">`setTitleExpr`</SwmToken>(null). This clears the title expression, so the tag doesn't keep any old title info. It's just another field in the reset sequence.

```java
        setTitleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="704">

---

SetTitleExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="704:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setTitleExpr(String titleExpr) {
        this.titleExpr = titleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="758">

---

After ELMultiboxTag.setTitleExpr, ELMultiboxTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="758:1:1" line-data="        setTitleKeyExpr(null);">`setTitleKeyExpr`</SwmToken>(null). This clears the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="217:12:12" line-data="     * Instance variable mapped to &quot;titleKey&quot; tag attribute. (Mapping set in">`titleKey`</SwmToken> expression, so the tag doesn't keep any old title key info. It's just another field in the reset sequence.

```java
        setTitleKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="712">

---

SetTitleKeyExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="712:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

```java
    public void setTitleKeyExpr(String titleKeyExpr) {
        this.titleKeyExpr = titleKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="759">

---

After ELMultiboxTag.setTitleKeyExpr, ELMultiboxTag.release finishes up by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="759:1:1" line-data="        setValueExpr(null);">`setValueExpr`</SwmToken>(null). This clears the value expression, making sure the tag doesn't keep any old value state. That's the last field reset before the method ends, so the tag is fully cleaned up for reuse.

```java
        setValueExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" line="720">

---

SetValueExpr just assigns the string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELMultiboxTag.java" pos="720:9:9" line-data="    public void setValueExpr(String valueExpr) {">`valueExpr`</SwmToken> field. No checks, no logic—just a plain setter, nothing else.

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
