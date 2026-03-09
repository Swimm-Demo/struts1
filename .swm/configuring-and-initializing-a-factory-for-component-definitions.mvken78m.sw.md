---
title: Configuring and Initializing a Factory for Component Definitions
---
This document describes how the system selects and initializes a new factory for component definitions based on configuration details. The process ensures that the correct factory is active and ready to handle component definitions according to the current setup.

# Configuring the Factory Wrapper

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="121">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="121:5:5" line-data="    public void setConfig(">`setConfig`</SwmToken>, we're grabbing the factory class name from the config and immediately creating a new factory instance with it. This step is about swapping in the right factory implementation before we do any further setup.

```java
    public void setConfig(
        DefinitionsFactoryConfig config,
        ServletContext servletContext)
        throws DefinitionsFactoryException {

        ComponentDefinitionsFactory newFactory =
            createFactoryInstance(config.getFactoryClassname());

```

---

</SwmSnippet>

## Instantiating the Factory Class

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="156">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="156:5:5" line-data="    protected ComponentDefinitionsFactory createFactoryInstance(String classname)">`createFactoryInstance`</SwmToken>, we're loading the factory class and instantiating it. This is where we actually turn the class name from the config into a live object. The next step (which jumps into DynaActionFormClass.newInstance) is about handling the instantiation, including any special logic or error handling needed for dynamic class creation.

```java
    protected ComponentDefinitionsFactory createFactoryInstance(String classname)
        throws DefinitionsFactoryException {

        try {
            Class factoryClass = RequestUtils.applicationClass(classname);
            Object factory = factoryClass.newInstance();
            return (ComponentDefinitionsFactory) factory;

        } catch (ClassCastException ex) { // Bad classname
            throw new DefinitionsFactoryException(
                "Error - createDefinitionsFactory : Factory class '"
                    + classname
                    + " must implement 'DefinitionsFactory'.",
                ex);

        } catch (ClassNotFoundException ex) { // Bad classname
            throw new DefinitionsFactoryException(
                "Error - createDefinitionsFactory : Bad class name '"
                    + classname
                    + "'.",
                ex);

```

---

</SwmSnippet>

### Dynamic Instantiation Logic

See <SwmLink doc-title="Creating a Dynamic Form Instance">[Creating a Dynamic Form Instance](/.swm/creating-a-dynamic-form-instance.7l8fu842.sw.md)</SwmLink>

### Handling Instantiation Errors

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="178">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="127:1:1" line-data="            createFactoryInstance(config.getFactoryClassname());">`createFactoryInstance`</SwmToken>, after trying to instantiate the class (which could have failed for a bunch of reasons), we catch any instantiation or access errors and wrap them in a <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="179:5:5" line-data="            throw new DefinitionsFactoryException(ex);">`DefinitionsFactoryException`</SwmToken>. This keeps error handling unified for the caller.

```java
        } catch (InstantiationException ex) { // Bad constructor or error
            throw new DefinitionsFactoryException(ex);

        } catch (IllegalAccessException ex) {
            throw new DefinitionsFactoryException(ex);
        }

    }
```

---

</SwmSnippet>

## Initializing the Factory with Config

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="129">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="121:5:5" line-data="    public void setConfig(">`setConfig`</SwmToken>, after getting the new factory, we call its <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="129:3:3" line-data="        newFactory.initFactory(servletContext, createConfigMap(config));">`initFactory`</SwmToken> method and pass in the servlet context plus a config map. The config map is built right here to bundle all the config details in a way the factory expects.

```java
        newFactory.initFactory(servletContext, createConfigMap(config));
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="202">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="202:7:7" line-data="    public static Map createConfigMap(DefinitionsFactoryConfig config) {">`createConfigMap`</SwmToken> builds a map from the config's attributes, adds the definitions config files and parser validate flag using repo-specific constant keys, and only adds the factory classname if it's not <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="213:16:16" line-data="        if (!&quot;org.apache.struts.tiles.xmlDefinition.I18nFactorySet&quot;">`I18nFactorySet`</SwmToken>. This map is what gets passed to the factory for setup.

```java
    public static Map createConfigMap(DefinitionsFactoryConfig config) {
        Map map = new HashMap(config.getAttributes());
        // Add property attributes using old names
        map.put(
            DefinitionsFactoryConfig.DEFINITIONS_CONFIG_PARAMETER_NAME,
            config.getDefinitionConfigFiles());

        map.put(
            DefinitionsFactoryConfig.PARSER_VALIDATE_PARAMETER_NAME,
            (config.getParserValidate() ? Boolean.TRUE.toString() : Boolean.FALSE.toString()));

        if (!"org.apache.struts.tiles.xmlDefinition.I18nFactorySet"
            .equals(config.getFactoryClassname())) {

            map.put(
                DefinitionsFactoryConfig.FACTORY_CLASSNAME_PARAMETER_NAME,
                config.getFactoryClassname());
        }

        return map;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" line="130">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/definition/ComponentDefinitionsFactoryWrapper.java" pos="121:5:5" line-data="    public void setConfig(">`setConfig`</SwmToken>, after initializing the new factory with the config map, we swap it in as the active factory. From here on, everything uses this new setup.

```java
        factory = newFactory;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
