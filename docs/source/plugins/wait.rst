Wait Maven Plugin
-----------------

A maven plugin to halt the build process and wait for the user

This is useful to pause after integration test are run before they are verified to interact with the system manually


Add the plugin execution like so::

    <plugin>
      <groupId>net.stickycode.plugins</groupId>
      <artifactId>wait-maven-plugin</artifactId>
      <version>1.5</version>
      <executions>
        <execution>
          <phase>post-integration-test</phase>
          <goals>
            <goal>wait</goal>
          </goals>
          <configuration>
            <promptMessage>${project.name} is now running on http://localhost:${port}${context}/,
            I'll wait while you do stuff, when you are finished let me know by hitting enter or ctrl-c
            </promptMessage>
          </configuration>
        </execution>
      </executions>
    </plugin>
      
And then when you are running a test running::

    mvn clean verify -Dwait=true
    
If you prefer to use a profile::

  <profiles>
    <profile>
      <id>webapp-interactive</id>
      <build>
        <properties>
          <wait>true</wait>
        </properties>
      </build>
    </profile>
  </profiles>
