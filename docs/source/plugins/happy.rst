Happy Maven Plugin
------------------

A maven plugin to collect application metadata and validate during delivery acceptance testing

When you have an environment made up of a number of applications having a quick derived test to validate the correct versions of applications are deployed is important

Usage
~~~~~

Collecting the metadata
^^^^^^^^^^^^^^^^^^^^^^^

During the build process this will record the application version and context path for later validation::

    <plugin>
      <groupId>net.stickycode.plugins</groupId>
      <artifactId>happy-maven-plugin</artifactId>
      <version>1.7</version>
      <executions>
      
        <execution>
          <id>collect-application-version</id>
          
          <goals>
            <goal>collect</goal> (1)
          </goals>
      
          <phase>compile</phase>
      
          <configuration>
            <contextPath>${context.path}/</contextPath> (2)
          </configuration>
        </execution>

      </executions>
    </plugin>

#. Invoke the collect goal
#. The path that will be validated

The record is kept in the jar file of the application - see https://github.com/StickySource/happy-maven-plugin/issues/1


Using the medadata
^^^^^^^^^^^^^^^^^^

This will collect all the version in the resolved classpath for the project and check the version url for them::

  <plugin>
    <groupId>net.stickycode.plugins</groupId>
    <artifactId>happy-maven-plugin</artifactId>
    <version>1.7</version>

    <executions>

      <execution>
        <id>validate-application-version</id>

        <goals>
          <goal>validate</goal>
        </goals>

        <phase>integration-test</phase>

        <configuration>
          <targetDomain>https://demo.exmaple.com</targetDomain>
          <retryDurationSeconds>120</retryDurationSeconds>
          <retryPeriodSeconds>3</retryPeriodSeconds>
        </configuration>
      </execution>

    </executions>
  </plugin>
  
For example
* If for example we had an application *demo* 
* with a context path *users* at version 1.7, 
* then a GET request would be made to `https://demo.example.com/users/version` 
* expecting demo-1.7 to be returned
* the request will be retried every 3 seconds
* the validation will fail if a successful call has not happened after 120 seconds

