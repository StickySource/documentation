Configured
==========

Introduction
------------

Configuration is something that is often an afterthought, its varies by developer to developer, project to project. 

The purpose of the ``@Configured`` annotation is to allow the developer to simply declare a field value as being source outside their control. 

This means the developer only needs to decide that they need a value and defer the resolution of it. It makes it an easy decision and encourages deliberate programming.

There are three kinds of configuration:

**Application**

  Configuration that is applied to components when assembled into an application. This configuration is constant across deployments and environments.
  
  Developers can choose these values and they cannot be overridden by environmental configuration
  
  For example a database component defines the database schema, that is common across environments but specific to an application.
  
**Environment**

  Configuration that is different across environment deployments, but may be common across applications. 
  
  This configuration should not be defined by the developer as there is no reasonable values they can choose.
  
  An example of this would be the size of the thread pool

**Default**
  
  Configuration that can be defined at development time, setting sensible values
  
  The value can be overridden at run time to allow for that fact that developers are not omniscient
  
  An example might be a timeout which can have a very sensible default at development time but due to environmental conditions or changes in third parties that make it invalid later on.

Lifecycle
----------

The lifecyle of configuration for a system fits into wiring like this

#. Construct
#. PostConstruct
#. Wiring Injection
#. Configuration resolution
#. BeforeConfiguration
#. For each configured component

  #. PreConfigured
  #. Configuration Injection
  #. PostConfigured
  
#. AfterConfiguration

Construct

  The DI container creates the component
  
PostConstruct

  The DI container invokes methods annotated with ``@PostConstruct``

Wiring Injection

  The DI container injects all fields marked with ``@Inject``
  
  I recommend using javax.inject and not framework annotations to avoid unnecessary dependencies
  
  Lots of frameworks include Spring do configuration at Injection time, but it violates the separation of concerns and fail fast paradigms.
  
BeforeConfiguration

  Hook for action before the configuration system is invoked, perhaps to affect the running of the configuration system

Configuration Resolution

  All configuration type information is derived and fails fast for anything that cannot be coerced

 All sources are merged and configuration values are resolved
  
PreConfigured

  Methods marked with ``@PreConfigured`` are invoked to prepare for configured fields, it was added for symmetry, not sure if its that useful
  
Configuration Injection

  Fields marked with ``@Configured`` are injected by the relevant Coercion
  
PostConfigured

  Methods marked with ``@PostConfigured`` are invoked to act on the inject configuration
  
  For example you might set a URI and then create a client for it
  
AfterConfiguration

  Once all method hooks are invoked and configured fields are injected

  




Usage
-----------

@Configured
^^^^^^^^^^^

The configured annotation is the key for the developer. It allows for a simple declaration that a field should be set externally.

In contrast to other frameworks the develoer is not allowed to choose a name for the configuration other than the component and field name. 

The reason being that the developer has already reasoned about those names, there is not need to add an indirection. 

In my experience you end up with large variation and confusion when the decisions about naming are declared at development time.::

  @StickyComponent
  public class Bean { (1)
  
    @Configured("Description of the configured field") (2)(3)
    Period cycle; (4)(5)(6)
    
    @Configured("The timeout for sending email") 
    Duration timeoutInSeconds = Duration.ofSeconds(10); (7)(8)
    
  }


#. The name of the component is **bean** 
#. The configuration annotation marked the field as injected by configuration
#. The description of the configuration, shown when there are failures in configuration or in exports describing the system
#. The type of the configured field, a Coercion is used to convert the Configuration value into this type, in this case a java.util.time.Period, 
#. Because this field does not have a value providing configuration at run time is mandatory.
#. The name of the field **cycle**, its combined to define the configuration lookup **bean.cycle** in properties format
#. The name of the timeout includes seconds, this convention is very useful for configurators to know the scale of the value.
#. The default value is defined using plain old java which makes unit test super easy and saves on indirection. This field does not need to be configured at runtime as it has a default

@PostConfigured
^^^^^^^^^^^^^^^

Method hook for the developer to execute code as part of the configuration lifecycle::

  @StickyComponent
  public class Bean { 
  
    @Configured("Description of the configured field") 
    URL url; 
    
    Client client;
    
    @PostConfigured (1)
    public void createClient() { (2)
      client = new Client(url); (3)
    }
    
  }

#. Post configured annotation declares the hook called **createClient**
#. The result is not used so void is fine, the hook can have any access modifier. Standard practice to to scope it for ease of unit testing.
#. The url is used to create the client object. The url value can be used freely without error checking because

  * the framework makes sure that there is a value
  * the url is typed so must be a value URL or whatever the type should be
  * sophisticated coercions can do extra checked to ensure the validaty as a cross cutting concern saving the developers cognitive load to just the business value they are adding

@AfterConfiguration
^^^^^^^^^^^^^^^^^^^^

Method hook for the developer to execute code after everything is configured::

  @StickyComponent
  public class Bean {
  
    @Inject
    ConfigurationSytem system;
    
    @AfterConfiguration
    public void showConfiguration() { 
      log.info("configuration {}", system.exportTree());
    }
    
  }
  
@BeforeConfiguration
^^^^^^^^^^^^^^^^^^^^

Method hook for the developer to execute code as part of the configuration lifecycle::

  @StickyComponent
  public class Bean {
    
    @BeforeConfiguration
    public void example() { 
      // contrived example to show off before configuration
    }
    
  }
  
@PreConfigured
^^^^^^^^^^^^^^^

Method hook for the developer to execute code as part of the configuration lifecycle::

  @StickyComponent
  public class Bean {
    
    @PreConfigured (1)
    public void resetSomething() { (2)
      // contrived example to show off pre configuration
    }
    
  }
  

Configuration
^^^^^^^^^^^^^

The configuration system collects all the defined sources of configuration together, the default configuration sources are

* Application - loads from **META-INF/sticky/application.properties** in the form **bean.field**
* File - loads a property file based on the system property **configuration.path**
* System - load system properties in the form **bean.field**
* Environment - loads environment properties in the form **BEAN_FIELD**
* Default - loads a properties file from **META-INF/sticky/defaults.properties**

The resolution builds the list of values for each field, with different sources having precedence.::

  Application > File > System > Environment > Default

Resolution can be nested for example given some configuration sources


application.properties::

  application-name=MyApp
  
System properties::

  environment-colour=blue

File::

  aBean.field=${some.other.value}
  some.other.value=found it
  
  anotherBean.veryNested=${colour.${environment-colour}}


defaults.properties::

  colour.blue=azul
  colour.yellow=amarillo

This will result in aBean.field having value **found it** and anotherBean.veryNested having value **azul**

Coercions
---------

.. toctree::
   :maxdepth: 4
   
   coercions





