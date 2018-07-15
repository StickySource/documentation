Getting Started
===============

StickyCode provides an abstraction for build an Dependency Injection context. Currently Guice and Spring are supported.

Let StickyCode take care of the glue transparently, so you can focus on writing components.

An example main class::

  public static void main(String[] args) {
    StickyBootstrap b = StickyBootstrap.crank() (1)
      .scan("com.example"); (2)
      
    b.start(); (3)

    Runtime.getRuntime().addShutdownHook(new Thread(new Runnable() { (4)

      @Override
      public void run() {
        b.shutdown(); (5)
      }
    }));
  }

#. Initiate a bootstrap context
#. scan the net.stickycode and com.example packages
#. Start the underlying dependency injection context
#. Add a shutdown thread that will shutdown the context on VM shutdown
#. Invoke shutdown of the bootstrap when the VM shuts down

Guice
-----

Add the dependencies for maven::
  
  <!-- This is for configuration resolution -->
  <dependency>
    <groupId>net.stickycode.configuration</groupId>
    <artifactId>sticky-configuration</artifactId>
    <version>[2.5]</version>
  </dependency>

  <!-- This is for the guice4 binding -->
  <dependency>
    <groupId>net.stickycode.configured</groupId>
    <artifactId>sticky-configured-guice4</artifactId>
    <version>[1.6]</version>
  </dependency>

Note that fixed ranges are used, this causes Maven to fail if you introduce conflicts rather than behaving non-deterministically

Spring 4
--------

Add the dependencies for maven::
  
  <!-- This is for the spring4 binding -->
  <dependency>
    <groupId>net.stickycode.configured</groupId>
    <artifactId>sticky-configured-spring4</artifactId>
    <version>[1.3]</version>
  </dependency>
  
  <!-- This is for configuration resolution -->
  <dependency>
    <groupId>net.stickycode.configuration</groupId>
    <artifactId>sticky-configuration</artifactId>
    <version>[2.5]</version>
  </dependency>

Note that fixed ranges are used, this causes Maven to fail if you introduce conflicts rather than behaving non-deterministically
  
