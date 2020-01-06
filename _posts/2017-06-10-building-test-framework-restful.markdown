---
title:  "14 - Building Test framework for Restful Web Application"
date:   2017-06-10 11:30:23
categories: [Development]
tags: [rest-ws]
reading_time: 10 min
---

**To be discussed**

1. Configuring Integrated Test case for Restful Application using Grizzly2
2. Configuring Integrated Test case for servlet based Restful Web Application using External Test Container
3. Running IntegratedTest cases using Maven
4. Running Unit Test case using Mockito and Maven

**Technology used**

 - Jersey 2.8
 - Spring 4.0
 - Maven 2
 - Junit 4
 - Mockito
 - Tomcat

Let's start from basic a bit...

To create a maven-jersey2-spring4- REST application

```java
mvn archetype:generate 
-DarchetypeArtifactId=jersey-quickstart-grizzly2 
-DarchetypeGroupId=org.glassfish.jersey.archetypes 
-DinteractiveMode=false -DgroupId=com.sample 
-DartifactId=jersey2-spring4-service 
-Dpackage=com.sample 
-DarchetypeVersion=2.23.2
```


To create a maven-jersey2-spring4- REST **WEB** application

```java
mvn archetype:generate 
-DarchetypeArtifactId=jersey-quickstart-webapp 
-DarchetypeGroupId=org.glassfish.jersey.archetypes 
-DinteractiveMode=false 
-DgroupId=com.example 
-DartifactId=webapp-jersey2-spring4-service 
-Dpackage=com.sample 
-DarchetypeVersion=2.23.2
```

> Difference between Jersey1.x and Jersey2.x
> 
> Jersey1.x has package com.sun.jersey and Jersey2.x has package org.glassfish.jersey, because Jersey team is now part of another organization (Glassfish). There is a long list of difference mentioned [here](https://java.net/jira/secure/ReleaseNote.jspa?projectId=10014&version=15271)

for sample Jersey 1.x application, check [this git hub repository](https://github.com/jrphub/webapp-spring4-jersey1)


**How Jersey Works?**

Jersey framework basically uses ServletContainer servlet to intercept all the incoming requests. As we configure in our projects web.xml, that all the incoming rest request should be handled by that servlet. There is an init-param that is configured with the jersey servlet to find your REST service classes. REST service classes are not Servlet and they need NOT to extend the HttpServlet. These REST service classes are simple POJOs annotated to tell the jersey framework about different properties such as path, consumes, produces etc. When you return from your service method, jersey takes care of marshalling those objects in the defined  'PRODUCES' responseType and write it on the client stream. Here is a sample of jersey config in web.xml

Jersey 1.x

```xml
<servlet>
<servlet-name>Jersey 1.x REST Tutorials</servlet-name>
<servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>

<init-param>
<param-name>com.sun.jersey.config.property.packages</param-name>
<param-value>your application package </param-value>
</init-param>

<load-on-startup>1</load-on-startup>
</servlet>
```

Jersey 2.x 

```xml
<servlet>
<servlet-name>Jersey REST Tutorials</servlet-name>
<servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>

<init-param>
<param-name>jersey.config.server.provider.packages</param-name>
<param-value>your application package</param-value>
</init-param>

<load-on-startup>1</load-on-startup>
</servlet>
```

This is one good image explaining Servlet invocation in web application.

![enter image description here](https://i.imgur.com/aVJdrER.png)

From the above picture, we understand all request/response are handled through servlet container. [Jersey Test framework](https://jersey.java.net/documentation/latest/test-framework.html) provides similar container (grizzly, in-memory, jdk, simple, jetty, external) for integration testing. Jersey Test also provides pre-configured client to access deployed application. We will use these concepts building our test framework for Restful web Application.

**Configuring Integrated Test case for Restful Web Application using Grizzly2**

Jersey provides 2 different test container factories based on Grizzly. The GrizzlyTestContainerFactory creates a container that can run as a light-weight, plain HTTP container. Almost all Jersey tests are using Grizzly HTTP test container factory. Second factory is GrizzlyWebTestContainerFactory that is Servlet-based and supports Servlet deployment context for tested applications. This factory can be useful when testing more complex Servlet-based application deployments.

```xml
<dependency>
    <groupId>org.glassfish.jersey.test-framework.providers</groupId>
    <artifactId>jersey-test-framework-provider-grizzly2</artifactId>
    <version>2.23.2</version>
</dependency>
```

Check this [github code repository](https://github.com/jrphub/webapp-spring4-jersey2) for implementing a simple Restful application with jUnit Test cases using Grizzly2.

For Simple servlet based application, you have to use Grizzly2 web container, by overriding configureDeployment() and getTestContainerFactory(). The below is just a sample code to give a brief idea.

```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = AppContextInitializer.class)
    @WebAppConfiguration
    public class DataSourceWSTest extends JerseyTest {
    
    	public ServletDeploymentContext configureDeployment() {
            return ServletDeploymentContext
                        .forServlet(new ServletContainer(new ResourceConfig(MainController.class))).build();
    } 
    @Override
        protected TestContainerFactory getTestContainerFactory() {
          return new GrizzlyWebTestContainerFactory();
        }
        @Override
    protected URI getBaseUri() {
        return UriBuilder.fromUri(super.getBaseUri()).path("applicationName").build();
    }
```


**Configuring Integrated Test Case for Servlet Based Restful Web Application**

For complex servlet based web application, Jersey Test framework provided External Container support.

To use this, add below in your pom.xml

```xml
<dependency>
  <groupId>org.glassfish.jersey.test-framework.providers</groupId>
  <artifactId>jersey-test-framework-provider-external</artifactId>
  <version>2.23.2</version>
</dependency>
```

Here is a sample code for Integration Test case using External Container


```java
import java.net.URI;

import javax.ws.rs.core.Application;
import javax.ws.rs.core.UriBuilder;

import org.glassfish.jersey.test.JerseyTest;
import org.glassfish.jersey.test.TestProperties;
import org.glassfish.jersey.test.external.ExternalTestContainerFactory;
import org.glassfish.jersey.test.spi.TestContainerFactory;

public class IntegrationTestHelper extends JerseyTest {

    /* Generated AUTH_KEY using username and password credential using postman client for authenticated application */
    
    protected final String AUTH_KEY = "Basic bWFwX3VzZXI6bWFwX3VzZXI=";

    @Override
    protected Application configure() {
        setTestProperties();
        return new Application(); // dummy Application instance for the test
                                  // framework - will not be used.
    }

    @Override
    protected TestContainerFactory getTestContainerFactory() {
        return new ExternalTestContainerFactory();
    }

    @Override
    protected URI getBaseUri() {
        return UriBuilder.fromUri(super.getBaseUri()).path("fae-mdp-web")
                .build();
    }

    protected void setTestProperties() {
        // Find first available port to run multiple test containers in parallel
        // to avoid TestContainerException: java.net.BindException: Address
        // already in use: bind
        forceSet(TestProperties.CONTAINER_PORT, "8080");

        enable(TestProperties.LOG_TRAFFIC);
        enable(TestProperties.DUMP_ENTITY);

    }

    protected void preTestCase(String testcaseName) {
        System.out.println("START : " + testcaseName);
    }

    protected void postTestCase(String testcaseName) {
        System.out.println("END : " + testcaseName);
    }

}
```
Your Test class

```java
import static org.junit.Assert.assertEquals;

import javax.ws.rs.core.Response;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;

import com.saama.mdp.test.common.IntegrationTestHelper;
import com.saama.mdp.web.conf.AppContextInitializer;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppContextInitializer.class)
@WebAppConfiguration
public class DataSourceWSIT extends IntegrationTestHelper {

    private final String DATASOURCE_URI = "api/v1/dev/datasource/cname/";

    @Test
    public void testGetById() {
        preTestCase("DataSourceWSIT.testGetById");
        int id = 20;
        Response response = target(DATASOURCE_URI + id).request()
                .header("Authorization", AUTH_KEY).get();
        assertEquals(200, response.getStatus());
        postTestCase("DataSourceWSIT.testGetById");
    }

    @Test
    public void testGetDataSources() {
        preTestCase("DataSourceWSIT.testGetDataSources");
        Response response = target(DATASOURCE_URI).request()
                .header("Authorization", AUTH_KEY).get();
        assertEquals(200, response.getStatus());
        postTestCase("DataSourceWSIT.testGetDataSources");
    }
}
```

*In @ContextConfiguration, you can give the context xml file or the class name which configures the application

*for Servlet Context initialization in external container, You must use jersey 2.8 and also, add below in your pom.xml

```xml
<dependency> 		  
	<groupId>org.glassfish.jersey.bundles.repackaged</groupId>
	<artifactId>jersey-guava</artifactId>
	<version>2.8</version>
</dependency>
```

**Running Integration Test**

To run intgeration test cases, there are 2 ways
1. Using eclipse (Handy, but less popular)
2. Using Command line

To run in eclipse IDE, you need to run the test case in two steps:

Step1: Run the test case on "Run on server" on tomcat or any other application server

Step2: When Step1 is completed, Run the test case as "JUnit Test"

To run the test case on command or in Jenkin, we need to use embeded application server like Jetty or Tomcat. Here I will show using Tomcat as I had a very hard time to make my application up using Jetty.

To use embedded Tomcat application server,  we need to use tomcat7-maven-plugin. You can use tomcat6-maven-plugin also.

Add below in your pom.xml

```xml
<plugin>
	<groupId>org.apache.tomcat.maven</groupId>
	<artifactId>tomcat7-maven-plugin</artifactId>
	<version>2.2</version>
	<configuration>
		<url>http://si-vm-201:8080/manager/text</url>
		<username>tomcat</username>
		<password>passwd</password>
		<skip>${skipITs}</skip>
	</configuration>
	<executions>
	  <execution>
		<id>start-tomcat</id>
		<phase>pre-integration-test</phase>
		<goals>
		  <goal>run</goal>
		</goals>
		<configuration>
		  <fork>true</fork>
		</configuration>
	  </execution>
	  <execution>
		<id>stop-tomcat</id>
		<phase>post-integration-test</phase>
		<goals>
		  <goal>shutdown</goal>
		</goals>
	  </execution>
	</executions>
</plugin>
```

For integration test case, we need to used another plugin called "maven-failsafe-plugin"

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-failsafe-plugin</artifactId>
  <version>2.19.1</version>
  <configuration>
	  <parallel>methods</parallel>
	  <threadCount>10</threadCount>
	  <skipITs>${skipITs}</skipITs>
  </configuration>
  <executions>
	  <execution>
		  <id>integration-test</id>
		  <goals>
			<goal>integration-test</goal>
		  </goals>
	  </execution>
	  <execution>
		  <id>verify</id>
		  <goals>
			<goal>verify</goal>
		  </goals>
	  </execution>
  </executions>
</plugin>
```

Now let's understand, what we have done so far...

We have added failsafe plugin for running integration test case and tomcat plugin to be used as embedded application server where our application will be deployed and test case requests will be served.

When we do, 

    mvn clean install

in command line, 

maven runs below life cycle phases in sequence

    validate
    initialize
    generate-sources
    process-sources
    generate-resources
    process-resources
    compile
    process-classes
    generate-test-sources
    process-test-sources
    generate-test-resources
    process-test-resources
    test-compile
    process-test-classes
    test
    prepare-package
    package
    pre-integration-test
    integration-test
    post-integration-test
    verify
    install

This shows, install phase runs all goals like test (for unit test using surefire plugin), pre-integration-test (Tomcat start), integration-test(test cases), post-integration-test(tomcat shutdown), verify (checks test cases are passed or failed), install (build successfully or not)

Here just one command is sufficient to run integration test cases, where tomcat starts, application gets deployed, integration test cases are run, tomcat stops and build gets completed.

Naming your test cases:

For Unit Test cases:

    *Test*.java
    *Test.java
    *TestCase.java

For Integration Test cases:

    IT*.java
    *IT.java
    *ITCase.java

**Running unit test cases with Mockito**
This is standard jUnit test cases irrespective of Restful Application.

To mock external sources, add this in your pom.xml

```xml
<dependency>
	<groupId>org.mockito</groupId>
	<artifactId>mockito-core</artifactId>
	<version>1.10.19</version>
</dependency>
```


You need to use surefire plugin for running your unit test case.

Add below in your pom.xml

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.12.4</version>
    <configuration>
        <parallel>methods</parallel>
        <threadCount>10</threadCount>
        <skipTests>${skipTests}</skipTests>
        <excludes>
            <exclude>**/*IT.java</exclude>
        </excludes>
    </configuration>
</plugin>
```


Here, I have enabled, parallel processing of unit test cases up to 10, skipped integration test cases as we are targeting only unit test case in this goal.

```java
mvn test
```

This command runs unit test cases using maven surefire plugin.

[This](https://springframework.guru/mocking-unit-tests-mockito/) is a simple article to learn using Mockito.

Enjoy!!!

**Reference**

 1. https://www.petrikainulainen.net/programming/maven/integration-testing-with-maven/
 2. http://cupofjava.de/blog/2013/02/05/integration-tests-with-maven-and-tomcat/
 3. https://github.com/Kalimaha/maven-spring-jersey-example
 4. https://github.com/thrawn01/Spring-Jersey-Hibernate-DbUnit-Example
 5. https://github.com/janusdn/jersey2-spring4-grizzly2
 6. https://github.com/alesaudate/kickstart-springjerseyhibernate
 7. http://stackoverflow.com/questions/27430052/jersey-how-to-mock-service/27447345#27447345
 8. http://stackoverflow.com/questions/27590896/how-to-use-mockito-for-testing-database-connection/27596173#27596173
 9. http://stackoverflow.com/questions/10607168/unit-testing-with-spring-and-the-jersey-test-framework
 10. https://blog.codecentric.de/en/2012/05/writing-lightweight-rest-integration-tests-with-the-jersey-test-framework/

 
