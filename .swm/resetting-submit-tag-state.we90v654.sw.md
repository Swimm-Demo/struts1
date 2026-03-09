---
title: Resetting Submit Tag State
---
This document describes how the submit tag's state is reset as part of its lifecycle. When a submit tag is reused, all dynamic expression properties are cleared to prevent old values from persisting. The process ensures that both resource-level and submit-level expressions are systematically removed.

# Resetting Submit Tag State

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="679">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="679:5:5" line-data="    public void release() {">`release`</SwmToken>, we start by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="680:1:5" line-data="        super.release();">`super.release()`</SwmToken> to make sure any cleanup from the parent class happens first. After that, we move on to clearing out our own expression fields, which means next up is the logic in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>. That class handles more expression properties, so we delegate to it to keep the cleanup consistent across the tag hierarchy.

```java
    public void release() {
        super.release();
```

---

</SwmSnippet>

## Clearing Resource Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="108">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="108:5:5" line-data="    public void release() {">`release`</SwmToken>, we call the superclass cleanup first, then start clearing out the expression fields like <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>. We do this to drop any references to dynamic expressions, so the tag doesn't hang onto old state if it's reused. Next, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> to keep clearing out the rest of the expression fields.

```java
    public void release() {
        super.release();
        setIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="85">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken> just assigns the given value to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> field. No checks, no extra logic—just a plain setter. After this, we move on to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>.

```java
    public void setIdExpr(String idExpr) {
        this.idExpr = idExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="111">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> with <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken>. Now, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="111:1:1" line-data="        setInputExpr(null);">`setInputExpr`</SwmToken> to clear the next expression field, keeping the cleanup consistent for all dynamic properties.

```java
        setInputExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="93">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken> field to whatever you pass in. Here, it's being set to null as part of the cleanup. Next up is clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>.

```java
    public void setInputExpr(String inputExpr) {
        this.inputExpr = inputExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="112">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>. Now, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="112:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken> to drop the last expression reference in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>. This wraps up the cleanup for all expression fields in this tag.

```java
        setNameExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="101">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to finish off the cleanup in <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

## Clearing Submit Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="681">

---

We just got back from cleaning up <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="38:4:4" line-data="public class ELResourceTag extends ResourceTag {">`ELResourceTag`</SwmToken>. Now, in ELSubmitTag.release, we start clearing out all the dynamic expression fields, starting with accesskeyExpr. This keeps the tag from holding onto any old state.

```java
        setAccesskeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="448">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="448:5:5" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`setAccesskeyExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="448:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setAccesskeyExpr(String accessKeyExpr) {
        this.accessKeyExpr = accessKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="682">

---

We just finished clearing accesskeyExpr. Now, ELSubmitTag.release moves on to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="456:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>, keeping the cleanup consistent for all dynamic fields.

```java
        setAltExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="456">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="456:5:5" line-data="    public void setAltExpr(String altExpr) {">`setAltExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="456:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setAltExpr(String altExpr) {
        this.altExpr = altExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="683">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="456:9:9" line-data="    public void setAltExpr(String altExpr) {">`altExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="464:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>, continuing the cleanup for all expression fields.

```java
        setAltKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="464">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="464:5:5" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`setAltKeyExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="464:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setAltKeyExpr(String altKeyExpr) {
        this.altKeyExpr = altKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="684">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="464:9:9" line-data="    public void setAltKeyExpr(String altKeyExpr) {">`altKeyExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="472:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setBundleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="472">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="472:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="472:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setBundleExpr(String bundleExpr) {
        this.bundleExpr = bundleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="685">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="472:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="480:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setDirExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="480">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="480:5:5" line-data="    public void setDirExpr(String dirExpr) {">`setDirExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="480:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setDirExpr(String dirExpr) {
        this.dirExpr = dirExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="686">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="480:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="488:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setDisabledExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="488">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="488:5:5" line-data="    public void setDisabledExpr(String disabledExpr) {">`setDisabledExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="488:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setDisabledExpr(String disabledExpr) {
        this.disabledExpr = disabledExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="687">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="488:9:9" line-data="    public void setDisabledExpr(String disabledExpr) {">`disabledExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="496:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setIndexedExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="496">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="496:5:5" line-data="    public void setIndexedExpr(String indexedExpr) {">`setIndexedExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="496:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setIndexedExpr(String indexedExpr) {
        this.indexedExpr = indexedExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="688">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="496:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="504:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setLangExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="504">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="504:5:5" line-data="    public void setLangExpr(String langExpr) {">`setLangExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="504:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setLangExpr(String langExpr) {
        this.langExpr = langExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="689">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="504:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="512:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnblurExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="512">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="512:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="512:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnblurExpr(String onblurExpr) {
        this.onblurExpr = onblurExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="690">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="512:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="520:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnchangeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="520">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="520:5:5" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`setOnchangeExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="520:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnchangeExpr(String onchangeExpr) {
        this.onchangeExpr = onchangeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="691">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="520:9:9" line-data="    public void setOnchangeExpr(String onchangeExpr) {">`onchangeExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="528:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="528">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="528:5:5" line-data="    public void setOnclickExpr(String onclickExpr) {">`setOnclickExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="528:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnclickExpr(String onclickExpr) {
        this.onclickExpr = onclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="692">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="528:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="536:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOndblclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="536">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="536:5:5" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`setOndblclickExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="536:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOndblclickExpr(String ondblclickExpr) {
        this.ondblclickExpr = ondblclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="693">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="536:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="544:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnfocusExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="544">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="544:5:5" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`setOnfocusExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="544:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnfocusExpr(String onfocusExpr) {
        this.onfocusExpr = onfocusExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="694">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="544:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="552:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnkeydownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="552">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="552:5:5" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`setOnkeydownExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="552:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnkeydownExpr(String onkeydownExpr) {
        this.onkeydownExpr = onkeydownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="695">

---

We just finished clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="552:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken>. Now, ELSubmitTag.release clears <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="560:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken>, continuing the systematic cleanup.

```java
        setOnkeypressExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="560">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="560:5:5" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`setOnkeypressExpr`</SwmToken> just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="560:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken> field. No checks, no extra logic—just a plain setter to clear the field.

```java
    public void setOnkeypressExpr(String onkeypressExpr) {
        this.onkeypressExpr = onkeypressExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="696">

---

We just got back from ELSubmitTag.setOnkeypressExpr. Now ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="696:1:1" line-data="        setOnkeyupExpr(null);">`setOnkeyupExpr`</SwmToken>(null) to clear out any leftover dynamic expression for the onkeyup event. This keeps the tag from holding onto old state and makes sure every event-related property gets reset before reuse.

```java
        setOnkeyupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="568">

---

SetOnkeyupExpr just assigns the given string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="568:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken> field. No checks, no extra logic—just a plain setter. This is exactly what the method name and signature say.

```java
    public void setOnkeyupExpr(String onkeyupExpr) {
        this.onkeyupExpr = onkeyupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="697">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="568:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken>, ELSubmitTag.release moves on to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="697:1:1" line-data="        setOnmousedownExpr(null);">`setOnmousedownExpr`</SwmToken>(null). This keeps the cleanup consistent for all mouse event expressions, making sure nothing sticks around between uses.

```java
        setOnmousedownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="576">

---

SetOnmousedownExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="576:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken> field to whatever string you pass in. No validation, no extra logic—just a standard setter.

```java
    public void setOnmousedownExpr(String onmousedownExpr) {
        this.onmousedownExpr = onmousedownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="698">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="576:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="698:1:1" line-data="        setOnmousemoveExpr(null);">`setOnmousemoveExpr`</SwmToken>(null) to wipe out any dynamic expression for mousemove. This keeps the tag's event state clean for the next time it's used.

```java
        setOnmousemoveExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="584">

---

SetOnmousemoveExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="584:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setOnmousemoveExpr(String onmousemoveExpr) {
        this.onmousemoveExpr = onmousemoveExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="699">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="584:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="699:1:1" line-data="        setOnmouseoutExpr(null);">`setOnmouseoutExpr`</SwmToken>(null) to drop any leftover mouseout expression. This keeps the tag's event properties consistent and ready for reuse.

```java
        setOnmouseoutExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="592">

---

SetOnmouseoutExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="592:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken> field to whatever string you pass in. No checks, no extra logic—just a standard setter.

```java
    public void setOnmouseoutExpr(String onmouseoutExpr) {
        this.onmouseoutExpr = onmouseoutExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="700">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="592:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="700:1:1" line-data="        setOnmouseoverExpr(null);">`setOnmouseoverExpr`</SwmToken>(null) to clear out any dynamic mouseover expression. This keeps the tag's event state clean and avoids leftover values.

```java
        setOnmouseoverExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="600">

---

SetOnmouseoverExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="600:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setOnmouseoverExpr(String onmouseoverExpr) {
        this.onmouseoverExpr = onmouseoverExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="701">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="600:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="701:1:1" line-data="        setOnmouseupExpr(null);">`setOnmouseupExpr`</SwmToken>(null) to clear out any dynamic mouseup expression. This keeps the tag's event state consistent and avoids stale values.

```java
        setOnmouseupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="608">

---

SetOnmouseupExpr just sets the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="608:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken> field to whatever string you pass in. No checks, no extra logic—just a standard setter.

```java
    public void setOnmouseupExpr(String onmouseupExpr) {
        this.onmouseupExpr = onmouseupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="702">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="608:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="702:1:1" line-data="        setPropertyExpr(null);">`setPropertyExpr`</SwmToken>(null) to clear out any dynamic property expression. This keeps the tag's property state clean and avoids leftover values.

```java
        setPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="616">

---

SetPropertyExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="616:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setPropertyExpr(String propertyExpr) {
        this.propertyExpr = propertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="703">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="616:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="703:1:1" line-data="        setStyleExpr(null);">`setStyleExpr`</SwmToken>(null) to clear out any dynamic style expression. This keeps the tag's style state clean and avoids leftover values.

```java
        setStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="624">

---

SetStyleExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="624:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setStyleExpr(String styleExpr) {
        this.styleExpr = styleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="704">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="624:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="704:1:1" line-data="        setStyleClassExpr(null);">`setStyleClassExpr`</SwmToken>(null) to clear out any dynamic <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="177:12:12" line-data="     * Instance variable mapped to &quot;styleClass&quot; tag attribute. (Mapping set in">`styleClass`</SwmToken> expression. This keeps the tag's style class state clean and avoids leftover values.

```java
        setStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="632">

---

SetStyleClassExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="632:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setStyleClassExpr(String styleClassExpr) {
        this.styleClassExpr = styleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="705">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="632:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="705:1:1" line-data="        setStyleIdExpr(null);">`setStyleIdExpr`</SwmToken>(null) to clear out any dynamic <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="183:12:12" line-data="     * Instance variable mapped to &quot;styleId&quot; tag attribute. (Mapping set in">`styleId`</SwmToken> expression. This keeps the tag's style id state clean and avoids leftover values.

```java
        setStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="640">

---

SetStyleIdExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="640:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setStyleIdExpr(String styleIdExpr) {
        this.styleIdExpr = styleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="706">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="640:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="706:1:1" line-data="        setTabindexExpr(null);">`setTabindexExpr`</SwmToken>(null) to clear out any dynamic tabindex expression. This keeps the tag's tabindex state clean and avoids leftover values.

```java
        setTabindexExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="648">

---

SetTabindexExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="648:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setTabindexExpr(String tabindexExpr) {
        this.tabindexExpr = tabindexExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="707">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="648:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="707:1:1" line-data="        setTitleExpr(null);">`setTitleExpr`</SwmToken>(null) to clear out any dynamic title expression. This keeps the tag's title state clean and avoids leftover values.

```java
        setTitleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="656">

---

SetTitleExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="656:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setTitleExpr(String titleExpr) {
        this.titleExpr = titleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="708">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="656:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="708:1:1" line-data="        setTitleKeyExpr(null);">`setTitleKeyExpr`</SwmToken>(null) to clear out any dynamic <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="201:12:12" line-data="     * Instance variable mapped to &quot;titleKey&quot; tag attribute. (Mapping set in">`titleKey`</SwmToken> expression. This keeps the tag's title key state clean and avoids leftover values.

```java
        setTitleKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="664">

---

SetTitleKeyExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="664:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

```java
    public void setTitleKeyExpr(String titleKeyExpr) {
        this.titleKeyExpr = titleKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="709">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="664:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken>, ELSubmitTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="709:1:1" line-data="        setValueExpr(null);">`setValueExpr`</SwmToken>(null) as the last step. This wipes out any dynamic value expression, finishing the reset for all tag properties so nothing hangs around between uses.

```java
        setValueExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" line="672">

---

SetValueExpr just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELSubmitTag.java" pos="672:9:9" line-data="    public void setValueExpr(String valueExpr) {">`valueExpr`</SwmToken> field. No extra logic, no checks—just a plain setter.

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
