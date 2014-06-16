# Varrow - Spring MVC and Force.com Template

Web app template utilizing Spring MVC and Salesforce.
This was optimized for Varrow's development team with the code from:
    - https://github.com/jamesward/hello-java-spring-force_dot_com
    - https://github.com/ryanbrainard/richsobjects
This application can be run on:
    - Your local machine, through the command line or eclipse
    - heroku
    - Cloud Foundry

## Requirements:
    1. [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) - WILL NOT WORK WITH JDK 8
    2. [Apache Maven](http://maven.apache.org/download.cgi)
    3. [git command line tools](http://git-scm.com/downloads)
    4. [Heroku Toolbelt](https://toolbelt.heroku.com/) and account
        $ heroku login
    5. [Cloud Foundry command line tools](https://github.com/cloudfoundry/cli/releases) and account
        $ cf login


## Running the app locally

Setup OAuth Remote Access in Salesforce.com

    1. Go to Salesforce.com's Setup page
    2. Go to Create -> Apps
    3. Add a new Connected App with a URL of: http://localhost:8080/_auth
        - This can be reused for multiple oauth2 apps

Add environment variables for authenticating to Salesforce.com obtained from the Connected App detail page:

- Linux/Mac:

        $ export OAUTH_CLIENT_KEY='Key without Quotes'
        $ export OAUTH_CLIENT_SECRET='Secret without Quotes'

- Windows:

        $ set OAUTH_CLIENT_KEY='Key without Quotes'
        $ set OAUTH_CLIENT_SECRET='Secret without Quotes'

Build with:

    $ mvn clean install

Then run it with:

    $ java -jar target/dependency/webapp-runner.jar target/*.war

After first build, simplify testing with:

    $ mvn install && java -jar target/dependency/webapp-runner.jar target/*.war

## Running on Heroku

Clone this project locally:

    $ git clone https://github.com/forgreed/varrow-java-spring-sforce.git

Create a new app on Heroku

    $ heroku create anyNameYouLike

Setup OAuth Remote Access in Salesforce.com

    1. Go to Salesforce.com's Setup page
    2. Go to Create -> Apps
    3. Add a new Connected App with a URL of: http://your-app.herokuapp.com/_auth

Add config params for authenticating to Salesforce.com obtained from the Connected App detail page:

    $ heroku config:add OAUTH_CLIENT_KEY='Key without Quotes'
    $ heroku config:add OAUTH_CLIENT_SECRET='Secret without Quotes'

Upload the app to Heroku:

    $ git push heroku master

Open the app in your browser:

    $ heroku open

## Running on Cloud Foundry

Clone this project locally:

    $ git clone https://github.com/forgreed/varrow-java-spring-sforce.git

Set app name and environment variables in /manifest.yml

Build the app using and Push to Cloud Foundry

    $ mvn clean install
    $ cf push