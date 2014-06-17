# Varrow - Spring MVC and Force.com Template

Salesforce-connected web app template utilizing Java Spring and Maven.

Optimized for Varrow's development team with the code from:

* [https://github.com/jamesward/hello-java-spring-force_dot_com](https://github.com/jamesward/hello-java-spring-force_dot_com)
* [https://github.com/ryanbrainard/richsobjects](https://github.com/ryanbrainard/richsobjects)

This application can be run on:

* Your local machine, through the command line or eclipse
* Heroku
* Cloud Foundry

## Requirements:

* [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) - WILL NOT WORK WITH JDK 8
* [Apache Maven](http://maven.apache.org/download.cgi)
* [git command line tools](http://git-scm.com/downloads)
* [Heroku Toolbelt](https://toolbelt.heroku.com/) and account: `$ heroku login`
* [Cloud Foundry command line tools](https://github.com/cloudfoundry/cli/releases) and account: `$ cf login`

## Clone this app locally:

`$ git clone https://github.com/forgreed/varrow-java-spring-sforce.git`  
'$ cd arrow-java-spring-sforce'

## Running the app locally

1. Setup OAuth Remote Access in Salesforce.com

    1. Go to Salesforce.com's Setup page
    2. Go to Create -> Apps
    3. Add a new Connected App with a URL of: `http://localhost:8080/_auth`
        - This can be reused for multiple oauth2 apps

2. Add environment variables for authenticating to Salesforce.com obtained from the Connected App detail page:

    * Linux/Mac:

              $ export OAUTH_CLIENT_KEY='Key without Quotes'  
              $ export OAUTH_CLIENT_SECRET='Secret without Quotes'   

    * Windows:

            $ set OAUTH_CLIENT_KEY='Key without Quotes'  
            $ set OAUTH_CLIENT_SECRET='Secret without Quotes'  

3. Build with:

    `$ mvn clean install`  

4. Then run it with:

    `$ java -jar target/dependency/webapp-runner.jar target/varrow-java-spring-sforce-0.0.1`  

5. After first build, simplify testing with:

    `$ mvn install && java -jar target/dependency/webapp-runner.jar target/varrow-java-spring-sforce-0.0.1`  

## Running on Heroku

1. Clone this project locally:

    `$ git clone https://github.com/forgreed/varrow-java-spring-sforce.git`  

2. Create a new app on Heroku

    `$ heroku create anyNameYouLike`  

3. Setup OAuth Remote Access in Salesforce.com

    1. Go to Salesforce.com's Setup page
    2. Go to Create -> Apps
    3. Add a new Connected App with a URL of: `http://your-app.herokuapp.com/_auth`

4. Add config params for authenticating to Salesforce.com obtained from the Connected App detail page:

    `$ heroku config:add OAUTH_CLIENT_KEY='Key without Quotes'`  
    `$ heroku config:add OAUTH_CLIENT_SECRET='Secret without Quotes'`  

5. Push the app to Heroku ('''mvn clean install''' is run automatically):

    `$ git push heroku master`  

6. Open the app in your browser:

    `$ heroku open`  

## Running on Cloud Foundry

1. Clone this project locally:

    `$ git clone https://github.com/forgreed/varrow-java-spring-sforce.git`  

2. Set app name and environment variables in `/manifest.yml`

3. Setup OAuth Remote Access in Salesforce.com

    1. Go to Salesforce.com's Setup page
    2. Go to Create -> Apps
    3. Add a new Connected App with a URL of: `http://your-app.cfapps.io/_auth`  

4. Build the app using and Push to Cloud Foundry

    `$ mvn clean install`  
    `$ cf push`  

5. Navigate to the presented url, i.e. `my-app.cfapps.io`

## To do:

1. Eclipse integration (working, just needs explanation)
2. Update bootstrap to v3

## DEEP DIVE: Courtesy of [James Ward](https://github.com/jamesward)


Now that you have everything working lets walk through how it works.

### Dependencies

First, lets look at the Maven build descriptor `pom.xml` file.  It specifies the dependencies of the application.  There are dependencies for `webapp-runner` (a Tomcat wrapper), Spring MVC for handling web requests, JSP support libraries, the Twitter Bootstrap CSS library, and the Force.com libraries.  Here are the Force.com dependencies:

    <dependency>
        <groupId>com.force.api</groupId>
        <artifactId>force-rest-api</artifactId>
        <version>0.0.18</version>
    </dependency>
    <dependency>
        <groupId>com.force.sdk</groupId>
        <artifactId>force-oauth</artifactId>
        <version>22.0.8-BETA</version>
    </dependency>
    <dependency>
        <groupId>com.force.sdk</groupId>
        <artifactId>force-springsecurity</artifactId>
        <version>22.0.8-BETA</version>
    </dependency>

The `force-rest-api` library is a Java wrapper around the Force.com REST APIs.  You can find more information about that library at:  
[https://github.com/jesperfj/force-rest-api](https://github.com/jesperfj/force-rest-api)

The `force-oauth` and `force-springsecurity` libraries make it easy to setup OAuth with Spring Security and Spring MVC.  You can find more information about those libraries at:  
[http://forcedotcom.github.com/java-sdk/](http://forcedotcom.github.com/java-sdk/)

This demo application uses Maven and Spring but you could certainly use those libraries in any type of JVM-based application.

### Spring Config for OAuth

To configure Spring Security and Spring MVC the `src/main/resources/applicationContext.xml` file contains:

    <?xml  version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xmlns:fss="http://www.salesforce.com/schema/springsecurity"
           xmlns:security="http://www.springframework.org/schema/security"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
                               http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd
                               http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd
                               http://www.salesforce.com/schema/springsecurity http://www.salesforce.com/schema/springsecurity/force-springsecurity-1.3.xsd
                               http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security-3.0.xsd">
    
        <context:annotation-config />
        <context:component-scan base-package="com.example" />
        
        <mvc:annotation-driven/>
        <mvc:resources mapping="/public/**" location="classpath:/public/"/>
    
        <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
            <property name="prefix" value="/WEB-INF/jsp/" />
            <property name="suffix" value=".jsp" />
            <property name="redirectHttp10Compatible" value="false" />
        </bean>
    
        <fss:oauth>
            <fss:oauthInfo endpoint="http://login.salesforce.com"
                           oauth-key="#{systemEnvironment['OAUTH_CLIENT_KEY']}"
                           oauth-secret="#{systemEnvironment['OAUTH_CLIENT_SECRET']}"/>
        </fss:oauth>
        
        <security:http use-expressions="true">
            <security:intercept-url pattern="/people/*" access="isAuthenticated()" />
        </security:http>
    
    </beans>

Most of the Spring config is typical Spring MVC configuration.  The `<fss:oauth>` section sets up the Force.com OAuth functionality using the `OAUTH_CLIENT_KEY` and `OAUTH_CLIENT_SECRET` environment variables which you already used for running the application locally and on Heroku.  The `<security:http use-expressions="true">` section protects requests matching the `/people/*` pattern and insures the requestor has authenticated via OAuth.

### Spring Security

In order to apply Spring Security and Spring MVC to the application, the `src/main/webapp/WEB-INF/web.xml` config file must contain the Spring Servlet Filter and the Spring MVC Dispatcher Servlet:

    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns="http://java.sun.com/xml/ns/javaee"
             xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
             version="2.5">
    
        <display-name>Spring-Hibernate-Template</display-name>
        
        <filter>
            <filter-name>springSecurityFilterChain</filter-name>
            <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
            <init-param>
                <param-name>contextAttribute</param-name>
                <param-value>org.springframework.web.servlet.FrameworkServlet.CONTEXT.spring</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>springSecurityFilterChain</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>
    
        <welcome-file-list>
            <welcome-file>index.jsp</welcome-file>
        </welcome-file-list>
    
        <servlet>
            <servlet-name>spring</servlet-name>
            <servlet-class>
                org.springframework.web.servlet.DispatcherServlet
            </servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:applicationContext.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        
        <servlet-mapping>
            <servlet-name>spring</servlet-name>
            <url-pattern>/people/*</url-pattern>
        </servlet-mapping>
    
        <servlet-mapping>
            <servlet-name>spring</servlet-name>
            <url-pattern>/assets/*</url-pattern>
        </servlet-mapping>
    
    </web-app>

Notice that in this case only the `/people/*` and `/assets/*` paths are mapped to the `DispatcherServlet` while Spring's `DelegatingFilterProxy` is applied all paths.  This example originated from the Tomcat and Spring template on [http://heroku.com/java](http://heroku.com/java) so it follows the conventions from there.  But it's up to you how you configure then path handling.  

### `Person` Value Object 

In order to map `contact` object data from Salesforce.com to a Java object we can use a JSON mapping conventions.  This is how the `force-rest-api` library maps data to a Java value object.  This application has a `Person` class defined in `src/main/java/com/example/model/Person.java` containing:

    package com.example.model;

    import org.codehaus.jackson.annotate.JsonIgnoreProperties;
    import org.codehaus.jackson.annotate.JsonProperty;

    @JsonIgnoreProperties(ignoreUnknown=true)
    public class Person {
    
        @JsonProperty(value="Id")
        private String id;
    
        @JsonProperty(value="FirstName")
        private String firstName;
    
        @JsonProperty(value="LastName")
        private String lastName;
    
        public String getId() { return id; }
        public String getFirstName() { return firstName; }
        public String getLastName() { return lastName; }
    
        public void setId(String id) { this.id = id; }
        public void setFirstName(String firstName) { this.firstName = firstName; }
        public void setLastName(String lastName) { this.lastName = lastName; }
    }    

In addition to the usual Java bean getters and setters, the `@JsonProperty` annotations define how to map from the fields on the `contact` data to the `Person` value object.  For instance, the `contact` object on Salesforce.com has a field named `FirstName` that is mapped to the `firstName` property on the `Person` object.  There is also a `@JsonIgnoreProperties(ignoreUnknown=true)` annotation on the class because we don't want to see an error if unknown properties can't be mapped.

### Data Access via the `ForceAPI`

With everything setup, it's now easy to call the Force.com REST APIs to retreive and change data.  The `PersonServiceImpl` class is essentially the data access object for the `contact` data on Salesforce.com.  Here is it's source from the `src/main/java/com/example/service/PersonServiceImpl.java` file:

    package com.example.service;
    
    import com.force.api.ApiSession;
    import com.force.api.ForceApi;
    import com.force.api.QueryResult;
    import com.force.sdk.oauth.context.ForceSecurityContextHolder;
    import com.force.sdk.oauth.context.SecurityContext;
    import org.springframework.stereotype.Service;
    
    import com.example.model.Person;
    
    import java.util.List;
    
    @Service
    public class PersonServiceImpl implements PersonService {
        
        private ForceApi getForceApi() {
            SecurityContext sc = ForceSecurityContextHolder.get();
    
            ApiSession s = new ApiSession();
            s.setAccessToken(sc.getSessionId());
            s.setApiEndpoint(sc.getEndPointHost());
    
            return new ForceApi(s);
        }
        
        public void addPerson(Person person) {
            getForceApi().createSObject("contact", person);
        }
    
        public List<Person> listPeople() {
            QueryResult<Person> res = getForceApi().query("SELECT Id, FirstName, LastName FROM contact", Person.class);
            return res.getRecords();
        }
    
        public void removePerson(String id) {
            getForceApi().deleteSObject("contact", id);
        }
        
    }

The `getForceApi` method sets up the Force.com REST connection using the OAuth information that Spring Security and the Force.com OAuth library worked together to provide.  The `addPerson` method simply creates a new `contact` object from a `Person` instance.  The `listPeople` method does a SOQL query to get all of the `contact` records and serialize them into `Person` objects.  Finally the `removePerson` method deletes the contact specified by the provided `id` value.  Salesforce.com applies the security to these operations so that a user can't access or change data that they shouldn't have access to.

The user interface for the web application is provided by the `src/main/java/com/example/controller/PersonController.java` Spring MVC Controller and the `src/main/webapp/WEB-INF/jsp/people.jsp` JSP view.  Those are just standard Spring MVC controller and view implementations.

### Further Learning

In the webinar we also demonstrated Chatter integration and Real-time Push with the Pusher add-on.  The source for those projects is at:  
[https://github.com/anandbn/spring-mvc-chatter](https://github.com/anandbn/spring-mvc-chatter)  
[https://github.com/anandbn/chatter-notify-pusher](https://github.com/anandbn/chatter-notify-pusher)

This example is just the basics of running Java applications on Heroku that integrate with Force.com.  If want to learn more about Java on Heroku, check out:  
[https://devcenter.heroku.com/articles/java-learn-more](https://devcenter.heroku.com/articles/java-learn-more)

If you want to learn more about the Force.com REST API, check out:    
[http://www.salesforce.com/us/developer/docs/api_rest/index.htm](http://www.salesforce.com/us/developer/docs/api_rest/index.htm)

