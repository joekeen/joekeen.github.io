---
layout: post
title: In praise of frontend-maven-plugin
categories: maven
---

Here is an example of using [frontend-maven-plugin](https://github.com/eirslett/frontend-maven-plugin) to integrate a node app into the maven lifecycle and build with Maven.

Some explanation:
* There was no internet access, so we had to host the node and yarn executables in an Artifactory instance.
* We bind the script `run build` to the generate-resources lifecycle phase.
* The unit test script `int` is bound to the test phase.
* Lastly, the integration test script `e2e` is bound to the integreation-test phase.
* The node app can be started via `mvn frontend:yarn@start` which is bound to `yarn start`.

```xml
<plugin>
    <groupId>com.github.eirslett</groupId>
    <artifactId>frontend-maven-plugin</artifactId>
    <version>${frontend-maven-plugin.version}</version>
    <configuration>
        <nodeVersion>${node.version}</nodeVersion>
        <yarnVersion>${yarn.version}</yarnVersion>
        <workingDirectory>${project.basedir}</workingDirectory>
        <installDirectory>${project.build.directory}</installDirectory>
        <serverId>central</serverId>
        <nodeDownloadRoot>https://artifactory.company.au/artifactory/ext-releases-local/node/</nodeDownloadRoot>
        <yarnDownloadRoot>https://artifactory.company.au/artifactory/ext-releases-local/yarn/</yarnDownloadRoot>
    </configuration>
    <executions>
        <execution>
            <id>install-frontend-tools</id>
            <goals>
                <goal>install-node-and-yarn</goal>
            </goals>
        </execution>
        <execution>
            <id>yarn-config-strict-ssl</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <configuration>
                <arguments>config set strict-ssl false</arguments>
            </configuration>
        </execution>
        <execution>
            <id>yarn-install</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <!-- optional: the default phase is "generate-resources" -->
            <phase>generate-resources</phase>
            <configuration>
                <arguments>install</arguments>
                <npmRegistryURL>https://artifactory.company.au/artifactory/api/npm/npm-remote/</npmRegistryURL>
            </configuration>
        </execution>
        <execution>
            <id>build-frontend</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <!-- optional: the default phase is "generate-resources" -->
            <phase>generate-resources</phase>
            <configuration>
                <arguments>run build</arguments>
            </configuration>
        </execution>
        <execution>
            <id>test</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <phase>test</phase>
            <configuration>
                <arguments>int</arguments>
            </configuration>
        </execution>
        <execution>
            <id>integration-test</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <phase>integration-test</phase>
            <configuration>
                <arguments>e2e</arguments>
            </configuration>
        </execution>
        <!--call from command line with: mvn frontend:yarn@start-->
        <execution>
            <id>start</id>
            <goals>
                <goal>yarn</goal>
            </goals>
            <phase>none</phase>
            <configuration>
                <arguments>start</arguments>
            </configuration>
        </execution>
    </executions>
</plugin>

```
