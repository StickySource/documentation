Shifty Maven Plugin
-------------------

A maven plugin to download and maybe unpack a set of artifacts

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

