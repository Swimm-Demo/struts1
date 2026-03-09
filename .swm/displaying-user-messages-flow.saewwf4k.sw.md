---
title: Displaying User Messages Flow
---
This document describes how user-facing messages, such as errors or notifications, are processed and displayed in the user interface. The flow covers selecting, normalizing, and formatting messages, applying localization, and making them available for display. It supports both global and property-specific messages, message counts, and customizable headers.

# Starting Message Tag Processing

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Choose which messages to display
(general or specific)"] --> node2["Normalizing Message Collection"]
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:214:217"
    
    node2 --> node3{"Are there messages to display?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:242:244"
    node3 -->|"No"| node5["No messages shown"]
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:243:244"
    node3 -->|"Yes"| node4["Finalizing Tag Output"]
    

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Normalizing Message Collection"
node2:::HeadingStyle
click node4 goToHeading "Finalizing Tag Output"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Choose which messages to display
%% (general or specific)"] --> node2["Normalizing Message Collection"]
%%     click node1 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:214:217"
%%     
%%     node2 --> node3{"Are there messages to display?"}
%%     click node3 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:242:244"
%%     node3 -->|"No"| node5["No messages shown"]
%%     click node5 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:243:244"
%%     node3 -->|"Yes"| node4["Finalizing Tag Output"]
%%     
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Normalizing Message Collection"
%% node2:::HeadingStyle
%% click node4 goToHeading "Finalizing Tag Output"
%% node4:::HeadingStyle
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" line="204">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="204:5:5" line-data="    public int doStartTag() throws JspException {">`doStartTag`</SwmToken>, we're figuring out which set of messages to work with by checking the 'name' and 'message' attributes. If 'message' is set, we force the use of the global message key. Then we call `TagUtils.getActionMessages` to actually fetch the messages from the page context, since messages could be stored in different formats or scopes. If something goes wrong, we save the exception and rethrow it. This sets up the rest of the tag processing.

```java
    public int doStartTag() throws JspException {
        // Initialize for a new request.
        processed = false;

        // Were any messages specified?
        ActionMessages messages = null;

        // Make a local copy of the name attribute that we can modify.
        String name = this.name;

        if ((message != null) && "true".equalsIgnoreCase(message)) {
            name = Globals.MESSAGE_KEY;
        }

        try {
            messages =
                TagUtils.getInstance().getActionMessages(pageContext, name);
        } catch (JspException e) {
            TagUtils.getInstance().saveException(pageContext, e);
            throw e;
        }

```

---

</SwmSnippet>

## Normalizing Message Collection

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Find messages by name in page context"] --> node2{"Is a value found?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:731:731"
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:733:733"
    node2 -->|"No"| node7["Return empty messages"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:729:729"
    node2 -->|"Yes"| node3{"Type of value?"}
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:735:752"
    node3 -->|"String"| node4["Add single message to result"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:736:737"
    node3 -->|"String array"| loop1
    node3 -->|ActionErrors| node10["Convert to ActionMessages and add"]
    click node10 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:746:748"
    node3 -->|ActionMessages| node6["Return found messages"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:750:750"
    node3 -->|"Other"| node8["Throw error"]
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:752:753"
    
    subgraph loop1["For each string in array"]
      node5["Add message to result"]
      click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:742:743"
    end
    node4 --> node9["Return messages"]
    loop1 --> node9
    node10 --> node9
    node6 --> node9
    node7 --> node9
    click node9 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:763:763"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Find messages by name in page context"] --> node2{"Is a value found?"}
%%     click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:731:731"
%%     click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:733:733"
%%     node2 -->|"No"| node7["Return empty messages"]
%%     click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:729:729"
%%     node2 -->|"Yes"| node3{"Type of value?"}
%%     click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:735:752"
%%     node3 -->|"String"| node4["Add single message to result"]
%%     click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:736:737"
%%     node3 -->|"String array"| loop1
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="745:12:12" line-data="                } else if (value instanceof ActionErrors) {">`ActionErrors`</SwmToken>| node10["Convert to <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="209:1:1" line-data="        ActionMessages messages = null;">`ActionMessages`</SwmToken> and add"]
%%     click node10 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:746:748"
%%     node3 -->|<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="209:1:1" line-data="        ActionMessages messages = null;">`ActionMessages`</SwmToken>| node6["Return found messages"]
%%     click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:750:750"
%%     node3 -->|"Other"| node8["Throw error"]
%%     click node8 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:752:753"
%%     
%%     subgraph loop1["For each string in array"]
%%       node5["Add message to result"]
%%       click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:742:743"
%%     end
%%     node4 --> node9["Return messages"]
%%     loop1 --> node9
%%     node10 --> node9
%%     node6 --> node9
%%     node7 --> node9
%%     click node9 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:763:763"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="727">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="727:5:5" line-data="    public ActionMessages getActionMessages(PageContext pageContext,">`getActionMessages`</SwmToken>, we're grabbing whatever is in the page context under the given name and converting it into an <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="727:3:3" line-data="    public ActionMessages getActionMessages(PageContext pageContext,">`ActionMessages`</SwmToken> object. We handle strings, arrays, <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="745:12:12" line-data="                } else if (value instanceof ActionErrors) {">`ActionErrors`</SwmToken>, and <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="727:3:3" line-data="    public ActionMessages getActionMessages(PageContext pageContext,">`ActionMessages`</SwmToken>, so no matter how the messages were put there, we can work with them in a consistent way for the rest of the tag processing.

```java
    public ActionMessages getActionMessages(PageContext pageContext,
        String paramName) throws JspException {
        ActionMessages am = new ActionMessages();

        Object value = pageContext.findAttribute(paramName);

        if (value != null) {
            try {
                if (value instanceof String) {
                    am.add(ActionMessages.GLOBAL_MESSAGE,
                        new ActionMessage((String) value));
                } else if (value instanceof String[]) {
                    String[] keys = (String[]) value;

                    for (int i = 0; i < keys.length; i++) {
                        am.add(ActionMessages.GLOBAL_MESSAGE,
                            new ActionMessage(keys[i]));
                    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="745">

---

After type-checking and converting, we return an <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="746:1:1" line-data="                    ActionMessages m = (ActionMessages) value;">`ActionMessages`</SwmToken> object that wraps whatever was in the page context. If nothing was found, it's just empty. This keeps the rest of the flow simple since we always have a consistent object to work with.

```java
                } else if (value instanceof ActionErrors) {
                    ActionMessages m = (ActionMessages) value;

                    am.add(m);
                } else if (value instanceof ActionMessages) {
                    am = (ActionMessages) value;
                } else {
                    throw new JspException(messages.getMessage(
                            "actionMessages.errors", value.getClass().getName()));
                }
            } catch (JspException e) {
                throw e;
            } catch (Exception e) {
                log.warn("Unable to retieve ActionMessage for paramName : "
                    + paramName, e);
            }
        }

        return am;
    }
```

---

</SwmSnippet>

## Selecting and Counting Messages

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is property specified?"}
    node1 -->|"Yes"| node2["Retrieve messages for property"]
    node1 -->|"No"| node3["Retrieve all messages"]
    node2 --> node4{"Expose message count?"}
    node3 --> node4
    node4 -->|"Yes"| node5["Expose message count"]
    node4 -->|"No"| node6{"Are there messages to display?"}
    node5 --> node6
    node6 -->|"No"| node7["Skip displaying messages"]
    node6 -->|"Yes"| node8["Begin displaying messages"]

    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:228:234"
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:232:233"
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:229:230"
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:237:239"
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:238:239"
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:242:244"
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:243:244"
    click node8 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:247:247"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is property specified?"}
%%     node1 -->|"Yes"| node2["Retrieve messages for property"]
%%     node1 -->|"No"| node3["Retrieve all messages"]
%%     node2 --> node4{"Expose message count?"}
%%     node3 --> node4
%%     node4 -->|"Yes"| node5["Expose message count"]
%%     node4 -->|"No"| node6{"Are there messages to display?"}
%%     node5 --> node6
%%     node6 -->|"No"| node7["Skip displaying messages"]
%%     node6 -->|"Yes"| node8["Begin displaying messages"]
%% 
%%     click node1 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:228:234"
%%     click node2 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:232:233"
%%     click node3 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:229:230"
%%     click node4 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:237:239"
%%     click node5 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:238:239"
%%     click node6 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:242:244"
%%     click node7 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:243:244"
%%     click node8 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:247:247"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" line="226">

---

Back in `MessagesTag.doStartTag`, after getting the messages, we pick either all messages or just those for a specific property. We also set the count in the page context if needed. If there are no messages, we skip the tag body. Otherwise, we call <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="247:1:1" line-data="        processMessage((ActionMessage) iterator.next());">`processMessage`</SwmToken> to handle the first message, which sets up the tag for rendering.

```java
        // Acquire the collection we are going to iterate over
        int size;
        if (property == null) {
        	this.iterator = messages.get();
        	size = messages.size();
        } else {
        	this.iterator = messages.get(property);
        	size = messages.size(property);
        }
        
        // Expose the count when specified
        if (count != null) {
        	pageContext.setAttribute(count, new Integer(size));
        }
        
        // Store the first value and evaluate, or skip the body if none
        if (!this.iterator.hasNext()) {
            return SKIP_BODY;
        }

        // process the first message
        processMessage((ActionMessage) iterator.next());

```

---

</SwmSnippet>

## Formatting a Single Message

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" line="293">

---

In <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="293:5:5" line-data="    private void processMessage(ActionMessage report)">`processMessage`</SwmToken>, we check if the <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="293:7:7" line-data="    private void processMessage(ActionMessage report)">`ActionMessage`</SwmToken> needs to be looked up from a resource bundle. If so, we optionally filter the arguments and then call `TagUtils.message` to get the localized string. This is where message formatting and localization happen.

```java
    private void processMessage(ActionMessage report)
        throws JspException {
        String msg = null;

        if (report.isResource()) {
            Object[] values = report.getValues();
            if (filterArgs) {
                values = filterMessageReplacementValues(values);
            }
            
            msg = TagUtils.getInstance().message(pageContext, bundle, locale,
                    report.getKey(), values);

```

---

</SwmSnippet>

### Looking Up Localized Message Text

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Retrieve message resources (bundle) and
user locale (locale)"] --> node2{"Are arguments provided?"}
  click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:998:1002"
  node2 -->|"No"| node3["Get message for key and locale"]
  click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1004:1005"
  node2 -->|"Yes"| node4["Get message for key, locale, and
arguments"]
  click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1005:1006"
  click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1007:1008"
  node3 --> node5{"Is message found?"}
  node4 --> node5
  click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1010:1010"
  node5 -->|"No"| node6["Log missing message key for debugging"]
  node5 -->|"Yes"| node7["Return localized message"]
  click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1011:1014"
  node6 --> node7
  click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/TagUtils.java:1016:1017"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Retrieve message resources (bundle) and
%% user locale (locale)"] --> node2{"Are arguments provided?"}
%%   click node1 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:998:1002"
%%   node2 -->|"No"| node3["Get message for key and locale"]
%%   click node2 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1004:1005"
%%   node2 -->|"Yes"| node4["Get message for key, locale, and
%% arguments"]
%%   click node3 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1005:1006"
%%   click node4 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1007:1008"
%%   node3 --> node5{"Is message found?"}
%%   node4 --> node5
%%   click node5 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1010:1010"
%%   node5 -->|"No"| node6["Log missing message key for debugging"]
%%   node5 -->|"Yes"| node7["Return localized message"]
%%   click node6 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1011:1014"
%%   node6 --> node7
%%   click node7 openCode "<SwmPath>[taglib/…/taglib/TagUtils.java](taglib/src/main/java/org/apache/struts/taglib/TagUtils.java)</SwmPath>:1016:1017"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" line="995">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="995:5:5" line-data="    public String message(PageContext pageContext, String bundle,">`message`</SwmToken> grabs the right <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/TagUtils.java" pos="998:1:1" line-data="        MessageResources resources =">`MessageResources`</SwmToken> for the bundle and locale, then fetches the message string by key, with or without arguments. If the message isn't found, it logs a debug message. If arguments are present, they're passed along for formatting. For some cases, argument resolution is delegated to `Resources.getMessage` for more advanced formatting.

```java
    public String message(PageContext pageContext, String bundle,
        String locale, String key, Object[] args)
        throws JspException {
        MessageResources resources =
            retrieveMessageResources(pageContext, bundle, false);

        Locale userLocale = getUserLocale(pageContext, locale);
        String message = null;

        if (args == null) {
            message = resources.getMessage(userLocale, key);
        } else {
            message = resources.getMessage(userLocale, key, args);
        }

        if ((message == null) && log.isDebugEnabled()) {
            // log missing key to ease debugging
            log.debug(resources.getMessage("message.resources", key, bundle,
                    locale));
        }

        return message;
    }
```

---

</SwmSnippet>

### Resolving Message Arguments

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Gather context-specific details for the
message"]
  click node1 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:267:267"
  node1 --> node2{"Is there a custom message for this field
and requirement?"}
  click node2 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:269:270"
  node2 -->|"Yes"| node3["Use custom field message template"]
  click node3 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:269:269"
  node2 -->|"No"| node4["Use default requirement message template"]
  click node4 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:270:270"
  node3 --> node5["Return localized and personalized
message"]
  click node5 openCode "core/src/main/java/org/apache/struts/validator/Resources.java:272:272"
  node4 --> node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Gather context-specific details for the
%% message"]
%%   click node1 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:267:267"
%%   node1 --> node2{"Is there a custom message for this field
%% and requirement?"}
%%   click node2 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:269:270"
%%   node2 -->|"Yes"| node3["Use custom field message template"]
%%   click node3 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:269:269"
%%   node2 -->|"No"| node4["Use default requirement message template"]
%%   click node4 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:270:270"
%%   node3 --> node5["Return localized and personalized
%% message"]
%%   click node5 openCode "<SwmPath>[core/…/validator/Resources.java](core/src/main/java/org/apache/struts/validator/Resources.java)</SwmPath>:272:272"
%%   node4 --> node5
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="265">

---

In <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="267:9:9" line-data="        String[] args = getArgs(va.getName(), messages, locale, field);">`getArgs`</SwmToken>, we pull up to 4 arguments from the Field for the given action. For each argument, if it's a resource, we resolve it to a localized string; otherwise, we just use the key. This prepares the argument list for message formatting.

```java
    public static String getMessage(MessageResources messages, Locale locale,
        ValidatorAction va, Field field) {
        String[] args = getArgs(va.getName(), messages, locale, field);
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="420">

---

<SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="420:9:9" line-data="    public static String[] getArgs(String actionName,">`getArgs`</SwmToken> always tries to fetch 4 arguments for the action from the Field. For each, if it's a resource, we resolve it to a localized string; otherwise, we just use the key. This is baked into how Struts validation messages are structured.

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
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/validator/Resources.java" line="268">

---

After getting the arguments from <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="267:9:9" line-data="        String[] args = getArgs(va.getName(), messages, locale, field);">`getArgs`</SwmToken>, we pick the message key from the Field if it exists, otherwise from the <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="266:1:1" line-data="        ValidatorAction va, Field field) {">`ValidatorAction`</SwmToken>. Then we call <SwmToken path="core/src/main/java/org/apache/struts/validator/Resources.java" pos="272:3:5" line-data="        return messages.getMessage(locale, msg, args);">`messages.getMessage`</SwmToken> with the locale, key, and arguments to get the final formatted string.

```java
        String msg =
            (field.getMsg(va.getName()) != null) ? field.getMsg(va.getName())
                                                 : va.getMsg();

        return messages.getMessage(locale, msg, args);
    }
```

---

</SwmSnippet>

### Storing the Final Message

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is there a message to display?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:306:306"
    node1 -->|"No"| node2{"Is a bundle specified?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:307:307"
    node2 -->|"No"| node3["Look up message using default bundle"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:307:310"
    node2 -->|"Yes"| node4["Look up message using specified bundle"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:307:310"
    node3 --> node5{"Is a message now available?"}
    node4 --> node5
    node1 -->|"Yes"| node5
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:316:316"
    node5 -->|"No"| node6["Remove message from page context"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:317:318"
    node5 -->|"Yes"| node7["Set message in page context"]
    click node7 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:319:320"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is there a message to display?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:306:306"
%%     node1 -->|"No"| node2{"Is a bundle specified?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:307:307"
%%     node2 -->|"No"| node3["Look up message using default bundle"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:307:310"
%%     node2 -->|"Yes"| node4["Look up message using specified bundle"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:307:310"
%%     node3 --> node5{"Is a message now available?"}
%%     node4 --> node5
%%     node1 -->|"Yes"| node5
%%     click node5 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:316:316"
%%     node5 -->|"No"| node6["Remove message from page context"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:317:318"
%%     node5 -->|"Yes"| node7["Set message in page context"]
%%     click node7 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:319:320"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" line="306">

---

After getting the message string from `TagUtils.message`, if it's still null, we remove the attribute from the page context. Otherwise, we set the resolved message under the given id, so the JSP can use it.

```java
            if (msg == null) {
                String bundleName = (bundle == null) ? "default" : bundle;

                msg = messageResources.getMessage("messagesTag.notfound",
                        report.getKey(), bundleName);
            }
        } else {
            msg = report.getKey();
        }

        if (msg == null) {
            pageContext.removeAttribute(id);
        } else {
            pageContext.setAttribute(id, msg);
        }
    }
```

---

</SwmSnippet>

## Finalizing Tag Output

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is a header provided and non-empty?"}
    click node1 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:249:257"
    node1 -->|"Yes"| node2{"Is the header message available?"}
    click node2 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:250:254"
    node2 -->|"Yes"| node3["Display the header message"]
    click node3 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:255:256"
    node2 -->|"No"| node4["No header displayed"]
    click node4 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:257:257"
    node1 -->|"No"| node4
    node3 --> node5["Mark tag as processed (processed = true)"]
    node4 --> node5
    click node5 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:259:261"
    node5 --> node6["Continue processing tag body"]
    click node6 openCode "taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java:263:263"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is a header provided and non-empty?"}
%%     click node1 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:249:257"
%%     node1 -->|"Yes"| node2{"Is the header message available?"}
%%     click node2 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:250:254"
%%     node2 -->|"Yes"| node3["Display the header message"]
%%     click node3 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:255:256"
%%     node2 -->|"No"| node4["No header displayed"]
%%     click node4 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:257:257"
%%     node1 -->|"No"| node4
%%     node3 --> node5["Mark tag as processed (processed = true)"]
%%     node4 --> node5
%%     click node5 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:259:261"
%%     node5 --> node6["Continue processing tag body"]
%%     click node6 openCode "<SwmPath>[taglib/…/html/MessagesTag.java](taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java)</SwmPath>:263:263"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" line="249">

---

After `MessagesTag.processMessage`, if a header is set, we look it up and write it out. We mark processing as done and return <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/html/MessagesTag.java" pos="263:4:4" line-data="        return (EVAL_BODY_TAG);">`EVAL_BODY_TAG`</SwmToken> so the JSP engine knows to evaluate the tag body. This wraps up the tag's setup and output.

```java
        if ((header != null) && (header.length() > 0)) {
            String headerMessage =
                TagUtils.getInstance().message(pageContext, bundle, locale,
                    header);

            if (headerMessage != null) {
                TagUtils.getInstance().write(pageContext, headerMessage);
            }
        }

        // Set the processed variable to true so the
        // doEndTag() knows processing took place
        processed = true;

        return (EVAL_BODY_TAG);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
