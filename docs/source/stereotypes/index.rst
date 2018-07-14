Stereotypes
===========

In Component based programming the Stereotype annotation tells the Dependency Injection framework that a class is a component and an instance should be created for injection (in the **Context**)


StickyComponent
---------------

The base component to create singletons in the **Context**

This stereotype is a meta annotation and can be used to creation other marker stereotypes to make the code cleaner.

The current marker component stereotypes are Repository, Gateway, Mapper, Service

This will result in a bean called **component** in the DI Context::

  @StickyCompoent
  public class Component {
  }

To inject the value then you use an Injection marker::

  @Inject
  Component component;


StickyRepository
^^^^^^^^^^^^^^^^

Used for compoenents that represent repositories of data::

  @StickyRepository
  public void IssueRepository {
  
    Issue findAll() {
      ...
    }
  }

StickyGateway
^^^^^^^^^^^^^

Used for components that interface with external systems they act as a gateway

StickyMapper
^^^^^^^^^^^^

Used for components that transform data from one form to another

StickyService
^^^^^^^^^^^^^

Used to mark components that provide a set of business logic

StickyPlugin
------------

Another meta annotation for components that are intended to be used for multple values to be injected.

Guice requires special semantics for multiple injection i.e. into Lists and Sets::

  @StickyPlugin
  public class A implements Contract {
  }
  
  @StickyPlugin
  public class B implements Contract {
  }
  
  @StickyComponent
  public Class Bean {
    @Inject
    Set<Contract> values;
  }

StickyExtension
^^^^^^^^^^^^^^^

For components that are intended as additions to the system

StickyStrategy
^^^^^^^^^^^^^^

For components where you select one of the implementations that are available

StickyFramework
---------------

The StickyCode framework its self is based on components, for some DI systems these components but be initialised in a first pass.

All system components and annotatied as Framework components for this purpose

