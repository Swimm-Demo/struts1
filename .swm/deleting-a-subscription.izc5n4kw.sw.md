---
title: Deleting a subscription
---
This document outlines how users can delete subscriptions through the application's subscription management feature. When a user requests to delete a subscription, the system gathers the relevant user and subscription information, constructs the appropriate action URL, and forwards the request to trigger the deletion process.

# Preparing the Delete Request

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" line="101">

---

In <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="101:5:5" line-data="    public String delete() {">`delete`</SwmToken>, we start by logging and grabbing the <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="106:1:1" line-data="        FacesContext context = FacesContext.getCurrentInstance();">`FacesContext`</SwmToken>. The function then calls <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="107:7:10" line-data="        StringBuffer url = subscription(context);">`subscription(context)`</SwmToken> from <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="36:8:8" line-data="public class RegistrationBacking extends AbstractBacking {">`AbstractBacking`</SwmToken> to get the base URL for the delete action. This sets up the URL for the delete operation, and we need <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="36:8:8" line-data="public class RegistrationBacking extends AbstractBacking {">`AbstractBacking`</SwmToken> because that's where the URL construction logic lives.

```java
    public String delete() {

        if (log.isDebugEnabled()) {
            log.debug("delete()");
        }
        FacesContext context = FacesContext.getCurrentInstance();
        StringBuffer url = subscription(context);
```

---

</SwmSnippet>

## Building the Action URL

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" line="124">

---

<SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="124:5:5" line-data="    protected StringBuffer subscription(FacesContext context) {">`subscription`</SwmToken> just calls <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="126:4:4" line-data="        return (action(context, &quot;/editSubscription&quot;));">`action`</SwmToken> with <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="126:10:11" line-data="        return (action(context, &quot;/editSubscription&quot;));">`/editSubscription`</SwmToken> and returns the result. This is how we get the base URL for subscription-related actions, and that's why RegistrationBacking.delete needs it next.

```java
    protected StringBuffer subscription(FacesContext context) {

        return (action(context, "/editSubscription"));

    }
```

---

</SwmSnippet>

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" line="47">

---

<SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="47:5:5" line-data="    protected StringBuffer action(FacesContext context, String action) {">`action`</SwmToken> takes the action path, appends '.do', and returns it. This is how Struts expects action URLs, so every action gets the extension added here.

```java
    protected StringBuffer action(FacesContext context, String action) {

        // FIXME - assumes extension mapping for Struts
        StringBuffer sb = new StringBuffer(action);
        sb.append(".do");
        return (sb);

    }
```

---

</SwmSnippet>

## Completing and Forwarding the Delete Action

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start deletion process"]
    click node1 openCode "apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java:108:108"
    node1 --> node2["Identify current user from session"]
    click node2 openCode "apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java:110:112"
    node2 --> node3["Identify subscription to delete from
request"]
    click node3 openCode "apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java:114:116"
    node3 --> node4["Build deletion URL (action=Delete,
username, host)"]
    click node4 openCode "apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java:108:116"
    node4 --> node5["Forward request to initiate deletion"]
    click node5 openCode "apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java:117:117"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start deletion process"]
%%     click node1 openCode "<SwmPath>[apps/…/example/RegistrationBacking.java](apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java)</SwmPath>:108:108"
%%     node1 --> node2["Identify current user from session"]
%%     click node2 openCode "<SwmPath>[apps/…/example/RegistrationBacking.java](apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java)</SwmPath>:110:112"
%%     node2 --> node3["Identify subscription to delete from
%% request"]
%%     click node3 openCode "<SwmPath>[apps/…/example/RegistrationBacking.java](apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java)</SwmPath>:114:116"
%%     node3 --> node4["Build deletion URL (action=Delete,
%% username, host)"]
%%     click node4 openCode "<SwmPath>[apps/…/example/RegistrationBacking.java](apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java)</SwmPath>:108:116"
%%     node4 --> node5["Forward request to initiate deletion"]
%%     click node5 openCode "<SwmPath>[apps/…/example/RegistrationBacking.java](apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java)</SwmPath>:117:117"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" line="108">

---

Back in RegistrationBacking.delete, after getting the base URL from <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/RegistrationBacking.java" pos="36:8:8" line-data="public class RegistrationBacking extends AbstractBacking {">`AbstractBacking`</SwmToken>, we append the delete action and user/subscription info as query parameters. Then we call forward to actually trigger the delete. Returning null here means JSF doesn't try to navigate anywhere else.

```java
        url.append("?action=Delete");
        url.append("&username=");
        User user = (User)
            context.getExternalContext().getSessionMap().get("user");
        url.append(user.getUsername());
        url.append("&host=");
        Subscription subscription = (Subscription)
            context.getExternalContext().getRequestMap().get("subscription");
        url.append(subscription.getHost());
        forward(context, url.toString());
        return (null);

    }
```

---

</SwmSnippet>

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" line="66">

---

<SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="66:5:5" line-data="    protected void forward(FacesContext context, String url) {">`forward`</SwmToken> uses dispatch to internally send the request to the constructed URL. If there's an error, it wraps it in a <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/AbstractBacking.java" pos="71:5:5" line-data="            throw new FacesException(e);">`FacesException`</SwmToken>. Finally, it marks the response as complete so JSF doesn't do anything else. This hands off control to Struts for the actual delete logic.

```java
    protected void forward(FacesContext context, String url) {

        try {
            context.getExternalContext().dispatch(url);
        } catch (IOException e) {
            throw new FacesException(e);
        } finally {
            context.responseComplete();
        }

    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
