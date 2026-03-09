---
title: Locale-Based Factory Loading
---
This document describes how configuration factories are selected and loaded based on locale. The flow receives a locale key and servlet context, builds a list of possible locale postfixes, checks for loaded factories, and attempts to load XML configuration files for each. When a config is found, base and intermediate files are merged, inheritance is resolved, and a new factory instance is created and cached. If no locale-specific config is found, the default factory is used. This process enables internationalization and layered configuration overrides for tile definitions.

```mermaid
flowchart TD
  node1["Locale-Based Factory Selection and Loading"]:::HeadingStyle
  click node1 goToHeading "Locale-Based Factory Selection and Loading"
  node1 --> node2{"Is locale key provided?"}
  node2 -->|"No"| node7["Factory Instantiation and Caching
(default)
(Factory Instantiation and Caching)"]:::HeadingStyle
  click node7 goToHeading "Factory Instantiation and Caching"
  node2 -->|"Yes"| node3["XML Filename Construction and Parsing"]:::HeadingStyle
  click node3 goToHeading "XML Filename Construction and Parsing"
  node3 --> node4{"Is factory already loaded for this
locale?"}
  node4 -->|"Yes"| node7
  node4 -->|"No"| node5{"Is config file available for this
locale?"}
  node5 -->|"No"| node7
  node5 -->|"Yes"| node6["Merging and Inheritance Extension"]:::HeadingStyle
  click node6 goToHeading "Merging and Inheritance Extension"
  node6 --> node7
  click node7 goToHeading "Factory Instantiation and Caching"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Locale-Based Factory Selection and Loading

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is locale key provided?"}
  click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:327:329"
  node1 -->|"No"| node10["Return default factory"]
  click node10 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:328:329"
  node1 -->|"Yes"| node2["Build list of possible locale postfixes"]
  click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:332:332"

  subgraph loop1["For each locale postfix in
possiblePostfixes (most specific to
least)"]
    node2 --> node3{"Is factory already loaded for this
postfix?"}
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:346:347"
    node3 -->|"Yes"| node9["Return loaded factory"]
    click node9 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:348:349"
    node3 -->|"No"| node4{"Can config file be loaded for this
postfix?"}
    click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:352:353"
    node4 -->|"Yes"| node5["Found config, stop search"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:353:354"
    node4 -->|"No"| node3
  end

  node5 --> node6["Definition Set Merging"]
  

  subgraph loop2["For each intermediate postfix before
found"]
    node6 --> node7["Definition Set Merging"]
    
    node7 --> node6
  end

  node6 --> node8["Inheritance Chain Resolution"]
  
  node8 --> node11["Create and return new factory"]
  click node11 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:375:383"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Definition Set Merging"
node6:::HeadingStyle
click node7 goToHeading "Definition Set Merging"
node7:::HeadingStyle
click node8 goToHeading "Inheritance Chain Resolution"
node8:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is locale key provided?"}
%%   click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:327:329"
%%   node1 -->|"No"| node10["Return default factory"]
%%   click node10 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:328:329"
%%   node1 -->|"Yes"| node2["Build list of possible locale postfixes"]
%%   click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:332:332"
%% 
%%   subgraph loop1["For each locale postfix in
%% <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="332:3:3" line-data="        List possiblePostfixes = calculateSuffixes((Locale) key);">`possiblePostfixes`</SwmToken> (most specific to
%% least)"]
%%     node2 --> node3{"Is factory already loaded for this
%% postfix?"}
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:346:347"
%%     node3 -->|"Yes"| node9["Return loaded factory"]
%%     click node9 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:348:349"
%%     node3 -->|"No"| node4{"Can config file be loaded for this
%% postfix?"}
%%     click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:352:353"
%%     node4 -->|"Yes"| node5["Found config, stop search"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:353:354"
%%     node4 -->|"No"| node3
%%   end
%% 
%%   node5 --> node6["Definition Set Merging"]
%%   
%% 
%%   subgraph loop2["For each intermediate postfix before
%% found"]
%%     node6 --> node7["Definition Set Merging"]
%%     
%%     node7 --> node6
%%   end
%% 
%%   node6 --> node8["Inheritance Chain Resolution"]
%%   
%%   node8 --> node11["Create and return new factory"]
%%   click node11 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:375:383"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Definition Set Merging"
%% node6:::HeadingStyle
%% click node7 goToHeading "Definition Set Merging"
%% node7:::HeadingStyle
%% click node8 goToHeading "Inheritance Chain Resolution"
%% node8:::HeadingStyle
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="321">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="321:5:5" line-data="    protected DefinitionsFactory createFactory(">`createFactory`</SwmToken>, the code expects the key to be a Locale (even though it's typed as Object), and immediately casts it. It then builds a list of locale suffixes, checks for already-loaded factories for each suffix (from most specific to least), and tries to load XML config files for each. If nothing is found, it falls back to the default factory. If a config is found, it loads the base and intermediate configs, merges them, resolves inheritance, creates a new <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="321:3:3" line-data="    protected DefinitionsFactory createFactory(">`DefinitionsFactory`</SwmToken>, caches it, and returns it. This is how it supports layered, locale-specific configuration.

```java
    protected DefinitionsFactory createFactory(
        Object key,
        ServletRequest request,
        ServletContext servletContext)
        throws DefinitionsFactoryException {

        if (key == null) {
            return getDefaultFactory();
        }

        // Build possible postfixes
        List possiblePostfixes = calculateSuffixes((Locale) key);

        // Search last postix corresponding to a config file to load.
        // First check if something is loaded for this postfix.
        // If not, try to load its config.
        XmlDefinitionsSet lastXmlFile = null;
        DefinitionsFactory factory = null;
        String curPostfix = null;
        int i = 0;

        for (i = possiblePostfixes.size() - 1; i >= 0; i--) {
            curPostfix = (String) possiblePostfixes.get(i);

            // Already loaded ?
            factory = (DefinitionsFactory) loaded.get(curPostfix);
            if (factory != null) { // yes, stop search
                return factory;
            }

            // Try to load it. If success, stop search
            lastXmlFile = parseXmlFiles(servletContext, curPostfix, null);
            if (lastXmlFile != null) {
                break;
            }
        }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="364">

---

After finding the most specific locale config, we load the base and all intermediate XML files, merging them into the root config. This is where we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="366:7:7" line-data="        XmlDefinitionsSet rootXmlConfig = parseXmlFiles(servletContext, &quot;&quot;, null);">`parseXmlFiles`</SwmToken> for each suffix, so that all relevant settings and inheritance chains are included before building the final factory.

```java
        // We found something. Need to load base and intermediate files
        String lastPostfix = curPostfix;
        XmlDefinitionsSet rootXmlConfig = parseXmlFiles(servletContext, "", null);
        for (int j = 0; j < i; j++) {
            curPostfix = (String) possiblePostfixes.get(j);
            parseXmlFiles(servletContext, curPostfix, rootXmlConfig);
        }

```

---

</SwmSnippet>

## XML Filename Construction and Parsing

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is a language/region postfix provided
and non-empty?"}
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:441:443"
    node1 -->|"Yes"| node2["Use provided postfix for file variants"]
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:441:443"
    node1 -->|"No"| node3["Use default files (no postfix)"]
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:441:443"

    subgraph loop1["For each XML definition file in
filenames"]
        node4["Combine filename with postfix (if any)"]
        click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:448:448"
        node5["Parse XML file and update definitions
set"]
        click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:449:450"
        node4 --> node5
    end
    node2 --> loop1
    node3 --> loop1
    loop1 --> node6["Return updated definitions set"]
    click node6 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:452:453"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is a language/region postfix provided
%% and non-empty?"}
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:441:443"
%%     node1 -->|"Yes"| node2["Use provided postfix for file variants"]
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:441:443"
%%     node1 -->|"No"| node3["Use default files (no postfix)"]
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:441:443"
%% 
%%     subgraph loop1["For each XML definition file in
%% filenames"]
%%         node4["Combine filename with postfix (if any)"]
%%         click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:448:448"
%%         node5["Parse XML file and update definitions
%% set"]
%%         click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:449:450"
%%         node4 --> node5
%%     end
%%     node2 --> loop1
%%     node3 --> loop1
%%     loop1 --> node6["Return updated definitions set"]
%%     click node6 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:452:453"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="435">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="435:5:5" line-data="    protected XmlDefinitionsSet parseXmlFiles(">`parseXmlFiles`</SwmToken>, we loop through all configured filenames and use <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="448:7:7" line-data="            String filename = concatPostfix((String) i.next(), postfix);">`concatPostfix`</SwmToken> to add the locale or config postfix to each one. This is how we generate the actual filenames to look for, so we can parse the right XML files for the current context.

```java
    protected XmlDefinitionsSet parseXmlFiles(
        ServletContext servletContext,
        String postfix,
        XmlDefinitionsSet xmlDefinitions)
        throws DefinitionsFactoryException {

        if (postfix != null && postfix.length() == 0) {
            postfix = null;
        }

        // Iterate throw each file name in list
        Iterator i = filenames.iterator();
        while (i.hasNext()) {
            String filename = concatPostfix((String) i.next(), postfix);
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="542">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="542:5:5" line-data="    private String concatPostfix(String name, String postfix) {">`concatPostfix`</SwmToken> figures out where to insert the postfix—right before the file extension, unless the dot is part of a directory name. It uses the path separator to avoid messing up directory structures. This way, the generated filename is always valid for the intended config file.

```java
    private String concatPostfix(String name, String postfix) {
        if (postfix == null) {
            return name;
        }

        // Search file name extension.
        // take care of Unix files starting with .
        int dotIndex = name.lastIndexOf(".");
        int lastNameStart = name.lastIndexOf(java.io.File.pathSeparator);
        if (dotIndex < 1 || dotIndex < lastNameStart) {
            return name + postfix;
        }

        String ext = name.substring(dotIndex);
        name = name.substring(0, dotIndex);
        return name + postfix + ext;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="449">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="352:5:5" line-data="            lastXmlFile = parseXmlFiles(servletContext, curPostfix, null);">`parseXmlFiles`</SwmToken>, after building the right filename with <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="448:7:7" line-data="            String filename = concatPostfix((String) i.next(), postfix);">`concatPostfix`</SwmToken>, we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="449:5:5" line-data="            xmlDefinitions = parseXmlFile(servletContext, filename, xmlDefinitions);">`parseXmlFile`</SwmToken> to actually read and load the XML definitions from that file into our config set.

```java
            xmlDefinitions = parseXmlFile(servletContext, filename, xmlDefinitions);
        }

        return xmlDefinitions;
    }
```

---

</SwmSnippet>

## XML File Parsing and Definition Loading

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Try to load XML file (filename) from
multiple sources"]
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:473:491"
    node1 --> node2{"Was file found?"}
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:494:499"
    node2 -->|"No"| node5["Return current definitions set"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:498:499"
    node2 -->|"Yes"| node3["Ensure definitions set exists"]
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:510:512"
    node3 --> node4["XML Content Digestion"]
    
    node4 --> node5

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node4 goToHeading "XML Content Digestion"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Try to load XML file (filename) from
%% multiple sources"]
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:473:491"
%%     node1 --> node2{"Was file found?"}
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:494:499"
%%     node2 -->|"No"| node5["Return current definitions set"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:498:499"
%%     node2 -->|"Yes"| node3["Ensure definitions set exists"]
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:510:512"
%%     node3 --> node4["XML Content Digestion"]
%%     
%%     node4 --> node5
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node4 goToHeading "XML Content Digestion"
%% node4:::HeadingStyle
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="467">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="467:5:5" line-data="    protected XmlDefinitionsSet parseXmlFile(">`parseXmlFile`</SwmToken>, we try to open the XML config file from the servlet context, filesystem, or class loader. If we find it, we set up the parser and parse the file into the definitions set. This is where the actual XML content gets loaded, so we call `XmlParser.parse` next.

```java
    protected XmlDefinitionsSet parseXmlFile(
        ServletContext servletContext,
        String filename,
        XmlDefinitionsSet xmlDefinitions)
        throws DefinitionsFactoryException {

        try {
            InputStream input = servletContext.getResourceAsStream(filename);
            // Try to load using real path.
            // This allow to load config file under websphere 3.5.x
            // Patch proposed Houston, Stephen (LIT) on 5 Apr 2002
            if (null == input) {
                try {
                    input =
                        new java.io.FileInputStream(
                            servletContext.getRealPath(filename));
                } catch (Exception e) {
                }
            }

            // If the config isn't in the servlet context, try the class loader
            // which allows the config files to be stored in a jar
            if (input == null) {
                input = getClass().getResourceAsStream(filename);
            }

            // If still nothing found, this mean no config file is associated
            if (input == null) {
                if (log.isDebugEnabled()) {
                    log.debug("Can't open file '" + filename + "'");
                }
                return xmlDefinitions;
            }

            // Check if parser already exist.
            // Doesn't seem to work yet.
            //if( xmlParser == null )
            if (true) {
                xmlParser = new XmlParser();
                xmlParser.setValidating(isValidatingParser);
            }

            // Check if definition set already exist.
            if (xmlDefinitions == null) {
                xmlDefinitions = new XmlDefinitionsSet();
            }

            xmlParser.parse(input, xmlDefinitions);

```

---

</SwmSnippet>

### XML Content Digestion

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Receive XML input stream and definitions
set"]
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java:275:276"
    node1 --> node2["Attempt to parse XML and load
definitions into set"]
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java:277:284"
    node2 --> node3{"Parsing successful?"}
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java:285:290"
    node3 -->|"Yes"| node4["Definitions loaded for application use"]
    click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java:284:285"
    node3 -->|"No (SAXException)"| node5["Parsing failed: error reported"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java:286:290"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Receive XML input stream and definitions
%% set"]
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlParser.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java)</SwmPath>:275:276"
%%     node1 --> node2["Attempt to parse XML and load
%% definitions into set"]
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlParser.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java)</SwmPath>:277:284"
%%     node2 --> node3{"Parsing successful?"}
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlParser.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java)</SwmPath>:285:290"
%%     node3 -->|"Yes"| node4["Definitions loaded for application use"]
%%     click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlParser.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java)</SwmPath>:284:285"
%%     node3 -->|"No (<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="516:6:6" line-data="        } catch (SAXException ex) {">`SAXException`</SwmToken>)"| node5["Parsing failed: error reported"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlParser.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java)</SwmPath>:286:290"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java" line="275">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlParser.java" pos="275:5:5" line-data="  public void parse( InputStream in, XmlDefinitionsSet definitions ) throws IOException, SAXException">`parse`</SwmToken> pushes the definitions set onto the digester stack and parses the XML input stream, filling the set with definitions. If parsing fails, it throws an exception. This is where the XML is actually turned into usable config data.

```java
  public void parse( InputStream in, XmlDefinitionsSet definitions ) throws IOException, SAXException
  {
    try
    {
      // set first object in stack
    //digester.clear();
    digester.push(definitions);
      // parse
      digester.parse(in);
      in.close();
      }
  catch (SAXException e)
    {
      //throw new ServletException( "Error while parsing " + mappingConfig, e);
    throw e;
      }

  }
```

---

</SwmSnippet>

### User Database Finalization

<SwmSnippet path="/apps/faces-example1/src/main/java/org/apache/struts/webapp/example/memory/MemoryUserDatabase.java" line="106">

---

<SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/memory/MemoryUserDatabase.java" pos="106:5:5" line-data="    public void close() throws Exception {">`close`</SwmToken> just calls <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/memory/MemoryUserDatabase.java" pos="108:1:1" line-data="        save();">`save`</SwmToken> to persist any changes before shutting down the <SwmToken path="apps/faces-example1/src/main/java/org/apache/struts/webapp/example/memory/MemoryUserDatabase.java" pos="45:23:25" line-data=" * &lt;p&gt;Concrete implementation of {@link UserDatabase} for an in-memory">`in-memory`</SwmToken> user database. This ensures nothing is lost when the database is closed.

```java
    public void close() throws Exception {

        save();

    }
```

---

</SwmSnippet>

### User Data Persistence

See <SwmLink doc-title="Saving user and subscription data">[Saving user and subscription data](/.swm/saving-user-and-subscription-data.vwkqw5ia.sw.md)</SwmLink>

### XML Parse Completion and Error Handling

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Parse XML file for i18n definitions"]
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:516:531"
    node1 --> node2{"Did a parsing error occur?"}
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:516:520"
    node2 -->|"No"| node3{"Did an IO error occur?"}
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:525:529"
    node2 -->|"Yes"| node4["Raise parsing error with filename"]
    click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:521:523"
    node3 -->|"No"| node5["Return extracted definitions"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:531:531"
    node3 -->|"Yes"| node6["Raise IO error with filename"]
    click node6 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java:526:528"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Parse XML file for i18n definitions"]
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:516:531"
%%     node1 --> node2{"Did a parsing error occur?"}
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:516:520"
%%     node2 -->|"No"| node3{"Did an IO error occur?"}
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:525:529"
%%     node2 -->|"Yes"| node4["Raise parsing error with filename"]
%%     click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:521:523"
%%     node3 -->|"No"| node5["Return extracted definitions"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:531:531"
%%     node3 -->|"Yes"| node6["Raise IO error with filename"]
%%     click node6 openCode "<SwmPath>[tiles/…/xmlDefinition/I18nFactorySet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java)</SwmPath>:526:528"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="516">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="449:5:5" line-data="            xmlDefinitions = parseXmlFile(servletContext, filename, xmlDefinitions);">`parseXmlFile`</SwmToken>, after parsing, we handle any SAX or IO exceptions by logging and throwing a <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="521:5:5" line-data="            throw new DefinitionsFactoryException(">`DefinitionsFactoryException`</SwmToken>. If parsing succeeds, we return the updated definitions set.

```java
        } catch (SAXException ex) {
            if (log.isDebugEnabled()) {
                log.debug("Error while parsing file '" + filename + "'.");
                ex.printStackTrace();
            }
            throw new DefinitionsFactoryException(
                "Error while parsing file '" + filename + "'. " + ex.getMessage(),
                ex);

        } catch (IOException ex) {
            throw new DefinitionsFactoryException(
                "IO Error while parsing file '" + filename + "'. " + ex.getMessage(),
                ex);
        }

        return xmlDefinitions;
    }
```

---

</SwmSnippet>

## Merging and Inheritance Extension

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="372">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="321:5:5" line-data="    protected DefinitionsFactory createFactory(">`createFactory`</SwmToken>, after loading all the XML files, we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="372:3:3" line-data="        rootXmlConfig.extend(lastXmlFile);">`extend`</SwmToken> on the root config with the last found locale-specific config. This merges the definitions, so everything is in one place before resolving inheritance.

```java
        rootXmlConfig.extend(lastXmlFile);
```

---

</SwmSnippet>

## Definition Set Merging

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is child set provided?"}
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:94:95"
    node1 -->|"Yes"| node2["Process each definition in child set"]
    node1 -->|"No"| node5["End"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:95:95"
    subgraph loop1["For each definition in child set"]
      node2 --> node3{"Does definition with same name exist in
current set?"}
      click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:100:101"
      node3 -->|"Yes"| node4["Merge child definition into existing
definition"]
      click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:103:103"
      node3 -->|"No"| node6["Add child definition to current set"]
      click node6 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:106:106"
      node4 --> node2
      node6 --> node2
    end
    node2 --> node5["End"]
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java:96:107"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is child set provided?"}
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:94:95"
%%     node1 -->|"Yes"| node2["Process each definition in child set"]
%%     node1 -->|"No"| node5["End"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:95:95"
%%     subgraph loop1["For each definition in child set"]
%%       node2 --> node3{"Does definition with same name exist in
%% current set?"}
%%       click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:100:101"
%%       node3 -->|"Yes"| node4["Merge child definition into existing
%% definition"]
%%       click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:103:103"
%%       node3 -->|"No"| node6["Add child definition to current set"]
%%       click node6 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:106:106"
%%       node4 --> node2
%%       node6 --> node2
%%     end
%%     node2 --> node5["End"]
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinitionsSet.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java)</SwmPath>:96:107"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" line="92">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="92:5:5" line-data="  public void extend( XmlDefinitionsSet child )">`extend`</SwmToken> merges all definitions from the child set into the current set. If a definition already exists, it calls <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="103:3:3" line-data="        parentInstance.overload( childInstance );">`overload`</SwmToken> to update it with the child's values. Otherwise, it just adds the new definition.

```java
  public void extend( XmlDefinitionsSet child )
    {
    if(child==null)
      return;
    Iterator i = child.getDefinitions().values().iterator();
    while( i.hasNext() )
      {
      XmlDefinition childInstance = (XmlDefinition)i.next();
      XmlDefinition parentInstance = getDefinition(childInstance.getName() );
      if( parentInstance != null )
        {
        parentInstance.overload( childInstance );
        }
       else
        putDefinition( childInstance );
      } // end loop
    }
```

---

</SwmSnippet>

## Definition Attribute Overriding

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Update definition from child"] --> node2{"Does child provide new path,
inheritance, role, or controller?"}
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:172:173"
    node2 -->|"Yes"| node3["Update properties: path, inheritance,
role, controller"]
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:174:190"
    node2 -->|"No"| node4["Keep existing properties"]
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:174:190"
    click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:174:190"
    node3 --> node5["Attribute Copying and Map Compatibility"]
    node4 --> node5
    

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node5 goToHeading "Attribute Copying and Map Compatibility"
node5:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Update definition from child"] --> node2{"Does child provide new path,
%% inheritance, role, or controller?"}
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:172:173"
%%     node2 -->|"Yes"| node3["Update properties: path, inheritance,
%% role, controller"]
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:174:190"
%%     node2 -->|"No"| node4["Keep existing properties"]
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:174:190"
%%     click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:174:190"
%%     node3 --> node5["Attribute Copying and Map Compatibility"]
%%     node4 --> node5
%%     
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node5 goToHeading "Attribute Copying and Map Compatibility"
%% node5:::HeadingStyle
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="172">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="172:5:5" line-data="  public void overload( XmlDefinition child )">`overload`</SwmToken>, we update the parent definition with any non-null fields from the child, like path, role, and controller. This ensures the parent reflects any overrides from the child. Next, we need to handle controller instantiation, so we call `InsertTag.getController`.

```java
  public void overload( XmlDefinition child )
    {
    if( child.getPath() != null )
      {
      path = child.getPath();
      }
    if( child.getExtends() != null )
      {
      inherit = child.getExtends();
      }
    if( child.getRole() != null )
      {
      role = child.getRole();
      }
    if( child.getController()!=null )
      {
      controller = child.getController();
      controllerType =  child.getControllerType();
      }
```

---

</SwmSnippet>

### Controller Instantiation Logic

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" line="400">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="400:5:5" line-data="    private Controller getController() throws JspException {">`getController`</SwmToken> checks if a <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="401:4:4" line-data="        if (controllerType == null) {">`controllerType`</SwmToken> is set and then calls <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/taglib/InsertTag.java" pos="406:3:5" line-data="            return ComponentDefinition.createController(">`ComponentDefinition.createController`</SwmToken> to actually create the controller instance. This abstracts the instantiation logic and handles different controller types.

```java
    private Controller getController() throws JspException {
        if (controllerType == null) {
            return null;
        }

        try {
            return ComponentDefinition.createController(
                controllerName,
                controllerType);

        } catch (InstantiationException ex) {
            throw new JspException(ex);
        }
    }
```

---

</SwmSnippet>

### Controller Creation and Fallbacks

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Request to create controller (name,
controllerType)"] --> node2{"Is controllerType specified?"}
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:481:490"
    node2 -->|"No"| node3{"Can create controller from class name?"}
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:490:491"
    node3 -->|"Yes"| node4["Return controller created from class
name"]
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:491:493"
    node3 -->|"No"| node5["Return URL controller (using name)"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:495:496"
    node2 -->|"Yes"| node6{"Is controllerType 'url'?"}
    click node6 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:498:499"
    node6 -->|"Yes"| node5
    node6 -->|"No"| node7{"Is controllerType 'classname'?"}
    click node7 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:501:502"
    node7 -->|"Yes"| node4
    node7 -->|"No"| node8["Return null (should not occur in normal
usage)"]
    click node8 openCode "tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java:504:505"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Request to create controller (name,
%% <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="189:1:1" line-data="      controllerType =  child.getControllerType();">`controllerType`</SwmToken>)"] --> node2{"Is <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="189:1:1" line-data="      controllerType =  child.getControllerType();">`controllerType`</SwmToken> specified?"}
%%     click node1 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:481:490"
%%     node2 -->|"No"| node3{"Can create controller from class name?"}
%%     click node2 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:490:491"
%%     node3 -->|"Yes"| node4["Return controller created from class
%% name"]
%%     click node3 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:491:493"
%%     node3 -->|"No"| node5["Return URL controller (using name)"]
%%     click node5 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:495:496"
%%     node2 -->|"Yes"| node6{"Is <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="189:1:1" line-data="      controllerType =  child.getControllerType();">`controllerType`</SwmToken> 'url'?"}
%%     click node6 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:498:499"
%%     node6 -->|"Yes"| node5
%%     node6 -->|"No"| node7{"Is <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="189:1:1" line-data="      controllerType =  child.getControllerType();">`controllerType`</SwmToken> 'classname'?"}
%%     click node7 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:501:502"
%%     node7 -->|"Yes"| node4
%%     node7 -->|"No"| node8["Return null (should not occur in normal
%% usage)"]
%%     click node8 openCode "<SwmPath>[tiles/…/tiles/ComponentDefinition.java](tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java)</SwmPath>:504:505"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" line="481">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" pos="481:7:7" line-data="    public static Controller createController(String name, String controllerType)">`createController`</SwmToken> checks the <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" pos="481:16:16" line-data="    public static Controller createController(String name, String controllerType)">`controllerType`</SwmToken> and either instantiates a controller by classname, creates a <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" pos="495:7:7" line-data="                controller = new UrlController(name);">`UrlController`</SwmToken>, or falls back if the type is unknown. If <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" pos="481:16:16" line-data="    public static Controller createController(String name, String controllerType)">`controllerType`</SwmToken> is null, it tries classname first, then falls back to <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/ComponentDefinition.java" pos="495:7:7" line-data="                controller = new UrlController(name);">`UrlController`</SwmToken> if that fails.

```java
    public static Controller createController(String name, String controllerType)
        throws InstantiationException {

        if (log.isDebugEnabled()) {
            log.debug("Create controller name=" + name + ", type=" + controllerType);
        }

        Controller controller = null;

        if (controllerType == null) { // first try as a classname
            try {
                return createControllerFromClassname(name);

            } catch (InstantiationException ex) { // ok, try something else
                controller = new UrlController(name);
            }

        } else if ("url".equalsIgnoreCase(controllerType)) {
            controller = new UrlController(name);

        } else if ("classname".equalsIgnoreCase(controllerType)) {
            controller = createControllerFromClassname(name);
        }

        return controller;
    }
```

---

</SwmSnippet>

### Class-Based Controller Instantiation

See <SwmLink doc-title="Creating Controllers and Dynamic Form Beans">[Creating Controllers and Dynamic Form Beans](/.swm/creating-controllers-and-dynamic-form-beans.6s9vsfer.sw.md)</SwmLink>

### Attribute Copying and Map Compatibility

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="191">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="81:1:1" line-data="      XmlDefinition definition = (XmlDefinition)i.next();">`XmlDefinition`</SwmToken>, after handling controller logic, we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="192:3:3" line-data="    attributes.putAll( child.getAttributes());">`putAll`</SwmToken> to copy all child attributes into the parent. If the attributes map doesn't support this, it throws an exception, which means not all map implementations are compatible here.

```java
      // put all child attributes in parent.
    attributes.putAll( child.getAttributes());
    }
```

---

</SwmSnippet>

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" line="236">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="236:5:5" line-data="    public void putAll(Map map) {">`putAll`</SwmToken> just throws <SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="238:5:5" line-data="        throw new UnsupportedOperationException();">`UnsupportedOperationException`</SwmToken>. This means the map can't be modified, and any attempt to copy entries into it will fail. It's a read-only or unsupported operation by design.

```java
    public void putAll(Map map) {

        throw new UnsupportedOperationException();

    }
```

---

</SwmSnippet>

## Inheritance Resolution and Factory Creation

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="373">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="321:5:5" line-data="    protected DefinitionsFactory createFactory(">`createFactory`</SwmToken>, after merging all the configs, we call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="373:3:3" line-data="        rootXmlConfig.resolveInheritances();">`resolveInheritances`</SwmToken> on the root config. This walks through all definitions and resolves any parent-child relationships, so the final config is ready for use.

```java
        rootXmlConfig.resolveInheritances();

```

---

</SwmSnippet>

## Inheritance Chain Resolution

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" line="75">

---

<SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="75:5:5" line-data="  public void resolveInheritances() throws NoSuchDefinitionException">`resolveInheritances`</SwmToken> loops through all definitions and calls <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="82:3:3" line-data="      definition.resolveInheritance( this );">`resolveInheritance`</SwmToken> on each one. This makes sure every definition gets its inherited attributes from its parent, if any.

```java
  public void resolveInheritances() throws NoSuchDefinitionException
    {
      // Walk through all definitions and resolve individual inheritance
    Iterator i = definitions.values().iterator();
    while( i.hasNext() )
      {
      XmlDefinition definition = (XmlDefinition)i.next();
      definition.resolveInheritance( this );
      }  // end loop
    }
```

---

</SwmSnippet>

## Parent Attribute Inheritance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1{"Is inheritance already resolved or not
needed?"}
    click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:119:120"
    node1 -->|"Yes"| node5["Finish: No inheritance needed"]
    click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:120:120"
    node1 -->|"No"| node2{"Is parent definition available?"}
    click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:130:140"
    node2 -->|"No"| node5
    node2 -->|"Yes"| node3["Resolve parent inheritance first"]
    click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:142:142"
    node3 --> node4["Redirect Path Resolution"]
    
    node4 --> node5

    subgraph loop1["For each attribute in parent"]
        node4 --> node6{"Is attribute already defined in child?"}
        
        node6 -->|"No"| node7["Add attribute to child"]
        click node7 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:150:150"
        node6 -->|"Yes"| node4
    end

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node6 goToHeading "Attribute Presence Check"
node6:::HeadingStyle
click node4 goToHeading "Redirect Path Resolution"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1{"Is inheritance already resolved or not
%% needed?"}
%%     click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:119:120"
%%     node1 -->|"Yes"| node5["Finish: No inheritance needed"]
%%     click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:120:120"
%%     node1 -->|"No"| node2{"Is parent definition available?"}
%%     click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:130:140"
%%     node2 -->|"No"| node5
%%     node2 -->|"Yes"| node3["Resolve parent inheritance first"]
%%     click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:142:142"
%%     node3 --> node4["Redirect Path Resolution"]
%%     
%%     node4 --> node5
%% 
%%     subgraph loop1["For each attribute in parent"]
%%         node4 --> node6{"Is attribute already defined in child?"}
%%         
%%         node6 -->|"No"| node7["Add attribute to child"]
%%         click node7 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:150:150"
%%         node6 -->|"Yes"| node4
%%     end
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node6 goToHeading "Attribute Presence Check"
%% node6:::HeadingStyle
%% click node4 goToHeading "Redirect Path Resolution"
%% node4:::HeadingStyle
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="115">

---

In <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="115:5:5" line-data="  public void resolveInheritance( XmlDefinitionsSet definitionsSet )">`resolveInheritance`</SwmToken>, after making sure the parent is resolved, we loop over the parent's attribute keys to copy any missing attributes into the child. This means we need to call <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="145:13:13" line-data="    Iterator parentAttributes = parent.getAttributes().keySet().iterator();">`keySet`</SwmToken> on the parent's attributes map, which could throw if not supported.

```java
  public void resolveInheritance( XmlDefinitionsSet definitionsSet )
    throws NoSuchDefinitionException
    {
      // Already done, or not needed ?
    if( isVisited || !isExtending() )
      return;

    if(log.isDebugEnabled())
      log.debug( "Resolve definition for child name='" + getName()
              + "' extends='" + getExtends() + "'.");

      // Set as visited to avoid endless recurisvity.
    setIsVisited( true );

      // Resolve parent before itself.
    XmlDefinition parent = definitionsSet.getDefinition( getExtends() );
    if( parent == null )
      { // error
      String msg = "Error while resolving definition inheritance: child '"
                           + getName() +    "' can't find its ancestor '"
                           + getExtends() +
                           "'. Please check your description file.";
      log.error( msg );
        // to do : find better exception
      throw new NoSuchDefinitionException( msg );
      }

    parent.resolveInheritance( definitionsSet );

      // Iterate on each parent's attribute and add it if not defined in child.
    Iterator parentAttributes = parent.getAttributes().keySet().iterator();
```

---

</SwmSnippet>

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" line="211">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="211:5:5" line-data="    public Set keySet() {">`keySet`</SwmToken> just throws <SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="213:5:5" line-data="        throw new UnsupportedOperationException();">`UnsupportedOperationException`</SwmToken>. So if code tries to iterate over the keys, it'll fail. This map isn't meant to be used like a normal map.

```java
    public Set keySet() {

        throw new UnsupportedOperationException();

    }
```

---

</SwmSnippet>

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="146">

---

Back in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinitionsSet.java" pos="81:1:1" line-data="      XmlDefinition definition = (XmlDefinition)i.next();">`XmlDefinition`</SwmToken>, after getting the parent's attribute keys, we check if each key is already present in the child before copying it. If the map doesn't support <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="149:9:9" line-data="      if( !getAttributes().containsKey(name) )">`containsKey`</SwmToken>, this will throw, so not all map types are compatible here.

```java
    while( parentAttributes.hasNext() )
      {
      String name = (String)parentAttributes.next();
      if( !getAttributes().containsKey(name) )
        putAttribute( name, parent.getAttribute(name) );
      }
```

---

</SwmSnippet>

### Attribute Presence Check

<SwmSnippet path="/faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" line="107">

---

<SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="107:5:5" line-data="    public boolean containsKey(Object key) {">`containsKey`</SwmToken> checks if the key is present by calling <SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="112:4:6" line-data="            return (messages.isPresent(locale, key.toString()));">`messages.isPresent`</SwmToken> with the current locale and key. If the key is null, it just returns false.

```java
    public boolean containsKey(Object key) {

        if (key == null) {
            return (false);
        } else {
            return (messages.isPresent(locale, key.toString()));
        }

    }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/MessageResources.java" line="397">

---

<SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="397:5:5" line-data="    public boolean isPresent(Locale locale, String key) {">`isPresent`</SwmToken> checks if a message exists for a locale and key by calling <SwmToken path="core/src/main/java/org/apache/struts/util/MessageResources.java" pos="398:7:7" line-data="        String message = getMessage(locale, key);">`getMessage`</SwmToken>. If the result is null or wrapped in '???', it treats the message as missing. This '???' marker is a local convention, not standard, and directly affects how presence is determined.

```java
    public boolean isPresent(Locale locale, String key) {
        String message = getMessage(locale, key);

        if (message == null) {
            return false;
        } else if (message.startsWith("???") && message.endsWith("???")) {
            return false; // FIXME - Only valid for default implementation
        } else {
            return true;
        }
    }
```

---

</SwmSnippet>

### Parent Path and Role Assignment

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="152">

---

After checking attribute presence in <SwmToken path="faces/src/main/java/org/apache/struts/faces/util/MessagesMap.java" pos="41:4:4" line-data="public class MessagesMap implements Map {">`MessagesMap`</SwmToken>, `XmlDefinition.resolveInheritance` assigns path and role from the parent if they're not set in the child. This ensures the child inherits routing and access control info. Next, we need to call ActionRedirect.getPath to resolve any redirects or original paths for the definition.

```java
      // Set path and role if not setted
    if( path == null )
      setPath( parent.getPath() );
```

---

</SwmSnippet>

### Redirect Path Resolution

See <SwmLink doc-title="Redirect URL Construction">[Redirect URL Construction](/.swm/redirect-url-construction.dg7qb612.sw.md)</SwmLink>

### Parent Controller and Role Inheritance

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1{"Is access role missing?"}
  click node1 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:155:156"
  node1 -->|"Yes"| node2["Inherit access role from parent"]
  click node2 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:156:156"
  node1 -->|"No"| node3{"Is controller logic missing?"}
  click node3 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:157:161"
  node2 --> node3
  node3 -->|"Yes"| node4["Inherit controller and controller type
from parent"]
  click node4 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:159:161"
  node3 -->|"No"| node5["All properties present"]
  click node5 openCode "tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java:162:162"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1{"Is access role missing?"}
%%   click node1 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:155:156"
%%   node1 -->|"Yes"| node2["Inherit access role from parent"]
%%   click node2 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:156:156"
%%   node1 -->|"No"| node3{"Is controller logic missing?"}
%%   click node3 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:157:161"
%%   node2 --> node3
%%   node3 -->|"Yes"| node4["Inherit controller and controller type
%% from parent"]
%%   click node4 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:159:161"
%%   node3 -->|"No"| node5["All properties present"]
%%   click node5 openCode "<SwmPath>[tiles/…/xmlDefinition/XmlDefinition.java](tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java)</SwmPath>:162:162"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" line="155">

---

Back from ActionRedirect, `XmlDefinition.resolveInheritance` copies controller and <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/XmlDefinition.java" pos="189:1:1" line-data="      controllerType =  child.getControllerType();">`controllerType`</SwmToken> from the parent if they're missing in the child. Role is also inherited if not set. This ensures the child definition has all necessary action handling and access control info.

```java
    if( role == null )
      setRole( parent.getRole() );
    if( controller==null )
      {
      setController( parent.getController());
      setControllerType( parent.getControllerType());
      }
    }
```

---

</SwmSnippet>

## Factory Instantiation and Caching

<SwmSnippet path="/tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" line="375">

---

After resolving inheritances in <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="337:1:1" line-data="        XmlDefinitionsSet lastXmlFile = null;">`XmlDefinitionsSet`</SwmToken>, `I18nFactorySet.createFactory` creates a new <SwmToken path="tiles/src/main/java/org/apache/struts/tiles/xmlDefinition/I18nFactorySet.java" pos="375:7:7" line-data="        factory = new DefinitionsFactory(rootXmlConfig);">`DefinitionsFactory`</SwmToken> with the merged config, caches it for the locale suffix, and returns it. The factory selection algorithm supports layered fallback and locale-specific loading by iterating suffixes, merging configs, and resolving inheritance before instantiation.

```java
        factory = new DefinitionsFactory(rootXmlConfig);
        loaded.put(lastPostfix, factory);

        if (log.isDebugEnabled()) {
            log.debug("factory loaded : " + factory);
        }

        // return last available found !
        return factory;
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
