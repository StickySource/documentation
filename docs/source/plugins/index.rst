Plugins
===========

Shifty Maven Plugin
-------------------

A maven plugin to download and/or unpack a set of artifacts

Similar to the dependency plugin but 

* efficiently leveraging metadata to be fast 
* using version ranges for fluidity
* using parallel execution where possible

Usage
~~~~~

Fetching a dependency, this will download sticky-coercion-2.7.jar to target/shifty/::
    
     <plugin>
        <groupId>net.stickycode.plugins</groupId>
        <artifactId>shifty-maven-plugin</artifactId>
        <version>1.2</version>
        <executions>
          <execution>
            <id>test</id>
            <phase>validate</phase>
            <goals>
              <goal>fetch</goal>
            </goals>
            <configuration>
              <artifacts>
                <artifact>net.stickycode:sticky-coercion:[2.1,3)</artifact>
              </artifacts>
            </configuration>
          </execution>
        </executions>
      </plugin>

      
Unpack a zip/jar into target/shifty::

     <plugin>
        <groupId>net.stickycode.plugins</groupId>
        <artifactId>shifty-maven-plugin</artifactId>
        <version>1.2</version>
        <executions>
          <execution>
            <id>test</id>
            <phase>validate</phase>
            <goals>
              <goal>fetch</goal>
            </goals>
            <configuration>
              <unpack>true</unpack>
              <artifacts>
                <artifact>net.stickycode:sticky-coercion:[2.1,3):sources:jar</artifact>
              </artifacts>
            </configuration>
          </execution>
        </executions>
      </plugin>
      
      
Unpack a zip/jar into target/other::

     <plugin>
        <groupId>net.stickycode.plugins</groupId>
        <artifactId>shifty-maven-plugin</artifactId>
        <version>1.2</version>
        <executions>
          <execution>
            <id>test</id>
            <phase>validate</phase>
            <goals>
              <goal>fetch</goal>
            </goals>
            <configuration>
              <outputDirectory>${project.build.directory}/other</outputDirectory>
              <unpack>true</unpack>
              <artifacts>
                <artifact>net.stickycode:sticky-coercion:[2.1,3):sources:jar</artifact>
              </artifacts>
            </configuration>
          </execution>
        </executions>
      </plugin>

How it works
~~~~~~~~~~~~

Its pretty straight forward, for gav net.stickycode:sticky-coercion:[2.1,3):

* [2.1,3) is first resolved, in this case to 2.7 via metadata 
* and the property sticky-coercion.version is set to 2.7
* the artifact sticky-coercion-2.7 is resolved and downloaded if needed
* the artifact is copied into the output directory

Note that a Maven property is set in case you need to reference the artifact after its resolved.

Happy Maven Plugin
------------------

Wait Maven Plugin
-----------------


Bounds Maven Plugin
-------------------

A maven plugin to update the lower bounds of ranges to reduce metadata downloads

Example delta
~~~~~~~~~~~~~

When you run bounds:update for a project that contains this::

      <plugin>
       <groupId>net.stickycode.composite</groupId>
       <artifactId>sticky-composite-logging-api</artifactId>
       <version>[2.3,3)</version>
      </plugin>

      
and the latest release of sticky-composite-logging-api is 2.4, then you will end up with::

     <plugin>
       <groupId>net.stickycode.composite</groupId>
       <artifactId>sticky-composite-logging-api</artifactId>
       <version>[2.4,3)</version>
      </plugin>

      
Usage
~~~~~

The plugin is in maven central so it should 'Just Work'.

Run the plugin from your Apache Maven project directory::

    mvn net.stickycode.plugins:bounds-maven-plugin:2.2:update


And your version ranges will have there lower bound updated to the latest released
artifact version.

If you want to include any SNAPSHOT references when calculating the lower bound, set the`includeSnapshots` property::

    -DincludeSnapshots


when calling `mvn`.

Update bounds during release
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To update the bounds during release you can do this::

    <pluginManagement>
     <plugins>

      <plugin>
       <groupId>net.stickycode.plugins</groupId>
       <artifactId>bounds-maven-plugin</artifactId>
       <version>3.3</version>
      </plugin>
      <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-release-plugin</artifactId>
       <version>2.2.2</version>
       <configuration>
         <preparationGoals>bounds:update enforcer:enforce clean verify</preparationGoals>
       </configuration>
      </plugin>
     </plugins>
    </pluginManagement>


Line endings
~~~~~~~~~~~~

You can specify the line separator used like so::

      <plugin>
       <groupId>net.stickycode.plugins</groupId>
       <artifactId>bounds-maven-plugin</artifactId>
       <version>3.3</version>
       <configuration>
        <lineSeparator>Unix</lineSeparator>
       </configuration>
      </plugin>


Extract Current Version
~~~~~~~~~~~~~~~~~~~~~~~

To get the current version of a library from a range use bounds:current-version, this will set the property *stickyCoercion.version* to the right 2.x version::

    <plugin>
      <plugin>
        <groupId>net.stickycode.plugins</groupId>
        <artifactId>bounds-maven-plugin</artifactId>
        <version>3.3</version>
        <executions>
          <execution>
            <goals>
              <goal>current-version</goal>
            </goals>
            <configuration>
              <stickyCoercion.version>net.stickycode:sticky-coercion:[2,3]</stickyCoercion.version>
            </configuration>
          </execution>
        </execution>
      </plugin>
    </plugin>


Releases
~~~~~~~~

Release 3.3

*  dependencies with classifiers were being ignored incorrectly

Release 3.2

* support for setting a property to the highest version in a range

Release 2.6

* added support for dependencyManagement - although I would suggest you never ever us it
* added support for version defined as properties - although again I would suggest you don't do that
* allow the line separator on rewrite to be configured (Mac, Unix Windows), useful when you define the line ending in your SCM and need re-generated poms to match

