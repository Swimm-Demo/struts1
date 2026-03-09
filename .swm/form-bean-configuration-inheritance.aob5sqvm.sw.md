---
title: Form Bean Configuration Inheritance
---
This document describes how form bean configurations inherit properties and settings from ancestor configurations. The process receives a form bean configuration and outputs an updated configuration with inherited and merged settings, supporting modular and reusable form definitions.

```mermaid
flowchart TD
  node1["Starting Form Bean Extension Processing"]:::HeadingStyle
  click node1 goToHeading "Starting Form Bean Extension Processing"
  node1 --> node2{"Does configuration extend another?
(Processing Form Bean Extension Hierarchy)"}:::HeadingStyle
  click node2 goToHeading "Processing Form Bean Extension Hierarchy"
  node2 -->|"No"| node5["Marking Extension as Complete"]:::HeadingStyle
  click node5 goToHeading "Marking Extension as Complete"
  node2 -->|"Yes"| node3["Validating Form Bean Extension and Circular Inheritance"]:::HeadingStyle
  click node3 goToHeading "Validating Form Bean Extension and Circular Inheritance"
  node3 --> node4{"Is circular inheritance detected?"}
  node4 -->|"Yes"| node5
  node4 -->|"No"| node6["Completing Form Bean Extension and Inheritance"]:::HeadingStyle
  click node6 goToHeading "Completing Form Bean Extension and Inheritance"
  node6 --> node5
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Starting Form Bean Extension Processing

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="968">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:5:5" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`processFormBeanExtension`</SwmToken>, we check if the <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:9:9" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`beanConfig`</SwmToken> extension has already been processed. If not, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="979:1:1" line-data="                    processFormBeanConfigClass(beanConfig, moduleConfig);">`processFormBeanConfigClass`</SwmToken> to handle inheritance and possibly swap <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:9:9" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`beanConfig`</SwmToken> for a subclass instance, prepping it for further extension logic.

```java
    protected void processFormBeanExtension(FormBeanConfig beanConfig,
        ModuleConfig moduleConfig)
        throws ServletException {
        try {
            if (!beanConfig.isExtensionProcessed()) {
                if (log.isDebugEnabled()) {
                    log.debug("Processing extensions for '"
                        + beanConfig.getName() + "'");
                }

                beanConfig =
                    processFormBeanConfigClass(beanConfig, moduleConfig);

```

---

</SwmSnippet>

## Resolving Form Bean Class and Inheritance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Does bean config extend another bean?"}
  click node1 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1006:1011"
  node1 -->|"No"| node4["Use bean config as is"]
  click node4 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1010:1011"
  node1 -->|"Yes"| node2{"Does bean class match ancestor's class?"}
  click node2 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1022:1025"
  node2 -->|"Yes"| node4
  node2 -->|"No"| node3["Create new bean config of correct class"]
  click node3 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1026:1041"
  
  subgraph loop1["For each property in bean config"]
    node3 --> node5["Setting Configuration Properties and Nested Values"]
    
  end
  node5 --> node4

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node5 goToHeading "Copying Configuration Properties"
node5:::HeadingStyle
click node5 goToHeading "Setting Configuration Properties and Nested Values"
node5:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Does bean config extend another bean?"}
%%   click node1 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1006:1011"
%%   node1 -->|"No"| node4["Use bean config as is"]
%%   click node4 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1010:1011"
%%   node1 -->|"Yes"| node2{"Does bean class match ancestor's class?"}
%%   click node2 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1022:1025"
%%   node2 -->|"Yes"| node4
%%   node2 -->|"No"| node3["Create new bean config of correct class"]
%%   click node3 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1026:1041"
%%   
%%   subgraph loop1["For each property in bean config"]
%%     node3 --> node5["Setting Configuration Properties and Nested Values"]
%%     
%%   end
%%   node5 --> node4
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node5 goToHeading "Copying Configuration Properties"
%% node5:::HeadingStyle
%% click node5 goToHeading "Setting Configuration Properties and Nested Values"
%% node5:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="1003">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1003:5:5" line-data="    protected FormBeanConfig processFormBeanConfigClass(">`processFormBeanConfigClass`</SwmToken>, we check if <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1004:3:3" line-data="        FormBeanConfig beanConfig, ModuleConfig moduleConfig)">`beanConfig`</SwmToken> extends another bean. If so, and the class is just <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1003:3:3" line-data="    protected FormBeanConfig processFormBeanConfigClass(">`FormBeanConfig`</SwmToken>, we swap it for an instance of the ancestor's class, copying all properties and configs. Next, we call BaseConfig.copyProperties to handle property copying.

```java
    protected FormBeanConfig processFormBeanConfigClass(
        FormBeanConfig beanConfig, ModuleConfig moduleConfig)
        throws ServletException {
        String ancestor = beanConfig.getExtends();

        if (ancestor == null) {
            // Nothing to do, then
            return beanConfig;
        }

        // Make sure that this bean is of the right class
        FormBeanConfig baseConfig = moduleConfig.findFormBeanConfig(ancestor);

        if (baseConfig == null) {
            throw new UnavailableException("Unable to find " + "form bean '"
                + ancestor + "' to extend.");
        }

        // Was our bean's class overridden already?
        if (beanConfig.getClass().equals(FormBeanConfig.class)) {
            // Ensure that our bean is using the correct class
            if (!baseConfig.getClass().equals(beanConfig.getClass())) {
                // Replace the bean with an instance of the correct class
                FormBeanConfig newBeanConfig = null;
                String baseConfigClassName = baseConfig.getClass().getName();

                try {
                    newBeanConfig =
                        (FormBeanConfig) RequestUtils.applicationInstance(baseConfigClassName);

                    // copy the values
                    BeanUtils.copyProperties(newBeanConfig, beanConfig);

```

---

</SwmSnippet>

### Copying Configuration Properties

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="155">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/BaseConfig.java" pos="155:5:5" line-data="    protected Properties copyProperties() {">`copyProperties`</SwmToken> duplicates all key-value pairs from the original config's properties to a new Properties object. This is needed to ensure the new <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:9:9" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`beanConfig`</SwmToken> instance inherits all settings from the original.

```java
    protected Properties copyProperties() {
        Properties copy = new Properties();

        Enumeration keys = properties.propertyNames();

        while (keys.hasMoreElements()) {
            String key = (String) keys.nextElement();

            copy.setProperty(key, properties.getProperty(key));
        }

        return copy;
    }
```

---

</SwmSnippet>

### Setting Configuration Properties and Nested Values

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/BaseConfig.java" line="88">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/BaseConfig.java" pos="88:5:5" line-data="    public void setProperty(String key, String value) {">`setProperty`</SwmToken> checks if the config is still modifiable, then sets the property in the Properties map. Next, we call NestedPropertyHelper.setProperty to handle nested property paths for request-scoped configs.

```java
    public void setProperty(String key, String value) {
        throwIfConfigured();
        properties.setProperty(key, value);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyHelper.java" line="136">

---

<SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyHelper.java" pos="136:9:9" line-data="    public static final void setProperty(HttpServletRequest request,">`setProperty`</SwmToken> grabs a <SwmToken path="taglib/src/main/java/org/apache/struts/taglib/nested/NestedPropertyHelper.java" pos="139:1:1" line-data="        NestedReference nr = referenceInstance(request);">`NestedReference`</SwmToken> tied to the request and sets the nested property via that object, instead of directly on the request. This keeps nested property logic encapsulated and organized.

```java
    public static final void setProperty(HttpServletRequest request,
        String property) {
        // get the old one if any
        NestedReference nr = referenceInstance(request);

        nr.setNestedProperty(property);
    }
```

---

</SwmSnippet>

### Copying Form Property Configurations

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Begin copying properties"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1036:1039"
    subgraph loop1["For each property in existing
configuration"]
        node1 --> node2{"Is property name unique in new
configuration?"}
        click node2 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:430:433"
        node2 -->|"Yes"| node3["Add property to new configuration"]
        click node3 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:435:436"
        node3 --> node2
        node2 -->|"No"| node4["Raise error and stop"]
        click node4 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:431:433"
    end
    node3 --> node5["Replace old configuration with new
configuration"]
    click node5 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1047:1049"
    node5 --> node6["Return updated configuration"]
    click node6 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:1053:1054"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Begin copying properties"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1036:1039"
%%     subgraph loop1["For each property in existing
%% configuration"]
%%         node1 --> node2{"Is property name unique in new
%% configuration?"}
%%         click node2 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:430:433"
%%         node2 -->|"Yes"| node3["Add property to new configuration"]
%%         click node3 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:435:436"
%%         node3 --> node2
%%         node2 -->|"No"| node4["Raise error and stop"]
%%         click node4 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:431:433"
%%     end
%%     node3 --> node5["Replace old configuration with new
%% configuration"]
%%     click node5 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1047:1049"
%%     node5 --> node6["Return updated configuration"]
%%     click node6 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:1053:1054"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="1036">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="979:1:1" line-data="                    processFormBeanConfigClass(beanConfig, moduleConfig);">`processFormBeanConfigClass`</SwmToken>, after copying bean properties, we copy each form property config to the new <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1037:1:1" line-data="                        beanConfig.findFormPropertyConfigs();">`beanConfig`</SwmToken>. Next, we call FormBeanConfig.addFormPropertyConfig to handle uniqueness and lifecycle checks for each property.

```java
                    FormPropertyConfig[] fpc =
                        beanConfig.findFormPropertyConfigs();

                    for (int i = 0; i < fpc.length; i++) {
                        newBeanConfig.addFormPropertyConfig(fpc[i]);
                    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="427">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="427:5:5" line-data="    public void addFormPropertyConfig(FormPropertyConfig config) {">`addFormPropertyConfig`</SwmToken> checks if the config is modifiable, then ensures the property name is unique before adding it to the map. This prevents duplicate property configs and enforces lifecycle constraints.

```java
    public void addFormPropertyConfig(FormPropertyConfig config) {
        throwIfConfigured();

        if (formProperties.containsKey(config.getName())) {
            throw new IllegalArgumentException("Property " + config.getName()
                + " already defined");
        }

        formProperties.put(config.getName(), config);
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="1042">

---

Finally, after adding form property configs, we update <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1047:1:1" line-data="                moduleConfig.removeFormBeanConfig(beanConfig);">`moduleConfig`</SwmToken> to use the new <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1046:5:5" line-data="                // replace beanConfig with newBeanConfig">`beanConfig`</SwmToken> instance. This ensures all future lookups reference the upgraded config, supporting inheritance and property copying in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="979:1:1" line-data="                    processFormBeanConfigClass(beanConfig, moduleConfig);">`processFormBeanConfigClass`</SwmToken>.

```java
                } catch (Exception e) {
                    handleCreationException(baseConfigClassName, e);
                }

                // replace beanConfig with newBeanConfig
                moduleConfig.removeFormBeanConfig(beanConfig);
                moduleConfig.addFormBeanConfig(newBeanConfig);
                beanConfig = newBeanConfig;
            }
        }

        return beanConfig;
    }
```

---

</SwmSnippet>

## Processing Form Bean Extension Hierarchy

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="981">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:5:5" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`processFormBeanExtension`</SwmToken>, after handling class inheritance, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="981:3:3" line-data="                beanConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> on <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="981:1:1" line-data="                beanConfig.processExtends(moduleConfig);">`beanConfig`</SwmToken> to process hierarchical extension logic, including circular checks and ancestor extension processing.

```java
                beanConfig.processExtends(moduleConfig);
            }
        } catch (ServletException e) {
            throw e;
        } catch (Exception e) {
            handleGeneralExtensionException("FormBeanConfig",
                beanConfig.getName(), e);
        }
    }
```

---

</SwmSnippet>

# Validating Form Bean Extension and Circular Inheritance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start processing form bean inheritance"]
    click node1 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:528:534"
    node1 --> node2{"Is configuration frozen?"}
    click node2 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:531:533"
    node2 -->|"Yes"| node5["Abort: Configuration cannot be changed"]
    click node5 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:532:533"
    node2 -->|"No"| node3{"Does this config extend another config
and not yet processed?"}
    click node3 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:535:537"
    node3 -->|"No"| node6["Finish: No inheritance needed"]
    click node6 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:563:563"
    node3 -->|"Yes"| node4["Copying Form Bean Settings from Ancestor"]
    
    node4 --> node6

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node4 goToHeading "Copying Form Bean Settings from Ancestor"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start processing form bean inheritance"]
%%     click node1 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:528:534"
%%     node1 --> node2{"Is configuration frozen?"}
%%     click node2 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:531:533"
%%     node2 -->|"Yes"| node5["Abort: Configuration cannot be changed"]
%%     click node5 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:532:533"
%%     node2 -->|"No"| node3{"Does this config extend another config
%% and not yet processed?"}
%%     click node3 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:535:537"
%%     node3 -->|"No"| node6["Finish: No inheritance needed"]
%%     click node6 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:563:563"
%%     node3 -->|"Yes"| node4["Copying Form Bean Settings from Ancestor"]
%%     
%%     node4 --> node6
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node4 goToHeading "Copying Form Bean Settings from Ancestor"
%% node4:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="528">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="528:5:5" line-data="    public void processExtends(ModuleConfig moduleConfig)">`processExtends`</SwmToken>, we check if the config is frozen, then look up the ancestor config and check for circular inheritance. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="548:4:4" line-data="            if (checkCircularInheritance(moduleConfig)) {">`checkCircularInheritance`</SwmToken> to ensure the extension chain doesn't loop back on itself.

```java
    public void processExtends(ModuleConfig moduleConfig)
        throws ClassNotFoundException, IllegalAccessException,
            InstantiationException, InvocationTargetException {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        String ancestor = getExtends();

        if ((!extensionProcessed) && (ancestor != null)) {
            FormBeanConfig baseConfig =
                moduleConfig.findFormBeanConfig(ancestor);

            if (baseConfig == null) {
                throw new NullPointerException("Unable to find "
                    + "form bean '" + ancestor + "' to extend.");
            }

            // Check against circule inheritance and make sure the base config's
            //  own extends have been processed already
            if (checkCircularInheritance(moduleConfig)) {
                throw new IllegalArgumentException(
                    "Circular inheritance detected for form bean " + getName());
            }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="206">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="206:5:5" line-data="    protected boolean checkCircularInheritance(ModuleConfig moduleConfig) {">`checkCircularInheritance`</SwmToken> walks up the ancestor chain, comparing each ancestor's name to the current config's name. If it finds a match, it flags circular inheritance; otherwise, it keeps going until the chain ends.

```java
    protected boolean checkCircularInheritance(ModuleConfig moduleConfig) {
        String ancestorName = getExtends();

        while (ancestorName != null) {
            // check if we have the same name as an ancestor
            if (getName().equals(ancestorName)) {
                return true;
            }

            // get our ancestor's ancestor
            FormBeanConfig ancestor =
                moduleConfig.findFormBeanConfig(ancestorName);

            ancestorName = ancestor.getExtends();
        }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="553">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="555:3:3" line-data="                baseConfig.processExtends(moduleConfig);">`processExtends`</SwmToken>, after checking for circular inheritance, we make sure the ancestor's extension is processed. If not, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="555:3:3" line-data="                baseConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> on the ancestor, which may trigger <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="939:1:1" line-data="            ActionConfig baseConfig = moduleConfig.findActionConfig(ancestor);">`ActionConfig`</SwmToken> extension logic next.

```java
            // Make sure the ancestor's own extension has been processed.
            if (!baseConfig.isExtensionProcessed()) {
                baseConfig.processExtends(moduleConfig);
            }

```

---

</SwmSnippet>

## Processing Action Extension Hierarchy

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is configuration frozen?"}
  click node1 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1296:1298"
  node1 -->|"Yes"| node2["Stop: Configuration is frozen"]
  click node2 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1297:1298"
  node1 -->|"No"| node3{"Does this action extend another?"}
  click node3 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1302:1302"
  node3 -->|"No"| node9["Inheritance complete"]
  click node9 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1331:1331"
  node3 -->|"Yes"| node4["Resolving Action Config by Path or Pattern"]
  
  node4 --> node5{"Was ancestor found?"}
  click node5 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1305:1312"
  node5 -->|"No"| node6["Stop: Ancestor not found"]
  click node6 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1310:1312"
  node5 -->|"Yes"| node7{"Is there circular inheritance?"}
  
  node7 -->|"Yes"| node8["Stop: Circular inheritance"]
  click node8 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1317:1319"
  node7 -->|"No"| node10{"Has ancestor's inheritance been
processed?"}
  click node10 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1322:1324"
  node10 -->|"No"| node11["Process ancestor's inheritance"]
  click node11 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1323:1324"
  node10 -->|"Yes"| node12["Copying Action Settings from Ancestor"]
  
  node11 --> node12
  node12 --> node13["Mark inheritance as processed"]
  click node13 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1330:1331"
  node13 --> node9
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node4 goToHeading "Resolving Action Config by Path or Pattern"
node4:::HeadingStyle
click node7 goToHeading "Validating Action Extension and Circular Inheritance"
node7:::HeadingStyle
click node12 goToHeading "Copying Action Settings from Ancestor"
node12:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is configuration frozen?"}
%%   click node1 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1296:1298"
%%   node1 -->|"Yes"| node2["Stop: Configuration is frozen"]
%%   click node2 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1297:1298"
%%   node1 -->|"No"| node3{"Does this action extend another?"}
%%   click node3 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1302:1302"
%%   node3 -->|"No"| node9["Inheritance complete"]
%%   click node9 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1331:1331"
%%   node3 -->|"Yes"| node4["Resolving Action Config by Path or Pattern"]
%%   
%%   node4 --> node5{"Was ancestor found?"}
%%   click node5 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1305:1312"
%%   node5 -->|"No"| node6["Stop: Ancestor not found"]
%%   click node6 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1310:1312"
%%   node5 -->|"Yes"| node7{"Is there circular inheritance?"}
%%   
%%   node7 -->|"Yes"| node8["Stop: Circular inheritance"]
%%   click node8 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1317:1319"
%%   node7 -->|"No"| node10{"Has ancestor's inheritance been
%% processed?"}
%%   click node10 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1322:1324"
%%   node10 -->|"No"| node11["Process ancestor's inheritance"]
%%   click node11 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1323:1324"
%%   node10 -->|"Yes"| node12["Copying Action Settings from Ancestor"]
%%   
%%   node11 --> node12
%%   node12 --> node13["Mark inheritance as processed"]
%%   click node13 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1330:1331"
%%   node13 --> node9
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node4 goToHeading "Resolving Action Config by Path or Pattern"
%% node4:::HeadingStyle
%% click node7 goToHeading "Validating Action Extension and Circular Inheritance"
%% node7:::HeadingStyle
%% click node12 goToHeading "Copying Action Settings from Ancestor"
%% node12:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="1293">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="1293:5:5" line-data="    public void processExtends(ModuleConfig moduleConfig)">`processExtends`</SwmToken>, we check if the config is frozen, then look up the ancestor action config. Next, we call ModuleConfigImpl.findActionConfig to resolve the ancestor, which may involve wildcard matching.

```java
    public void processExtends(ModuleConfig moduleConfig)
        throws ClassNotFoundException, IllegalAccessException,
            InstantiationException, InvocationTargetException {
        if (configured) {
            throw new IllegalStateException("Configuration is frozen");
        }

        String ancestor = getExtends();

        if ((!extensionProcessed) && (ancestor != null)) {
            ActionConfig baseConfig =
                moduleConfig.findActionConfig(ancestor);
```

---

</SwmSnippet>

### Resolving Action Config by Path or Pattern

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Find configuration for action path"]
  click node1 openCode "core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java:436:437"
  node1 --> node2{"Is there a direct match?"}
  click node2 openCode "core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java:437:441"
  node2 -->|"Yes"| node3["Return configuration"]
  click node3 openCode "core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java:445:446"
  node2 -->|"No"| node4{"Is a matcher available?"}
  click node4 openCode "core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java:441:443"
  node4 -->|"Yes"| node5["Try wildcard match"]
  click node5 openCode "core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java:442:443"
  node5 --> node3
  node4 -->|"No"| node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Find configuration for action path"]
%%   click node1 openCode "<SwmPath>[core/…/impl/ModuleConfigImpl.java](core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java)</SwmPath>:436:437"
%%   node1 --> node2{"Is there a direct match?"}
%%   click node2 openCode "<SwmPath>[core/…/impl/ModuleConfigImpl.java](core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java)</SwmPath>:437:441"
%%   node2 -->|"Yes"| node3["Return configuration"]
%%   click node3 openCode "<SwmPath>[core/…/impl/ModuleConfigImpl.java](core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java)</SwmPath>:445:446"
%%   node2 -->|"No"| node4{"Is a matcher available?"}
%%   click node4 openCode "<SwmPath>[core/…/impl/ModuleConfigImpl.java](core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java)</SwmPath>:441:443"
%%   node4 -->|"Yes"| node5["Try wildcard match"]
%%   click node5 openCode "<SwmPath>[core/…/impl/ModuleConfigImpl.java](core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java)</SwmPath>:442:443"
%%   node5 --> node3
%%   node4 -->|"No"| node3
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java" line="436">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/impl/ModuleConfigImpl.java" pos="436:5:5" line-data="    public ActionConfig findActionConfig(String path) {">`findActionConfig`</SwmToken> first tries a direct lookup for the action config by path. If that fails and a matcher exists, it uses the matcher to find a config by pattern. Next, we call ActionConfigMatcher.match to handle wildcard resolution.

```java
    public ActionConfig findActionConfig(String path) {
        ActionConfig config = (ActionConfig) actionConfigs.get(path);

        // If a direct match cannot be found, try to match action configs
        // containing wildcard patterns only if a matcher exists.
        if ((config == null) && (matcher != null)) {
            config = matcher.match(path);
        }

        return config;
    }
```

---

</SwmSnippet>

### Wildcard Matching for Action Config Resolution

See <SwmLink doc-title="Matching Paths to Action Configurations">[Matching Paths to Action Configurations](/.swm/matching-paths-to-action-configurations.a49ev0uj.sw.md)</SwmLink>

### Converting and Setting Action Config Attributes

See <SwmLink doc-title="Creating a Customized Action Configuration">[Creating a Customized Action Configuration](/.swm/creating-a-customized-action-configuration.ih4gm0ut.sw.md)</SwmLink>

### Handling Ancestor Lookup and Circular Inheritance in Action Extension

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node2{"Is base action (ancestor) already
found?"}
    click node2 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1305:1307"
    node2 -->|"No"| node7["Look up base action by ancestor ID"]
    click node7 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1306:1306"
    node7 --> node3{"Is base action found after lookup?"}
    click node3 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1309:1312"
    node3 -->|"No"| node4["Error: Cannot find base action for
'ancestor'"]
    click node4 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1310:1312"
    node3 -->|"Yes"| node5{"Is circular inheritance detected?"}
    click node5 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1316:1319"
    node2 -->|"Yes"| node5
    node5 -->|"Yes"| node6["Error: Circular inheritance detected"]
    click node6 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1317:1319"
    node5 -->|"No"| node8["Inheritance processed successfully"]
    click node8 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:1314:1315"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node2{"Is base action (ancestor) already
%% found?"}
%%     click node2 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1305:1307"
%%     node2 -->|"No"| node7["Look up base action by ancestor ID"]
%%     click node7 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1306:1306"
%%     node7 --> node3{"Is base action found after lookup?"}
%%     click node3 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1309:1312"
%%     node3 -->|"No"| node4["Error: Cannot find base action for
%% 'ancestor'"]
%%     click node4 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1310:1312"
%%     node3 -->|"Yes"| node5{"Is circular inheritance detected?"}
%%     click node5 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1316:1319"
%%     node2 -->|"Yes"| node5
%%     node5 -->|"Yes"| node6["Error: Circular inheritance detected"]
%%     click node6 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1317:1319"
%%     node5 -->|"No"| node8["Inheritance processed successfully"]
%%     click node8 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:1314:1315"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="1305">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="981:3:3" line-data="                beanConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> (<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="939:1:1" line-data="            ActionConfig baseConfig = moduleConfig.findActionConfig(ancestor);">`ActionConfig`</SwmToken>), after resolving the ancestor config, we check for circular inheritance before proceeding. This prevents loops in the extension chain.

```java
            if (baseConfig == null) {
                baseConfig = moduleConfig.findActionConfigId(ancestor); 
            }
            
            if (baseConfig == null) {
                throw new NullPointerException("Unable to find "
                    + "action for '" + ancestor + "' to extend.");
            }

            // Check against circular inheritance and make sure the base
            //  config's own extends has been processed already
            if (checkCircularInheritance(moduleConfig)) {
                throw new IllegalArgumentException(
                    "Circular inheritance detected for action " + getPath());
            }

```

---

</SwmSnippet>

### Validating Action Extension and Circular Inheritance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Check for circular inheritance"]
    click node1 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:929:930"
    subgraph loop1["For each ancestor in the inheritance
chain"]
        node2{"Is ancestor present?"}
        click node2 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:932:932"
        node2 -->|"No"| node6["No circular inheritance found"]
        click node6 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:951:952"
        node2 -->|"Yes"| node3{"Does ancestor equal action path or
action id?"}
        click node3 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:934:935"
        node3 -->|"Yes"| node5["Circular inheritance detected"]
        click node5 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:935:936"
        node3 -->|"No"| node4{"Is ancestor found in configuration?"}
        click node4 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:939:942"
        node4 -->|"Yes"| node7["Set ancestor to next in chain"]
        click node7 openCode "core/src/main/java/org/apache/struts/config/ActionConfig.java:945:946"
        node7 --> node2
        node4 -->|"No"| node6
    end

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Check for circular inheritance"]
%%     click node1 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:929:930"
%%     subgraph loop1["For each ancestor in the inheritance
%% chain"]
%%         node2{"Is ancestor present?"}
%%         click node2 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:932:932"
%%         node2 -->|"No"| node6["No circular inheritance found"]
%%         click node6 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:951:952"
%%         node2 -->|"Yes"| node3{"Does ancestor equal action path or
%% action id?"}
%%         click node3 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:934:935"
%%         node3 -->|"Yes"| node5["Circular inheritance detected"]
%%         click node5 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:935:936"
%%         node3 -->|"No"| node4{"Is ancestor found in configuration?"}
%%         click node4 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:939:942"
%%         node4 -->|"Yes"| node7["Set ancestor to next in chain"]
%%         click node7 openCode "<SwmPath>[core/…/config/ActionConfig.java](core/src/main/java/org/apache/struts/config/ActionConfig.java)</SwmPath>:945:946"
%%         node7 --> node2
%%         node4 -->|"No"| node6
%%     end
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="929">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="929:5:5" line-data="    protected boolean checkCircularInheritance(ModuleConfig moduleConfig) {">`checkCircularInheritance`</SwmToken>, we walk up the ancestor chain, comparing each ancestor's path and <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="79:5:5" line-data="    protected String actionId = null;">`actionId`</SwmToken> to the current config. If a match is found, we flag circular inheritance; otherwise, we keep traversing using ModuleConfigImpl.findActionConfig.

```java
    protected boolean checkCircularInheritance(ModuleConfig moduleConfig) {
        String ancestor = getExtends();

        while (ancestor != null) {
            // check if we have the same path or id as an ancestor
            if (getPath().equals(ancestor) || ancestor.equals(getActionId())) {
                return true;
            }

            // get our ancestor's config
            ActionConfig baseConfig = moduleConfig.findActionConfig(ancestor);
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="940">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="206:5:5" line-data="    protected boolean checkCircularInheritance(ModuleConfig moduleConfig) {">`checkCircularInheritance`</SwmToken>, we update ancestor each loop, traversing the chain until either a loop is found or the chain ends. If no loop is found, we return false.

```java
            if (baseConfig == null) {
                baseConfig = moduleConfig.findActionConfigId(ancestor); 
            }

            if (baseConfig != null) {
                ancestor = baseConfig.getExtends();
            } else {
                ancestor = null;
            }
        }

        return false;
    }
```

---

</SwmSnippet>

### Completing Action Extension and Inheritance

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="1321">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="1323:3:3" line-data="                baseConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> (<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="939:1:1" line-data="            ActionConfig baseConfig = moduleConfig.findActionConfig(ancestor);">`ActionConfig`</SwmToken>), after ensuring the ancestor's extension is processed, we call <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="1327:1:1" line-data="            inheritFrom(baseConfig);">`inheritFrom`</SwmToken> to copy settings from the ancestor. This finalizes inheritance for the current action config.

```java
            // Make sure the ancestor's own extension has been processed.
            if (!baseConfig.isExtensionProcessed()) {
                baseConfig.processExtends(moduleConfig);
            }

            // Copy values from the base config
            inheritFrom(baseConfig);
        }

```

---

</SwmSnippet>

### Copying Action Settings from Ancestor

See <SwmLink doc-title="Inheriting Action Configuration Properties">[Inheriting Action Configuration Properties](/.swm/inheriting-action-configuration-properties.ewxymqs9.sw.md)</SwmLink>

### Finalizing Action Extension Processing

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/ActionConfig.java" line="1330">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="981:3:3" line-data="                beanConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> (<SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="939:1:1" line-data="            ActionConfig baseConfig = moduleConfig.findActionConfig(ancestor);">`ActionConfig`</SwmToken>), we mark <SwmToken path="core/src/main/java/org/apache/struts/config/ActionConfig.java" pos="1330:1:1" line-data="        extensionProcessed = true;">`extensionProcessed`</SwmToken> as true to signal that inheritance and extension logic is complete for this config.

```java
        extensionProcessed = true;
    }
```

---

</SwmSnippet>

## Completing Form Bean Extension and Inheritance

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="558">

---

Back in <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="981:3:3" line-data="                beanConfig.processExtends(moduleConfig);">`processExtends`</SwmToken> (<SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="968:7:7" line-data="    protected void processFormBeanExtension(FormBeanConfig beanConfig,">`FormBeanConfig`</SwmToken>), after ancestor extension processing, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="559:1:1" line-data="            inheritFrom(baseConfig);">`inheritFrom`</SwmToken> to copy values from the base config, finalizing inheritance for the current bean config.

```java
            // Copy values from the base config
            inheritFrom(baseConfig);
        }

```

---

</SwmSnippet>

## Copying Form Bean Settings from Ancestor

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Begin inheriting configuration values"]
    click node1 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:497:500"
    node1 --> node2{"Are there values not set in current
configuration?"}
    click node2 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:503:513"
    node2 -->|"Yes"| node3["Setting and Resolving Form Bean Type"]
    
    node2 -->|"No"| node4["Skip inheritance of those values"]
    click node4 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:503:513"
    node3 --> node5["Copying and Merging Form Property Definitions"]
    
    node4 --> node5

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node3 goToHeading "Setting and Resolving Form Bean Type"
node3:::HeadingStyle
click node5 goToHeading "Copying and Merging Form Property Definitions"
node5:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Begin inheriting configuration values"]
%%     click node1 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:497:500"
%%     node1 --> node2{"Are there values not set in current
%% configuration?"}
%%     click node2 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:503:513"
%%     node2 -->|"Yes"| node3["Setting and Resolving Form Bean Type"]
%%     
%%     node2 -->|"No"| node4["Skip inheritance of those values"]
%%     click node4 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:503:513"
%%     node3 --> node5["Copying and Merging Form Property Definitions"]
%%     
%%     node4 --> node5
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node3 goToHeading "Setting and Resolving Form Bean Type"
%% node3:::HeadingStyle
%% click node5 goToHeading "Copying and Merging Form Property Definitions"
%% node5:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="497">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="497:5:5" line-data="    public void inheritFrom(FormBeanConfig config)">`inheritFrom`</SwmToken>, we check if the config is modifiable, then set the name from the ancestor only if it's not already set. Next, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="504:1:1" line-data="            setName(config.getName());">`setName`</SwmToken> to update the field if needed.

```java
    public void inheritFrom(FormBeanConfig config)
        throws ClassNotFoundException, IllegalAccessException,
            InstantiationException, InvocationTargetException {
        throwIfConfigured();

        // Inherit values that have not been overridden
        if (getName() == null) {
            setName(config.getName());
        }

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="152">

---

<SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="152:5:5" line-data="    public void setName(String name) {">`setName`</SwmToken> checks if the config is still modifiable, then updates the name field. This enforces immutability after configuration is finalized.

```java
    public void setName(String name) {
        throwIfConfigured();
        this.name = name;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="507">

---

After returning from FormBeanConfig.inheritFrom, we check if the current config should inherit the 'restricted' flag and the 'type' from the ancestor. If these aren't set, we update them now. This ensures the config is fully aligned with its parent before moving on to type-specific logic.

```java
        if (!isRestricted()) {
            setRestricted(config.isRestricted());
        }

        if (getType() == null) {
            setType(config.getType());
        }

```

---

</SwmSnippet>

### Setting and Resolving Form Bean Type

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Set form bean type to given value"]
  click node1 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:161:163"
  node1 --> node0["Check if configuration is allowed"]
  click node0 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:162:162"
  node0 --> node2{"Is form bean class found for type?"}
  click node2 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:166:168"
  node2 -->|"Yes"| node3{"Is form bean class dynamic?"}
  click node3 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:169:171"
  node2 -->|"No"| node6["Form bean is static ('dynamic' set to
false)"]
  click node6 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:175:176"
  node3 -->|"Yes"| node4["Form bean is dynamic ('dynamic' set to
true)"]
  click node4 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:170:170"
  node3 -->|"No"| node6

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Set form bean type to given value"]
%%   click node1 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:161:163"
%%   node1 --> node0["Check if configuration is allowed"]
%%   click node0 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:162:162"
%%   node0 --> node2{"Is form bean class found for type?"}
%%   click node2 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:166:168"
%%   node2 -->|"Yes"| node3{"Is form bean class dynamic?"}
%%   click node3 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:169:171"
%%   node2 -->|"No"| node6["Form bean is static ('dynamic' set to
%% false)"]
%%   click node6 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:175:176"
%%   node3 -->|"Yes"| node4["Form bean is dynamic ('dynamic' set to
%% true)"]
%%   click node4 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:170:170"
%%   node3 -->|"No"| node6
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="161">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="161:5:5" line-data="    public void setType(String type) {">`setType`</SwmToken>, we first make sure the config isn't already locked down by calling <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="162:1:1" line-data="        throwIfConfigured();">`throwIfConfigured`</SwmToken>. Then we set the type and figure out if this bean is dynamic by checking if the resolved class is a <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="165:7:7" line-data="        Class dynaBeanClass = DynaActionForm.class;">`DynaActionForm`</SwmToken>. This impacts how the bean is used later, especially for dynamic property handling.

```java
    public void setType(String type) {
        throwIfConfigured();
        this.type = type;

        Class dynaBeanClass = DynaActionForm.class;
        Class formBeanClass = formBeanClass();

```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="603">

---

FormBeanClass tries to load the class named by <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="612:8:10" line-data="            return (classLoader.loadClass(getType()));">`getType()`</SwmToken> using the thread's context class loader, falling back to the class's own loader if needed. If loading fails, it just returns null. This lets the framework support different deployment setups where classes might live in different class loaders.

```java
    protected Class formBeanClass() {
        ClassLoader classLoader =
            Thread.currentThread().getContextClassLoader();

        if (classLoader == null) {
            classLoader = this.getClass().getClassLoader();
        }

        try {
            return (classLoader.loadClass(getType()));
        } catch (Exception e) {
            return (null);
        }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="168">

---

After coming back from <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="168:4:4" line-data="        if (formBeanClass != null) {">`formBeanClass`</SwmToken>, we check if the loaded class is a <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="165:7:7" line-data="        Class dynaBeanClass = DynaActionForm.class;">`DynaActionForm`</SwmToken>. If it is, we set the dynamic flag to true; otherwise, it's false. This controls how the framework treats the bean for property handling and validation.

```java
        if (formBeanClass != null) {
            if (dynaBeanClass.isAssignableFrom(formBeanClass)) {
                this.dynamic = true;
            } else {
                this.dynamic = false;
            }
        } else {
            this.dynamic = false;
        }
    }
```

---

</SwmSnippet>

### Inheriting Form Property Configurations

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="515">

---

After <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="161:5:5" line-data="    public void setType(String type) {">`setType`</SwmToken>, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="515:1:1" line-data="        inheritFormProperties(config);">`inheritFormProperties`</SwmToken> to pull in any form property configs from the parent that aren't already present. This step ensures the bean config has all the necessary property definitions before merging other settings.

```java
        inheritFormProperties(config);
```

---

</SwmSnippet>

### Copying and Merging Form Property Definitions

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start property inheritance from base
configuration"]
    click node1 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:232:258"
    subgraph loop1["For each property in base configuration"]
        node1 --> node2{"Does current configuration have this
property?"}
        click node2 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:240:246"
        node2 -->|"No"| node3["Copy property to current configuration"]
        click node3 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:247:255"
        node2 -->|"Yes"| node4["Continue to next property"]
        click node4 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:256:256"
        node3 --> node4
    end
    loop1 --> node5["All properties processed"]
    click node5 openCode "core/src/main/java/org/apache/struts/config/FormBeanConfig.java:257:258"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start property inheritance from base
%% configuration"]
%%     click node1 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:232:258"
%%     subgraph loop1["For each property in base configuration"]
%%         node1 --> node2{"Does current configuration have this
%% property?"}
%%         click node2 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:240:246"
%%         node2 -->|"No"| node3["Copy property to current configuration"]
%%         click node3 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:247:255"
%%         node2 -->|"Yes"| node4["Continue to next property"]
%%         click node4 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:256:256"
%%         node3 --> node4
%%     end
%%     loop1 --> node5["All properties processed"]
%%     click node5 openCode "<SwmPath>[core/…/config/FormBeanConfig.java](core/src/main/java/org/apache/struts/config/FormBeanConfig.java)</SwmPath>:257:258"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="232">

---

In <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="232:5:5" line-data="    protected void inheritFormProperties(FormBeanConfig config)">`inheritFormProperties`</SwmToken>, we loop through the parent's property configs. If the child doesn't already have a property, we clone it using <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="253:1:3" line-data="                BeanUtils.copyProperties(prop, baseFpc);">`BeanUtils.copyProperties`</SwmToken>. This avoids shared state between parent and child configs.

```java
    protected void inheritFormProperties(FormBeanConfig config)
        throws ClassNotFoundException, IllegalAccessException,
            InstantiationException, InvocationTargetException {
        throwIfConfigured();

        // Inherit form property configs
        FormPropertyConfig[] baseFpcs = config.findFormPropertyConfigs();

        for (int i = 0; i < baseFpcs.length; i++) {
            FormPropertyConfig baseFpc = baseFpcs[i];

            // Do we have this prop?
            FormPropertyConfig prop =
                this.findFormPropertyConfig(baseFpc.getName());

            if (prop == null) {
                // We don't have this, so let's copy it
                prop =
                    (FormPropertyConfig) RequestUtils.applicationInstance(baseFpc.getClass()
                                                                                 .getName());

                BeanUtils.copyProperties(prop, baseFpc);
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="254">

---

After copying the property config, we add it to the child config with <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="254:3:3" line-data="                this.addFormPropertyConfig(prop);">`addFormPropertyConfig`</SwmToken>. This makes sure the new property is registered and available for later use.

```java
                this.addFormPropertyConfig(prop);
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="255">

---

After adding the property config, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="255:3:3" line-data="                prop.setProperties(baseFpc.copyProperties());">`setProperties`</SwmToken> with a copy of the parent's properties. This step finalizes the property config so it has all the right settings, fully independent from the parent.

```java
                prop.setProperties(baseFpc.copyProperties());
            }
        }
    }
```

---

</SwmSnippet>

### Merging Remaining Config Properties

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="516">

---

After finishing up with form property configs, we call <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="516:1:1" line-data="        inheritProperties(config);">`inheritProperties`</SwmToken> to merge in any remaining settings from the parent config. This wraps up the inheritance process for the bean config.

```java
        inheritProperties(config);
    }
```

---

</SwmSnippet>

## Marking Extension as Complete

<SwmSnippet path="/core/src/main/java/org/apache/struts/config/FormBeanConfig.java" line="562">

---

After all the inheritance and merging, we set <SwmToken path="core/src/main/java/org/apache/struts/config/FormBeanConfig.java" pos="562:1:1" line-data="        extensionProcessed = true;">`extensionProcessed`</SwmToken> to true. This tells the framework we're done with extension logic for this config, so it won't get processed again.

```java
        extensionProcessed = true;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
