---
title: Rendering the opening HTML tag with locale and markup compliance
---
This document describes how the system renders the opening <html> tag for each page, ensuring that the tag includes the correct language, country, and XHTML attributes. The flow adapts the tag to match the user's locale and markup standard, so that the page is compliant and accessible.

# Rendering the opening <html> tag with locale and XHTML compliance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Prepare to output <html> tag"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:120:121"
    node1 --> node2{"Is XHTML output required?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:148:178"
    node2 -->|"Yes"| node3{"XHTML Version?"}
    node2 -->|"No"| node4["Use standard HTML <html> tag"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:155:177"
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:133:147"
    node3 -->|1.1| node5["Add XHTML 1.1 attributes"]
    node3 -->|2.0| node6["Add XHTML 2.0 attributes"]
    node3 -->|5.0| node7["Add XHTML 5.0 attributes"]
    node3 -->|"1.0/Other"| node8["Add XHTML 1.0 attributes"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:155:159"
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:161:165"
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:167:169"
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:171:177"
    node5 --> node9{"Add language/country attributes?"}
    node6 --> node9
    node7 --> node9
    node8 --> node9
    node4 --> node9
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:182:206"
    node9 -->|"lang enabled, valid language/country, (not XHTML or XHTML version < 1.1)"| node10["Add lang and country to <html> tag"]
    node9 -->|"XHTML and valid language"| node11["Add xml:lang and country to <html> tag"]
    node9 -->|"No"| node12["Skip language/country attributes"]
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:182:194"
    click node11 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:196:206"
    click node12 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:208:209"
    node10 --> node13["Output <html> tag to page"]
    node11 --> node13
    node12 --> node13
    click node13 openCode "taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java:121:122"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Prepare to output <html> tag"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:120:121"
%%     node1 --> node2{"Is XHTML output required?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:148:178"
%%     node2 -->|"Yes"| node3{"XHTML Version?"}
%%     node2 -->|"No"| node4["Use standard HTML <html> tag"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:155:177"
%%     click node4 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:133:147"
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="154:3:5" line-data="        	// 1.1 ">`1.1`</SwmToken>| node5["Add XHTML <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="154:3:5" line-data="        	// 1.1 ">`1.1`</SwmToken> attributes"]
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="160:3:5" line-data="            // 2.0">`2.0`</SwmToken>| node6["Add XHTML <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="160:3:5" line-data="            // 2.0">`2.0`</SwmToken> attributes"]
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="166:3:5" line-data="            // 5.0">`5.0`</SwmToken>| node7["Add XHTML <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="166:3:5" line-data="            // 5.0">`5.0`</SwmToken> attributes"]
%%     node3 -->|"1.0/Other"| node8["Add XHTML <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="170:3:5" line-data="        	// 1.0/Default">`1.0`</SwmToken> attributes"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:155:159"
%%     click node6 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:161:165"
%%     click node7 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:167:169"
%%     click node8 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:171:177"
%%     node5 --> node9{"Add language/country attributes?"}
%%     node6 --> node9
%%     node7 --> node9
%%     node8 --> node9
%%     node4 --> node9
%%     click node9 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:182:206"
%%     node9 -->|"lang enabled, valid language/country, (not XHTML or XHTML version < <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="154:3:5" line-data="        	// 1.1 ">`1.1`</SwmToken>)"| node10["Add lang and country to <html> tag"]
%%     node9 -->|"XHTML and valid language"| node11["Add xml:lang and country to <html> tag"]
%%     node9 -->|"No"| node12["Skip language/country attributes"]
%%     click node10 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:182:194"
%%     click node11 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:196:206"
%%     click node12 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:208:209"
%%     node10 --> node13["Output <html> tag to page"]
%%     node11 --> node13
%%     node12 --> node13
%%     click node13 openCode "<SwmPath>[taglib/…/html/HtmlTag.java](taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java)</SwmPath>:121:122"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" line="120">

---

DoStartTag kicks off the tag rendering by writing the opening <html> tag to the page output. It calls <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="122:3:3" line-data="            this.renderHtmlStartElement());">`renderHtmlStartElement`</SwmToken> to handle all the logic for building the tag string, then signals to include the body content. This separation keeps the tag generation logic clean and maintainable.

```java
    public int doStartTag() throws JspException {
        TagUtils.getInstance().write(this.pageContext,
            this.renderHtmlStartElement());

        return EVAL_BODY_INCLUDE;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" line="132">

---

RenderHtmlStartElement builds the <html> tag string, adding locale-based language and country attributes, and setting XHTML-specific xmlns/schemaLocation attributes depending on the version. It pulls locale info from the page context, validates it, and applies lang/xml:lang as needed. It also sets <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="139:9:9" line-data="            TagUtils.getInstance().getUserLocale(pageContext, Globals.LOCALE_KEY);">`pageContext`</SwmToken> flags for XHTML compliance and handles unknown XHTML versions by defaulting to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/HtmlTag.java" pos="170:3:5" line-data="        	// 1.0/Default">`1.0`</SwmToken>.

```java
    protected String renderHtmlStartElement() {
        StringBuffer sb = new StringBuffer("<html");

        String language = null;
        String country = "";

        Locale currentLocale =
            TagUtils.getInstance().getUserLocale(pageContext, Globals.LOCALE_KEY);

        language = currentLocale.getLanguage();
        country = currentLocale.getCountry();

        boolean validLanguage = isValidRfc2616(language);
        boolean validCountry  = isValidRfc2616(country);

        // XHTML document conformance
        if (this.xhtml) {
            this.pageContext.setAttribute(Globals.XHTML_KEY, "true",
                PageContext.PAGE_SCOPE);
            this.pageContext.setAttribute(Globals.XHTML_VERSION_KEY, 
            		xhtmlVersion, PageContext.REQUEST_SCOPE);

        	// 1.1 
            if (xhtmlVersion.equals(TagUtils.XHTML_1_1)) {
                sb.append(" xmlns=\"http://www.w3.org/1999/xhtml\"");
        		sb.append(" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"");
        		sb.append(" xsi:schemaLocation=\"http://www.w3.org/MarkUp/SCHEMA/xhtml11.xsd\"");
        	}
            // 2.0
        	else if (xhtmlVersion.equals(TagUtils.XHTML_2_0)) {
        		sb.append(" xmlns=\"http://www.w3.org/2002/06/xhtml2/\"");
        		sb.append(" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"");
        		sb.append(" xsi:schemaLocation=\"http://www.w3.org/2002/06/xhtml2/ http://www.w3.org/MarkUp/SCHEMA/xhtml2.xsd\"");
        	}
            // 5.0
        	else if (xhtmlVersion.equals(TagUtils.XHTML_5_0)) {
                sb.append(" xmlns=\"http://www.w3.org/1999/xhtml\"");
        	}
        	// 1.0/Default
        	else {
            	if (!xhtmlVersion.equals(TagUtils.XHTML_1_0)) {
            		log.warn("Defaulting to XHTML 1.0. Unknown version: " + xhtmlVersion);
            		xhtmlVersion = TagUtils.XHTML_1_0;
            	}
                sb.append(" xmlns=\"http://www.w3.org/1999/xhtml\"");
        	}
        }

        // If language is specified, output the attribute
        // unless XHTML is version >= 1.1
        if (this.lang && validLanguage) {
        	if (!this.xhtml || (xhtmlVersion.compareTo(TagUtils.XHTML_1_1) < 0)) {
            	sb.append(" lang=\"");
                sb.append(language);

                if (validCountry) {
                    sb.append("-");
                    sb.append(country);
                }

                sb.append("\"");
        	}
        }

        if (this.xhtml && validLanguage) {
            sb.append(" xml:lang=\"");
            sb.append(language);

            if (validCountry) {
                sb.append("-");
                sb.append(country);
            }

            sb.append("\"");
        }

        sb.append(">");

        return sb.toString();
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
