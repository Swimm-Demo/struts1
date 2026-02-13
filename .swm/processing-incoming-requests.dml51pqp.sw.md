---
title: Processing incoming requests
---
This document explains how incoming requests are routed to the correct module and handled by the appropriate business logic. The flow enables modular and scalable processing of user actions, ensuring each request is managed according to its module configuration. The main steps are routing the request, setting up the module's processor, initializing modules and resources, and delegating the request for execution.

```mermaid
flowchart TD
  node1["Routing the incoming request to the correct module"]:::HeadingStyle
  click node1 goToHeading "Routing the incoming request to the correct module"
  node1 --> node2{"Processor exists for module?"}
  node2 -->|"Yes"| node3["Delegating request handling to the processor"]:::HeadingStyle
  click node3 goToHeading "Delegating request handling to the processor"
  node2 -->|"No"| node4["Creating and caching the module's processor"]:::HeadingStyle
  click node4 goToHeading "Creating and caching the module's processor"
  click node4 goToHeading "Initializing modules and their resources"
  node4 --> node3
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

# Where is this flow used?

This flow is used multiple times in the codebase as represented in the following diagram:

```mermaid
graph TD;
      dde35d78060f1afca84b5e9dedc002fc37669b07142e9d5a1f656107cc900aa0(core/…/action/ActionServlet.java::ActionServlet.doGet) --> 294cd284fa406b4fe4f160157c73b79eaa7835613a545efd883ce1f14dd1ca7e(core/…/action/ActionServlet.java::ActionServlet.process)

ec52fb7c28a6afaae4fd5937965ca776c6c12441d8a10e0b92b387b05c5c5cf5(core/…/action/ActionServlet.java::ActionServlet.doPost) --> 294cd284fa406b4fe4f160157c73b79eaa7835613a545efd883ce1f14dd1ca7e(core/…/action/ActionServlet.java::ActionServlet.process)


classDef mainFlowStyle color:#000000,fill:#7CB9F4
classDef rootsStyle color:#000000,fill:#00FFF4
classDef Style1 color:#000000,fill:#00FFAA
classDef Style2 color:#000000,fill:#FFFF00
classDef Style3 color:#000000,fill:#AA7CB9

%% Swimm:
%% graph TD;
%%       dde35d78060f1afca84b5e9dedc002fc37669b07142e9d5a1f656107cc900aa0(<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>::ActionServlet.doGet) --> 294cd284fa406b4fe4f160157c73b79eaa7835613a545efd883ce1f14dd1ca7e(<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>::ActionServlet.process)
%% 
%% ec52fb7c28a6afaae4fd5937965ca776c6c12441d8a10e0b92b387b05c5c5cf5(<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>::ActionServlet.doPost) --> 294cd284fa406b4fe4f160157c73b79eaa7835613a545efd883ce1f14dd1ca7e(<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>::ActionServlet.process)
%% 
%% 
%% classDef mainFlowStyle color:#000000,fill:#7CB9F4
%% classDef rootsStyle color:#000000,fill:#00FFF4
%% classDef Style1 color:#000000,fill:#00FFAA
%% classDef Style2 color:#000000,fill:#FFFF00
%% classDef Style3 color:#000000,fill:#AA7CB9
```

# Routing the incoming request to the correct module

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="1952">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1952:5:5" line-data="    protected void process(HttpServletRequest request,">`process`</SwmToken>, we start by picking the right module for the request and grabbing its config. Then we check if there's already a <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1959:1:1" line-data="        RequestProcessor processor = getProcessorForModule(config);">`RequestProcessor`</SwmToken> for this module. If not, we call <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1962:5:5" line-data="            processor = getRequestProcessor(config);">`getRequestProcessor`</SwmToken> to set one up, so the request gets handled with the right logic for its module.

```java
    protected void process(HttpServletRequest request,
        HttpServletResponse response)
        throws IOException, ServletException {
        ModuleUtils.getInstance().selectModule(request, getServletContext());

        ModuleConfig config = getModuleConfig(request);

        RequestProcessor processor = getProcessorForModule(config);

        if (processor == null) {
            processor = getRequestProcessor(config);
        }

```

---

</SwmSnippet>

## Creating and caching the module's processor

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Check if request processor exists for module"]
  click node1 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:610:612"
  node1 -->|"Exists"| node2["Return existing processor"]
  click node2 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:641:642"
  node1 -->|"Does not exist"| node3["Create new processor"]
  click node3 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:613:617"
  node3 --> node4["Initialize processor"]
  click node4 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:634:634"
  node4 --> node5{"Is processor the recommended type?"}
  click node5 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:628:632"
  node5 -->|"Yes"| node6["Register processor"]
  click node6 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:638:638"
  node5 -->|"No"| node7["Log warning, register processor"]
  click node7 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:629:638"
  node6 --> node8["Return processor"]
  click node8 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:641:642"
  node7 --> node8
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Check if request processor exists for module"]
%%   click node1 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:610:612"
%%   node1 -->|"Exists"| node2["Return existing processor"]
%%   click node2 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:641:642"
%%   node1 -->|"Does not exist"| node3["Create new processor"]
%%   click node3 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:613:617"
%%   node3 --> node4["Initialize processor"]
%%   click node4 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:634:634"
%%   node4 --> node5{"Is processor the recommended type?"}
%%   click node5 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:628:632"
%%   node5 -->|"Yes"| node6["Register processor"]
%%   click node6 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:638:638"
%%   node5 -->|"No"| node7["Log warning, register processor"]
%%   click node7 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:629:638"
%%   node6 --> node8["Return processor"]
%%   click node8 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:641:642"
%%   node7 --> node8
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="608">

---

<SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="608:7:7" line-data="    protected synchronized RequestProcessor getRequestProcessor(">`getRequestProcessor`</SwmToken> checks for a cached processor for the module. If it's missing, it instantiates the processor class from the module config, initializes it, logs a warning if it's the old type, and stores it in the servlet context for reuse. Calling init here sets up the processor with everything it needs before handling requests.

```java
    protected synchronized RequestProcessor getRequestProcessor(
        ModuleConfig config) throws ServletException {
        RequestProcessor processor = this.getProcessorForModule(config);

        if (processor == null) {
            try {
                processor =
                    (RequestProcessor) RequestUtils.applicationInstance(config.getControllerConfig()
                                                                              .getProcessorClass());
            } catch (Exception e) {
            	UnavailableException e2 = new UnavailableException(
                    "Cannot initialize RequestProcessor of class "
                    + config.getControllerConfig().getProcessorClass());
                e2.initCause(e);
                throw e2;
            }
            
            // Emit a warning to the log if the classic RequestProcessor is 
            // being used without composition. Hopefully developers will 
            // heed this message and make the upgrade.
            if (!(processor instanceof ComposableRequestProcessor)) {
                log.warn("Use of the classic RequestProcessor is not recommended. " +
                        "Please upgrade to the ComposableRequestProcessor to " +
                        "receive the advantage of modern enhancements and fixes.");
            }

            processor.init(this, config);

            String key = Globals.REQUEST_PROCESSOR_KEY + config.getPrefix();

            getServletContext().setAttribute(key, processor);
        }

        return (processor);
    }
```

---

</SwmSnippet>

## Initializing modules and their resources

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start Struts ActionServlet initialization"]
    click node1 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:339:346"
    node1 --> node2["Initialize core servlet and main module config"]
    click node2 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:347:357"
    node2 --> node3["Set up main module resources and freeze config"]
    click node3 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:358:366"
    node3 --> node4
    
    subgraph loop1["For each servlet init parameter"]
        node4{"Does parameter name start with core/…/struts/config?"}
        click node4 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:372:374"
        node4 -->|"Yes"| node5["Initialize module config for prefix"]
        click node5 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:376:380"
        node5 --> node6["Set up module resources and freeze config"]
        click node6 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:381:388"
        node6 --> node4
        node4 -->|"No"| node8["Continue to next parameter"]
        click node8 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:373:374"
    end
    node4 --> node7["All modules initialized"]
    click node7 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:391:394"
    node7 --> node9["Servlet ready for requests"]
    click node9 openCode "core/src/main/java/org/apache/struts/action/ActionServlet.java:394:408"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start Struts <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="400:14:14" line-data="            log.error(&quot;Unable to initialize Struts ActionServlet due to an &quot;">`ActionServlet`</SwmToken> initialization"]
%%     click node1 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:339:346"
%%     node1 --> node2["Initialize core servlet and main module config"]
%%     click node2 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:347:357"
%%     node2 --> node3["Set up main module resources and freeze config"]
%%     click node3 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:358:366"
%%     node3 --> node4
%%     
%%     subgraph loop1["For each servlet init parameter"]
%%         node4{"Does parameter name start with <SwmPath>[core/…/struts/config/](core/target/test-classes/org/apache/struts/config/)</SwmPath>?"}
%%         click node4 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:372:374"
%%         node4 -->|"Yes"| node5["Initialize module config for prefix"]
%%         click node5 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:376:380"
%%         node5 --> node6["Set up module resources and freeze config"]
%%         click node6 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:381:388"
%%         node6 --> node4
%%         node4 -->|"No"| node8["Continue to next parameter"]
%%         click node8 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:373:374"
%%     end
%%     node4 --> node7["All modules initialized"]
%%     click node7 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:391:394"
%%     node7 --> node9["Servlet ready for requests"]
%%     click node9 openCode "<SwmPath>[core/…/action/ActionServlet.java](core/src/main/java/org/apache/struts/action/ActionServlet.java)</SwmPath>:394:408"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="339">

---

In <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="339:5:5" line-data="    public void init() throws ServletException {">`init`</SwmToken>, we loop through servlet config parameters, picking out those with <SwmPath>[core/…/struts/config/](core/target/test-classes/org/apache/struts/config/)</SwmPath> to figure out module prefixes. For each, we initialize the module and its resources. This lets the servlet handle multiple modules based on config naming.

```java
    public void init() throws ServletException {
        final String configPrefix = "config/";
        final int configPrefixLength = configPrefix.length() - 1;

        // Wraps the entire initialization in a try/catch to better handle
        // unexpected exceptions and errors to provide better feedback
        // to the developer
        try {
            initInternal();
            initOther();
            initServlet();
            initChain();

            getServletContext().setAttribute(Globals.ACTION_SERVLET_KEY, this);
            initModuleConfigFactory();

            // Initialize modules as needed
            ModuleConfig moduleConfig = initModuleConfig("", config);

            initModuleMessageResources(moduleConfig);
            initModulePlugIns(moduleConfig);
            initModuleFormBeans(moduleConfig);
            initModuleForwards(moduleConfig);
            initModuleExceptionConfigs(moduleConfig);
            initModuleActions(moduleConfig);
            postProcessConfig(moduleConfig);
            moduleConfig.freeze();

            Enumeration names = getServletConfig().getInitParameterNames();

            while (names.hasMoreElements()) {
                String name = (String) names.nextElement();

                if (!name.startsWith(configPrefix)) {
                    continue;
                }

                String prefix = name.substring(configPrefixLength);

                moduleConfig =
                    initModuleConfig(prefix,
                        getServletConfig().getInitParameter(name));
                initModuleMessageResources(moduleConfig);
                initModulePlugIns(moduleConfig);
                initModuleFormBeans(moduleConfig);
                initModuleForwards(moduleConfig);
                initModuleExceptionConfigs(moduleConfig);
                initModuleActions(moduleConfig);
                postProcessConfig(moduleConfig);
                moduleConfig.freeze();
            }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="391">

---

After setting up modules and their prefixes, <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="339:5:5" line-data="    public void init() throws ServletException {">`init`</SwmToken> cleans up the config digester and handles errors by marking the servlet unavailable if anything goes wrong.

```java
            this.initModulePrefixes(this.getServletContext());

            this.destroyConfigDigester();
        } catch (UnavailableException ex) {
            throw ex;
        } catch (Throwable t) {
            // The follow error message is not retrieved from internal message
            // resources as they may not have been able to have been
            // initialized
            log.error("Unable to initialize Struts ActionServlet due to an "
                + "unexpected exception or error thrown, so marking the "
                + "servlet as unavailable.  Most likely, this is due to an "
                + "incorrect or missing library dependency.", t);
            UnavailableException t2 = new UnavailableException(t.getMessage());
            t2.initCause(t);
            throw t2;
        }
    }
```

---

</SwmSnippet>

## Delegating request handling to the processor

<SwmSnippet path="/core/src/main/java/org/apache/struts/action/ActionServlet.java" line="1965">

---

Back in ActionServlet.process, after getting the processor from <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="608:7:7" line-data="    protected synchronized RequestProcessor getRequestProcessor(">`getRequestProcessor`</SwmToken>, we hand off the request and response to <SwmToken path="core/src/main/java/org/apache/struts/action/ActionServlet.java" pos="1965:1:3" line-data="        processor.process(request, response);">`processor.process`</SwmToken>. This means the request is now handled by the module-specific processor we just set up or reused.

```java
        processor.process(request, response);
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
