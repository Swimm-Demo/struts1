---
title: Resetting Tag State for Reuse
---
This document describes how tag instances are reset to ensure they are ready for reuse without retaining any previous state. The process involves clearing all expression fields managed by both resource and link tags, making sure that each tag instance is clean and safe for the next usage.

# Resetting Link Tag State

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="943">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="943:5:5" line-data="    public void release() {">`release`</SwmToken>, we start by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="944:1:5" line-data="        super.release();">`super.release()`</SwmToken> to make sure any cleanup from the parent class happens first. Then, we move on to resetting fields specific to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="37:4:4" line-data="public class ELLinkTag extends LinkTag {">`ELLinkTag`</SwmToken>. Next up, we need to call ELResourceTag.release to clear out expression fields managed by that tag, keeping the cleanup consistent across related tag classes.

```java
    public void release() {
        super.release();
```

---

</SwmSnippet>

## Clearing Resource Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="108">

---

In <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="108:5:5" line-data="    public void release() {">`release`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="109:1:5" line-data="        super.release();">`super.release()`</SwmToken> first, then clear out <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, and <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="712:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken> using their setters. This drops any references those fields held, making sure the tag is ready for reuse and doesn't hang onto old data.

```java
    public void release() {
        super.release();
        setIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="85">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:5:5" line-data="    public void setIdExpr(String idExpr) {">`setIdExpr`</SwmToken> just assigns the input string to the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken> field. Nothing fancy—straightforward setter.

```java
    public void setIdExpr(String idExpr) {
        this.idExpr = idExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="111">

---

Back in ELResourceTag.release, after clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="85:9:9" line-data="    public void setIdExpr(String idExpr) {">`idExpr`</SwmToken>, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="111:1:1" line-data="        setInputExpr(null);">`setInputExpr`</SwmToken>(null) to drop any reference held by <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>. This keeps the cleanup thorough for all expression fields.

```java
        setInputExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="93">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:5:5" line-data="    public void setInputExpr(String inputExpr) {">`setInputExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>. It's a plain setter—no extra logic.

```java
    public void setInputExpr(String inputExpr) {
        this.inputExpr = inputExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="112">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="93:9:9" line-data="    public void setInputExpr(String inputExpr) {">`inputExpr`</SwmToken>, we finish up ELResourceTag.release by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="112:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken>(null) to clear the last expression field. This wraps up the cleanup for all managed expressions.

```java
        setNameExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" line="101">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/bean/ELResourceTag.java" pos="101:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

## Resetting Link Tag Expressions

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="945">

---

Back in ELLinkTag.release, we start clearing expression fields by calling <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="945:1:1" line-data="        setAccesskeyExpr(null);">`setAccesskeyExpr`</SwmToken>(null). This drops any reference held by <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="616:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken>, kicking off the cleanup for the tag.

```java
        setAccesskeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="616">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="616:5:5" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`setAccesskeyExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="616:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken>. It's a plain setter—no extra logic.

```java
    public void setAccesskeyExpr(String accessKeyExpr) {
        this.accessKeyExpr = accessKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="946">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="616:9:9" line-data="    public void setAccesskeyExpr(String accessKeyExpr) {">`accessKeyExpr`</SwmToken>, ELLinkTag.release moves on to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="946:1:1" line-data="        setActionExpr(null);">`setActionExpr`</SwmToken>(null) to clear the <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="624:9:9" line-data="    public void setActionExpr(String actionExpr) {">`actionExpr`</SwmToken> field. This keeps the cleanup going for all expression fields.

```java
        setActionExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="624">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="624:5:5" line-data="    public void setActionExpr(String actionExpr) {">`setActionExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="624:9:9" line-data="    public void setActionExpr(String actionExpr) {">`actionExpr`</SwmToken>. It's a straightforward setter—no extra logic.

```java
    public void setActionExpr(String actionExpr) {
        this.actionExpr = actionExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="947">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="624:9:9" line-data="    public void setActionExpr(String actionExpr) {">`actionExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="947:1:1" line-data="        setModuleExpr(null);">`setModuleExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="632:9:9" line-data="    public void setModuleExpr(String moduleExpr) {">`moduleExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setModuleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="632">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="632:5:5" line-data="    public void setModuleExpr(String moduleExpr) {">`setModuleExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="632:9:9" line-data="    public void setModuleExpr(String moduleExpr) {">`moduleExpr`</SwmToken>. It's a plain setter—no extra logic.

```java
    public void setModuleExpr(String moduleExpr) {
        this.moduleExpr = moduleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="948">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="632:9:9" line-data="    public void setModuleExpr(String moduleExpr) {">`moduleExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="948:1:1" line-data="        setAnchorExpr(null);">`setAnchorExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="640:9:9" line-data="    public void setAnchorExpr(String anchorExpr) {">`anchorExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setAnchorExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="640">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="640:5:5" line-data="    public void setAnchorExpr(String anchorExpr) {">`setAnchorExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="640:9:9" line-data="    public void setAnchorExpr(String anchorExpr) {">`anchorExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setAnchorExpr(String anchorExpr) {
        this.anchorExpr = anchorExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="949">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="640:9:9" line-data="    public void setAnchorExpr(String anchorExpr) {">`anchorExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="949:1:1" line-data="        setBundleExpr(null);">`setBundleExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="648:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setBundleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="648">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="648:5:5" line-data="    public void setBundleExpr(String bundleExpr) {">`setBundleExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="648:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>. It's a plain setter—no extra logic.

```java
    public void setBundleExpr(String bundleExpr) {
        this.bundleExpr = bundleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="950">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="648:9:9" line-data="    public void setBundleExpr(String bundleExpr) {">`bundleExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="950:1:1" line-data="        setDirExpr(null);">`setDirExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="656:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setDirExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="656">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="656:5:5" line-data="    public void setDirExpr(String dirExpr) {">`setDirExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="656:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setDirExpr(String dirExpr) {
        this.dirExpr = dirExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="951">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="656:9:9" line-data="    public void setDirExpr(String dirExpr) {">`dirExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="951:1:1" line-data="        setForwardExpr(null);">`setForwardExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="664:9:9" line-data="    public void setForwardExpr(String forwardExpr) {">`forwardExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setForwardExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="664">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="664:5:5" line-data="    public void setForwardExpr(String forwardExpr) {">`setForwardExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="664:9:9" line-data="    public void setForwardExpr(String forwardExpr) {">`forwardExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setForwardExpr(String forwardExpr) {
        this.forwardExpr = forwardExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="952">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="664:9:9" line-data="    public void setForwardExpr(String forwardExpr) {">`forwardExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="952:1:1" line-data="        setHrefExpr(null);">`setHrefExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="672:9:9" line-data="    public void setHrefExpr(String hrefExpr) {">`hrefExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setHrefExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="672">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="672:5:5" line-data="    public void setHrefExpr(String hrefExpr) {">`setHrefExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="672:9:9" line-data="    public void setHrefExpr(String hrefExpr) {">`hrefExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setHrefExpr(String hrefExpr) {
        this.hrefExpr = hrefExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="953">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="672:9:9" line-data="    public void setHrefExpr(String hrefExpr) {">`hrefExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="953:1:1" line-data="        setIndexedExpr(null);">`setIndexedExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="680:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setIndexedExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="680">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="680:5:5" line-data="    public void setIndexedExpr(String indexedExpr) {">`setIndexedExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="680:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setIndexedExpr(String indexedExpr) {
        this.indexedExpr = indexedExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="954">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="680:9:9" line-data="    public void setIndexedExpr(String indexedExpr) {">`indexedExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="954:1:1" line-data="        setIndexIdExpr(null);">`setIndexIdExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="688:9:9" line-data="    public void setIndexIdExpr(String indexIdExpr) {">`indexIdExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setIndexIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="688">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="688:5:5" line-data="    public void setIndexIdExpr(String indexIdExpr) {">`setIndexIdExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="688:9:9" line-data="    public void setIndexIdExpr(String indexIdExpr) {">`indexIdExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setIndexIdExpr(String indexIdExpr) {
        this.indexIdExpr = indexIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="955">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="688:9:9" line-data="    public void setIndexIdExpr(String indexIdExpr) {">`indexIdExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="955:1:1" line-data="        setLangExpr(null);">`setLangExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="696:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setLangExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="696">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="696:5:5" line-data="    public void setLangExpr(String langExpr) {">`setLangExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="696:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setLangExpr(String langExpr) {
        this.langExpr = langExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="956">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="696:9:9" line-data="    public void setLangExpr(String langExpr) {">`langExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="956:1:1" line-data="        setLinkNameExpr(null);">`setLinkNameExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="704:9:9" line-data="    public void setLinkNameExpr(String linkNameExpr) {">`linkNameExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setLinkNameExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="704">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="704:5:5" line-data="    public void setLinkNameExpr(String linkNameExpr) {">`setLinkNameExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="704:9:9" line-data="    public void setLinkNameExpr(String linkNameExpr) {">`linkNameExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setLinkNameExpr(String linkNameExpr) {
        this.linkNameExpr = linkNameExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="957">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="704:9:9" line-data="    public void setLinkNameExpr(String linkNameExpr) {">`linkNameExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="957:1:1" line-data="        setNameExpr(null);">`setNameExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="712:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setNameExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="712">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="712:5:5" line-data="    public void setNameExpr(String nameExpr) {">`setNameExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="712:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setNameExpr(String nameExpr) {
        this.nameExpr = nameExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="958">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="712:9:9" line-data="    public void setNameExpr(String nameExpr) {">`nameExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="958:1:1" line-data="        setOnblurExpr(null);">`setOnblurExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="720:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setOnblurExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="720">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="720:5:5" line-data="    public void setOnblurExpr(String onblurExpr) {">`setOnblurExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="720:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setOnblurExpr(String onblurExpr) {
        this.onblurExpr = onblurExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="959">

---

After clearing <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="720:9:9" line-data="    public void setOnblurExpr(String onblurExpr) {">`onblurExpr`</SwmToken>, ELLinkTag.release calls <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="959:1:1" line-data="        setOnclickExpr(null);">`setOnclickExpr`</SwmToken>(null) to clear <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="728:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>. This keeps the cleanup moving through all expression fields.

```java
        setOnclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="728">

---

<SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="728:5:5" line-data="    public void setOnclickExpr(String onclickExpr) {">`setOnclickExpr`</SwmToken> just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="728:9:9" line-data="    public void setOnclickExpr(String onclickExpr) {">`onclickExpr`</SwmToken>. It's a basic setter—no extra logic.

```java
    public void setOnclickExpr(String onclickExpr) {
        this.onclickExpr = onclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="960">

---

We just returned from ELLinkTag.setOnclickExpr in ELLinkTag.release. Now we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="960:1:1" line-data="        setOndblclickExpr(null);">`setOndblclickExpr`</SwmToken>(null) to make sure any double-click event handler expression is cleared out, so the tag doesn't accidentally reuse old event logic on the next usage.

```java
        setOndblclickExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="736">

---

SetOndblclickExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="736:9:9" line-data="    public void setOndblclickExpr(String ondblclickExpr) {">`ondblclickExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOndblclickExpr(String ondblclickExpr) {
        this.ondblclickExpr = ondblclickExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="961">

---

After returning from ELLinkTag.setOndblclickExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="961:1:1" line-data="        setOnfocusExpr(null);">`setOnfocusExpr`</SwmToken>(null) to clear any focus event handler expression, making sure no leftover logic sticks around for the next tag usage.

```java
        setOnfocusExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="744">

---

SetOnfocusExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="744:9:9" line-data="    public void setOnfocusExpr(String onfocusExpr) {">`onfocusExpr`</SwmToken>. No checks, no extra logic—just a standard setter.

```java
    public void setOnfocusExpr(String onfocusExpr) {
        this.onfocusExpr = onfocusExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="962">

---

After returning from ELLinkTag.setOnfocusExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="962:1:1" line-data="        setOnkeydownExpr(null);">`setOnkeydownExpr`</SwmToken>(null) to clear any keydown event handler expression, so the tag doesn't accidentally reuse old keyboard logic.

```java
        setOnkeydownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="752">

---

SetOnkeydownExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="752:9:9" line-data="    public void setOnkeydownExpr(String onkeydownExpr) {">`onkeydownExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnkeydownExpr(String onkeydownExpr) {
        this.onkeydownExpr = onkeydownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="963">

---

After returning from ELLinkTag.setOnkeydownExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="963:1:1" line-data="        setOnkeypressExpr(null);">`setOnkeypressExpr`</SwmToken>(null) to clear any keypress event handler expression, so the tag doesn't accidentally reuse old keyboard logic.

```java
        setOnkeypressExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="760">

---

SetOnkeypressExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="760:9:9" line-data="    public void setOnkeypressExpr(String onkeypressExpr) {">`onkeypressExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnkeypressExpr(String onkeypressExpr) {
        this.onkeypressExpr = onkeypressExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="964">

---

After returning from ELLinkTag.setOnkeypressExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="964:1:1" line-data="        setOnkeyupExpr(null);">`setOnkeyupExpr`</SwmToken>(null) to clear any keyup event handler expression, so the tag doesn't accidentally reuse old keyboard logic.

```java
        setOnkeyupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="768">

---

SetOnkeyupExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="768:9:9" line-data="    public void setOnkeyupExpr(String onkeyupExpr) {">`onkeyupExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnkeyupExpr(String onkeyupExpr) {
        this.onkeyupExpr = onkeyupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="965">

---

After returning from ELLinkTag.setOnkeyupExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="965:1:1" line-data="        setOnmousedownExpr(null);">`setOnmousedownExpr`</SwmToken>(null) to clear any mousedown event handler expression, so the tag doesn't accidentally reuse old mouse logic.

```java
        setOnmousedownExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="776">

---

SetOnmousedownExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="776:9:9" line-data="    public void setOnmousedownExpr(String onmousedownExpr) {">`onmousedownExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnmousedownExpr(String onmousedownExpr) {
        this.onmousedownExpr = onmousedownExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="966">

---

After returning from ELLinkTag.setOnmousedownExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="966:1:1" line-data="        setOnmousemoveExpr(null);">`setOnmousemoveExpr`</SwmToken>(null) to clear any mousemove event handler expression, so the tag doesn't accidentally reuse old mouse logic.

```java
        setOnmousemoveExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="784">

---

SetOnmousemoveExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="784:9:9" line-data="    public void setOnmousemoveExpr(String onmousemoveExpr) {">`onmousemoveExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnmousemoveExpr(String onmousemoveExpr) {
        this.onmousemoveExpr = onmousemoveExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="967">

---

After returning from ELLinkTag.setOnmousemoveExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="967:1:1" line-data="        setOnmouseoutExpr(null);">`setOnmouseoutExpr`</SwmToken>(null) to clear any mouseout event handler expression, so the tag doesn't accidentally reuse old mouse logic.

```java
        setOnmouseoutExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="792">

---

SetOnmouseoutExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="792:9:9" line-data="    public void setOnmouseoutExpr(String onmouseoutExpr) {">`onmouseoutExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnmouseoutExpr(String onmouseoutExpr) {
        this.onmouseoutExpr = onmouseoutExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="968">

---

After returning from ELLinkTag.setOnmouseoutExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="968:1:1" line-data="        setOnmouseoverExpr(null);">`setOnmouseoverExpr`</SwmToken>(null) to clear any mouseover event handler expression, so the tag doesn't accidentally reuse old mouse logic.

```java
        setOnmouseoverExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="800">

---

SetOnmouseoverExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="800:9:9" line-data="    public void setOnmouseoverExpr(String onmouseoverExpr) {">`onmouseoverExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnmouseoverExpr(String onmouseoverExpr) {
        this.onmouseoverExpr = onmouseoverExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="969">

---

After returning from ELLinkTag.setOnmouseoverExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="969:1:1" line-data="        setOnmouseupExpr(null);">`setOnmouseupExpr`</SwmToken>(null) to clear any mouseup event handler expression, so the tag doesn't accidentally reuse old mouse logic.

```java
        setOnmouseupExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="808">

---

SetOnmouseupExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="808:9:9" line-data="    public void setOnmouseupExpr(String onmouseupExpr) {">`onmouseupExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setOnmouseupExpr(String onmouseupExpr) {
        this.onmouseupExpr = onmouseupExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="970">

---

After returning from ELLinkTag.setOnmouseupExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="970:1:1" line-data="        setPageExpr(null);">`setPageExpr`</SwmToken>(null) to clear any page expression, so the tag doesn't accidentally reuse old navigation logic.

```java
        setPageExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="816">

---

SetPageExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="816:9:9" line-data="    public void setPageExpr(String pageExpr) {">`pageExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setPageExpr(String pageExpr) {
        this.pageExpr = pageExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="971">

---

After returning from ELLinkTag.setPageExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="971:1:1" line-data="        setParamIdExpr(null);">`setParamIdExpr`</SwmToken>(null) to clear any parameter ID expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setParamIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="824">

---

SetParamIdExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="824:9:9" line-data="    public void setParamIdExpr(String paramIdExpr) {">`paramIdExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setParamIdExpr(String paramIdExpr) {
        this.paramIdExpr = paramIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="972">

---

After returning from ELLinkTag.setParamIdExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="972:1:1" line-data="        setParamNameExpr(null);">`setParamNameExpr`</SwmToken>(null) to clear any parameter name expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setParamNameExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="832">

---

SetParamNameExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="832:9:9" line-data="    public void setParamNameExpr(String paramNameExpr) {">`paramNameExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setParamNameExpr(String paramNameExpr) {
        this.paramNameExpr = paramNameExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="973">

---

After returning from ELLinkTag.setParamNameExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="973:1:1" line-data="        setParamPropertyExpr(null);">`setParamPropertyExpr`</SwmToken>(null) to clear any parameter property expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setParamPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="840">

---

SetParamPropertyExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="840:9:9" line-data="    public void setParamPropertyExpr(String paramPropertyExpr) {">`paramPropertyExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setParamPropertyExpr(String paramPropertyExpr) {
        this.paramPropertyExpr = paramPropertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="974">

---

After returning from ELLinkTag.setParamPropertyExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="974:1:1" line-data="        setParamScopeExpr(null);">`setParamScopeExpr`</SwmToken>(null) to clear any parameter scope expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setParamScopeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="848">

---

SetParamScopeExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="848:9:9" line-data="    public void setParamScopeExpr(String paramScopeExpr) {">`paramScopeExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setParamScopeExpr(String paramScopeExpr) {
        this.paramScopeExpr = paramScopeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="975">

---

After returning from ELLinkTag.setParamScopeExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="975:1:1" line-data="        setPropertyExpr(null);">`setPropertyExpr`</SwmToken>(null) to clear any property expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setPropertyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="856">

---

SetPropertyExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="856:9:9" line-data="    public void setPropertyExpr(String propertyExpr) {">`propertyExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setPropertyExpr(String propertyExpr) {
        this.propertyExpr = propertyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="976">

---

After returning from ELLinkTag.setPropertyExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="976:1:1" line-data="        setScopeExpr(null);">`setScopeExpr`</SwmToken>(null) to clear any scope expression, so the tag doesn't accidentally reuse old parameter logic.

```java
        setScopeExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="864">

---

SetScopeExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="864:9:9" line-data="    public void setScopeExpr(String scopeExpr) {">`scopeExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setScopeExpr(String scopeExpr) {
        this.scopeExpr = scopeExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="977">

---

After returning from ELLinkTag.setScopeExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="977:1:1" line-data="        setStyleExpr(null);">`setStyleExpr`</SwmToken>(null) to clear any style expression, so the tag doesn't accidentally reuse old style logic.

```java
        setStyleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="872">

---

SetStyleExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="872:9:9" line-data="    public void setStyleExpr(String styleExpr) {">`styleExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setStyleExpr(String styleExpr) {
        this.styleExpr = styleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="978">

---

After returning from ELLinkTag.setStyleExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="978:1:1" line-data="        setStyleClassExpr(null);">`setStyleClassExpr`</SwmToken>(null) to clear any style class expression, so the tag doesn't accidentally reuse old style logic.

```java
        setStyleClassExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="880">

---

SetStyleClassExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="880:9:9" line-data="    public void setStyleClassExpr(String styleClassExpr) {">`styleClassExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setStyleClassExpr(String styleClassExpr) {
        this.styleClassExpr = styleClassExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="979">

---

After returning from ELLinkTag.setStyleClassExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="979:1:1" line-data="        setStyleIdExpr(null);">`setStyleIdExpr`</SwmToken>(null) to clear any style ID expression, so the tag doesn't accidentally reuse old style logic.

```java
        setStyleIdExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="888">

---

SetStyleIdExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="888:9:9" line-data="    public void setStyleIdExpr(String styleIdExpr) {">`styleIdExpr`</SwmToken>. No validation, no side effects—just a standard setter.

```java
    public void setStyleIdExpr(String styleIdExpr) {
        this.styleIdExpr = styleIdExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="980">

---

We just returned from ELLinkTag.setStyleIdExpr in ELLinkTag.release. Now we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="980:1:1" line-data="        setTabindexExpr(null);">`setTabindexExpr`</SwmToken>(null) to make sure any tabindex expression is cleared out, so the tag doesn't accidentally reuse old tabindex values on the next usage.

```java
        setTabindexExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="896">

---

SetTabindexExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="896:9:9" line-data="    public void setTabindexExpr(String tabindexExpr) {">`tabindexExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setTabindexExpr(String tabindexExpr) {
        this.tabindexExpr = tabindexExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="981">

---

After returning from ELLinkTag.setTabindexExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="981:1:1" line-data="        setTargetExpr(null);">`setTargetExpr`</SwmToken>(null) to clear any target expression, so the tag doesn't accidentally reuse old target values for link navigation.

```java
        setTargetExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="904">

---

SetTargetExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="904:9:9" line-data="    public void setTargetExpr(String targetExpr) {">`targetExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setTargetExpr(String targetExpr) {
        this.targetExpr = targetExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="982">

---

After returning from ELLinkTag.setTargetExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="982:1:1" line-data="        setTitleExpr(null);">`setTitleExpr`</SwmToken>(null) to clear any title expression, so the tag doesn't accidentally reuse old title values for tooltips or accessibility.

```java
        setTitleExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="912">

---

SetTitleExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="912:9:9" line-data="    public void setTitleExpr(String titleExpr) {">`titleExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setTitleExpr(String titleExpr) {
        this.titleExpr = titleExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="983">

---

After returning from ELLinkTag.setTitleExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="983:1:1" line-data="        setTitleKeyExpr(null);">`setTitleKeyExpr`</SwmToken>(null) to clear any title key expression, so the tag doesn't accidentally reuse old resource keys for localization.

```java
        setTitleKeyExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="920">

---

SetTitleKeyExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="920:9:9" line-data="    public void setTitleKeyExpr(String titleKeyExpr) {">`titleKeyExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setTitleKeyExpr(String titleKeyExpr) {
        this.titleKeyExpr = titleKeyExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="984">

---

After returning from ELLinkTag.setTitleKeyExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="984:1:1" line-data="        setTransactionExpr(null);">`setTransactionExpr`</SwmToken>(null) to clear any transaction expression, so the tag doesn't accidentally reuse old transaction values for form submission or security.

```java
        setTransactionExpr(null);
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="928">

---

SetTransactionExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="928:9:9" line-data="    public void setTransactionExpr(String transactionExpr) {">`transactionExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setTransactionExpr(String transactionExpr) {
        this.transactionExpr = transactionExpr;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="985">

---

After returning from ELLinkTag.setTransactionExpr in ELLinkTag.release, we call <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="985:1:1" line-data="        setUseLocalEncodingExpr(null);">`setUseLocalEncodingExpr`</SwmToken>(null) to clear any encoding expression, making sure the tag doesn't accidentally reuse old encoding preferences.

```java
        setUseLocalEncodingExpr(null);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" line="936">

---

SetUseLocalEncodingExpr just assigns the input string to <SwmToken path="el/src/main/java/org/apache/strutsel/taglib/html/ELLinkTag.java" pos="936:9:9" line-data="    public void setUseLocalEncodingExpr(String useLocalEncodingExpr) {">`useLocalEncodingExpr`</SwmToken>. No checks, no side effects—just a standard setter.

```java
    public void setUseLocalEncodingExpr(String useLocalEncodingExpr) {
        this.useLocalEncodingExpr = useLocalEncodingExpr;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
