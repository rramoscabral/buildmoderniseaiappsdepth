**Usecase 01 - Incorporate Quarkus Todo applications into Container Apps
and integrate them with a relational database.**

**Objective:**

This usecase shows how to develop, configure, and deploy a
secure Quarkus To-d- application in Container Apps that's connected to a
Azure Database for PostgreSQL. When you're finished, you'll have a
Quarkus app running on Azure App Service on Linux.

**Key technologies used** – Java 17, Azure Database for PostgreSQL

**Estimated duration** – 45 minutes

**Lab Type:** Instructor Led

**Exercise 1 : Generate the Quarkus application by using Maven**

There are several ways to generate a Quarkus project structure. You can
use the Quarkus web interface, an IDE plugin, or the Quarkus Maven
plugin. Let's use the Maven plugin to generate the project structure.

You generate your application with several dependencies:

- The resteasy dependency to expose a REST endpoint

- The jackson dependency to serialize and deserialize JSON

- The hibernate dependency to interact with the database

- The postgresql dependency to connect to the PostgreSQL database

- The docker dependency to build a Docker image

You don't need to specify Azure dependencies because you run your
application locally first and then deploy a containerized version of it
to Azure Container Apps.

1.  Search for **Environment variable** from **Windows start** menu and
    select it.

2.  Click on Environment variable and check **JAVA_HOME** under user
    variable. Make sure JAVA_HOME is set to jdk17 as this lab requires
    **jdk17** to run.

> ![A screenshot of a computer Description automatically
> generated](./media/image1.png)
>
> ![A computer screen with white text Description automatically
> generated](./media/image2.png)

3.  Open **Git Bash** from the Window start menu and run below command

mvn -U io.quarkus:quarkus-maven-plugin:3.7.3:create \\

-DplatformVersion=3.7.3 \\

-DprojectGroupId=com.example.demo \\

-DprojectArtifactId=todo \\

-DclassName="com.example.demo.TodoResource" \\

-Dpath="/api/todos" \\

-DjavaVersion=17 \\

-Dextensions="resteasy-jackson, hibernate-orm-panache, jdbc-postgresql,
docker"

![A screenshot of a computer program Description automatically
generated](./media/image3.png)

4.  This command creates a new Quarkus project. It generates a Maven
    directory structure (src/main/java for source code
    and src/test/java for tests). It creates some Java classes, some
    tests, and some Dockerfiles. It also generates a *pom.xml* file with
    all the needed dependencies (Hibernate, RESTEasy, Jackson,
    PostgreSQL, and Docker):

5.  Click on Search and type **IntelliJ IDE** and then select **IntelliJ
    IDE**

> ![](./media/image4.png)

6.  Select confirmation check box and then click on **Continue** button.

![](./media/image5.png)

7.  Close the Data sharing window.

> ![](./media/image6.png)

8.  Select the **Start trial** radio button and then click on the
    **Start trial** button.

![](./media/image7.png)

9.  Click on the **Allow access** button.

![](./media/image8.png)

10. Close the browser, switch back to IntelliJ license window and click
    on the **Continue** button.

![](./media/image9.png)

11. Click on Open folder.

> ![](./media/image10.png)

12. Browse to **C:\Users\Admin\todo** and select **todo** project folder
    and then click on **OK**.

![](./media/image11.png)

13. Click on the **Trust project** button.

![](./media/image12.png)

14. Open **pom.xml** and you should see below xml format.

\<dependencies\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-hibernate-orm-panache\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-resteasy-jackson\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-jdbc-postgresql\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-container-image-docker\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-arc\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-hibernate-orm\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-resteasy\</artifactId\>

\</dependency\>

\<dependency\>

\<groupId\>io.quarkus\</groupId\>

\<artifactId\>quarkus-junit5\</artifactId\>

\<scope\>test\</scope\>

\</dependency\>

\<dependency\>

\<groupId\>io.rest-assured\</groupId\>

\<artifactId\>rest-assured\</artifactId\>

\<scope\>test\</scope\>

\</dependency\>

\</dependencies\>

** **

**Note**

All the dependencies in the *pom.xml* file are defined in the Quarkus
BOM (bill of materials) io.quarkus.platform:quarkus-bom.

![A screenshot of a computer program Description automatically
generated](./media/image13.png)

**Task 2 : Code the application**

1.  Go to **src/main/java/com.example.demo** and right click on
    **MyEntity.Java -\> Refactor -\> Rename**.

![](./media/image14.png)

2.  Rename the generated ***MyEntity.java*** class to +++***Todo
    +++*** (located in the same folder as the *TodoResource.java* file)

![](./media/image15.png)

3.  Replace the existing code with the following Java code. It uses Java
    Persistence API (jakarta.persistence.\* package) to store and
    retrieve data from your PostgreSQL server. It also uses *Hibernate
    ORM with Panache* (inheriting
    from io.quarkus.hibernate.orm.panache.PanacheEntity) to simplify the
    persistence layer.

4.  You use a JPA entity (@Entity) to map the Java Todo object directly
    to the PostgreSQL Todo table. The TodoResource REST endpoint then
    creates a new Todo entity class and persists it. This class is a
    domain model that's mapped on the Todo table. The table is
    automatically created by JPA.

5.  Extending PanacheEntity gets you a number of generic create, read,
    update, and delete (CRUD) methods for your type. So you can do
    things like saving and deleting Todo objects in just one line of
    Java code.

6.  Setup JDK in IntelliJ if it was not already set.

![A screenshot of a computer Description automatically
generated](./media/image16.png)

7.  Replace the existing code with the following Java code to
    the Todo entity:

package com.example.demo;

import io.quarkus.hibernate.orm.panache.PanacheEntity;

import jakarta.persistence.Entity;

import java.time.Instant;

@Entity

public class Todo extends PanacheEntity {

public String description;

public String details;

public boolean done;

public Instant createdAt = Instant.now();

@Override

public String toString() {

return "Todo{" +

"id=" + id + '\\'' +

", description='" + description + '\\'' +

", details='" + details + '\\'' +

", done=" + done +

", createdAt=" + createdAt +

'}';

}

}

![A screenshot of a computer program Description automatically
generated](./media/image17.png)

8.  To manage that class, update the **TodoResource** so that it can
    publish REST interfaces to store and retrieve data by using HTTP.
    Open the **TodoResource** class and replace the code with the
    following:

package com.example.demo;

import jakarta.inject.Inject;

import jakarta.transaction.Transactional;

import jakarta.ws.rs.Consumes;

import jakarta.ws.rs.GET;

import jakarta.ws.rs.POST;

import jakarta.ws.rs.Path;

import jakarta.ws.rs.Produces;

import static jakarta.ws.rs.core.MediaType.APPLICATION_JSON;

import jakarta.ws.rs.core.Response;

import jakarta.ws.rs.core.UriBuilder;

import jakarta.ws.rs.core.UriInfo;

import org.jboss.logging.Logger;

import java.util.List;

@Path("/api/todos")

@Consumes(APPLICATION_JSON)

@Produces(APPLICATION_JSON)

public class TodoResource {

@Inject

Logger logger;

@Inject

UriInfo uriInfo;

@POST

@Transactional

public Response createTodo(Todo todo) {

logger.info("Creating todo: " + todo);

Todo.persist(todo);

UriBuilder uriBuilder =
uriInfo.getAbsolutePathBuilder().path(todo.id.toString());

return Response.created(uriBuilder.build()).entity(todo).build();

}

@GET

public List\<Todo\> getTodos() {

logger.info("Getting all todos");

return Todo.listAll();

}

}

![](./media/image18.png)

**Task 3 : Run the application**

When you run the application in development mode, Docker needs to be
running. That's because Quarkus detects that you need a PostgreSQL
database (because of the PostgreSQL
dependency quarkus-jdbc-postgresql declared in the *pom.xml* file),
downloads the PostgreSQL Docker image, and starts a container with the
database. It then automatically creates the Todo table in the database.

1.  Double-click on **Docker Desktop**. Make sure it’s running.(no need
    to Sign in )

![](./media/image19.png)

![A screenshot of a computer Description automatically
generated](./media/image20.png)

2.  Go back to **Gitbash** and run the to-do application by using this
    command:

+++cd todo+++

+++./mvnw quarkus:dev+++

Note : ./mvnw quarkus:dev \# On Mac or Linux

mvnw.cmd quarkus:dev \# On Windows

![A screen shot of a computer Description automatically
generated](./media/image21.png)

3.  The Quarkus application should start and connect to your database.
    You should see the following output:

![A screenshot of a computer program Description automatically
generated](./media/image22.png)

![A computer screen shot of a computer program Description automatically
generated](./media/image23.png)

4.  Click on **Allow access.**

![](./media/image24.png)

![](./media/image25.png)

5.  To test the application, you can use cURL.

In a separate new instance of Gitbash , create a new to-do item in the
database with the following command. You should see the log in the
Quarkus console:

curl --header "Content-Type: application/json" \\

--request POST \\

--data '{"description":"Take Quarkus MS Learn","details":"Take the MS
Learn on deploying Quarkus to Azure Container Apps","done": "true"}' \\

<http://127.0.0.1:8080/api/todos>

![A computer screen with white text Description automatically
generated](./media/image26.png)

6.  This command should return the created item (with an identifier):

![A computer screen with white text Description automatically
generated](./media/image26.png)

7.  Create a second to-do by using the following cURL command:

curl --header "Content-Type: application/json" \\

--request POST \\

--data '{"description":"Take Azure Container Apps MS
Learn","details":"Take the ACA Learn module","done": "false"}' \\

<http://127.0.0.1:8080/api/todos>

![](./media/image27.png)

8.  Next, retrieve the data by using a new cURL request:

+++curl http://127.0.0.1:8080/api/todos+++

This command returns the list of to-do items, including the items you
created:

![A screenshot of a computer Description automatically
generated](./media/image28.png)

**Task 4 : Test the application**

To test the application, you can use the
existing **TodoResourceTest** class. It needs to test the REST endpoint.
To test the endpoint, it uses *RESTAssured*.

1.  Switch back to Intellij and open **TodoResourceTest**  class from
    **src/test/java/com.example.demo**. Replace code in
    the **TodoResourceTest** class with the following code:

package com.example.demo;

import io.quarkus.test.junit.QuarkusTest;

import static io.restassured.RestAssured.given;

import static jakarta.ws.rs.core.HttpHeaders.CONTENT_TYPE;

import static jakarta.ws.rs.core.MediaType.APPLICATION_JSON;

import org.junit.jupiter.api.Test;

@QuarkusTest

class TodoResourceTest {

@Test

void shouldGetAllTodos() {

given()

.when().get("/api/todos")

.then()

.statusCode(200);

}

@Test

void shouldCreateATodo() {

Todo todo = new Todo();

todo.description = "Take Quarkus MS Learn";

todo.details = "Take the MS Learn on deploying Quarkus to Azure
Container Apps";

todo.done = true;

given().body(todo)

.header(CONTENT_TYPE, APPLICATION_JSON)

.when().post("/api/todos")

.then()

.statusCode(201);

}

}

![A screenshot of a computer program Description automatically
generated](./media/image29.png)

2.  When you test the application, Docker Desktop needs to be running
    because Quarkus detects that it needs the PostgreSQL database for
    testing.

3.  Switch back to **Gitbash** and Ctrl + C. Run below commands to test
    the application by using this command:

+++./mvnw clean test+++

![A screen shot of a computer program Description automatically
generated](./media/image30.png)

Note : mvnw.cmd clean test \# on mac

You should see output that looks similar to this:

![A screenshot of a computer program Description automatically
generated](./media/image31.png)

**Exercise 2 - Set up Azure Container Apps**

In this Exercise, you create an Azure resource group that contains the
resources for the application. You then set up the PostgreSQL database
by using the Azure CLI. Finally, you configure the Quarkus application
to access the remote PostgreSQL database. Use a terminal of your choice
to run the commands.

**Task 1 : Prepare the working environment**

You need to set up some environment variables. Here are some notes about
the variables you'll create:

Expand table

[TABLE]

** **

**Note :**You can name your Azure resources in any way that you want,
but we recommend that you review [***Abbreviation examples for Azure
resources***](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations).
This article provides example abbreviations for many Azure resources
(for example, rg for resource groups and ca for container apps).

Use the following commands to set up the variables. Be sure to modify
the values as described in the preceding table. These environment
variables are used throughout the rest of this module.

Note: Use the location which is near to your location eg: West US,East
US, East US2 etc…

export AZ_PROJECT_Quarkus="azure-deploy-quarkus-"$RANDOM

export AZ_RESOURCE_GROUP="rg${AZ_PROJECT_Quarkus}"

export AZ_LOCATION="Location near to you"

export AZ_CONTAINERAPP="ca${AZ_PROJECT_Quarkus}"

export AZ_CONTAINERAPP_ENV="cae${AZ_PROJECT_Quarkus}"

export AZ_POSTGRES_DB_NAME="postgres${AZ_PROJECT_Quarkus}"

export AZ_POSTGRES_USERNAME="azuser123"

export AZ_POSTGRES_PASSWORD="P@55w.rd12345"

export AZ_POSTGRES_SERVER_NAME="psql${AZ_PROJECT_Quarkus}"

![A screen shot of a computer Description automatically
generated](./media/image32.png)

4.  Run +++az login+++ It opens the default browser to sign in.

5.  Run below command to create a resource group in Azure Portal.

+++az group create --name $AZ_RESOURCE_GROUP --location $AZ_LOCATION+++

![A computer screen with white text Description automatically
generated](./media/image33.png)

**Task 2 : Create an instance of Azure Database for PostgreSQL**

1.  You'll now create a managed PostgreSQL server. Run the following
    command to create a small instance of Azure Database for PostgreSQL:

az postgres flexible-server create --resource-group "$AZ_RESOURCE_GROUP"
--location "$AZ_LOCATION" --name "$AZ_POSTGRES_SERVER_NAME"
--database-name "$AZ_POSTGRES_DB_NAME" --admin-user
"$AZ_POSTGRES_USERNAME" --admin-password "$AZ_POSTGRES_PASSWORD"
--public-access "All" --tier "Burstable" --sku-name "Standard_B1ms"
--storage-size 32 --version "16"

![A screen shot of a computer screen Description automatically
generated](./media/image34.png)

2.  This command creates a small PostgreSQL server that uses the
    variables that you set up earlier.

![A screenshot of a computer screen Description automatically
generated](./media/image35.png)

**Task 3 : Configure Quarkus to access the PostgreSQL database**

1.  You'll now connect the Quarkus application to the PostgreSQL
    database. To do so, you first need to obtain the connection string
    for the database:

2.  Run below command to obtain the connection string for the database.

export POSTGRES_CONNECTION_STRING=$(

az postgres flexible-server show-connection-string --server-name
"$AZ_POSTGRES_SERVER_NAME" --database-name "$AZ_POSTGRES_DB_NAME"
--admin-user "$AZ_POSTGRES_USERNAME" --admin-password
"$AZ_POSTGRES_PASSWORD" --query "connectionStrings.jdbc" --output tsv

)

export
POSTGRES_CONNECTION_STRING_SSL="$POSTGRES_CONNECTION_STRING&ssl=true&sslmode=require"

echo "POSTGRES_CONNECTION_STRING_SSL=$POSTGRES_CONNECTION_STRING_SSL"

![A computer screen with white text Description automatically
generated](./media/image36.png)

3.  Note the connection string that's returned.

![](./media/image37.png)

**Task 4 :Configure the Quarkus application to connect to the PostgreSQL
database**

1.  Switch back to Intellij IDE. Update
    the **application.properties** file in
    the **src/main/resources** folder of the project to configure the
    connection string to the PostgreSQL database.

> ![A screenshot of a computer Description automatically
> generated](./media/image38.png)

2.  Set the **quarkus.datasource.jdbc.url** property to the previously
    output **$POSTGRES_CONNECTION_STRING_SSL** value.
    The **&ssl=true&sslmode=require** part of the connection string
    forces the driver to use SSL, a requirement for Azure Database for
    PostgreSQL.

quarkus.hibernate-orm.database.generation=update

quarkus.datasource.jdbc.url=\<the POSTGRES_CONNECTION_STRING_SSL value\>

![A screenshot of a computer Description automatically
generated](./media/image39.png)

**Task 5 : Run the Quarkus application locally to test the remote
database connection**

1.  Switch back to Gitbash and run below command to run the application
    locally:

+++./mvnw clean quarkus:dev+++

Note : mvnw.cmd clean quarkus:dev \# On Mac or Linux

![A screen shot of a computer program Description automatically
generated](./media/image40.png)

![A screenshot of a computer program Description automatically
generated](./media/image41.png)

![A screenshot of a computer program Description automatically
generated](./media/image42.png)

2.  When Quarkus is running, create a few to-dos by using the following
    cURL commands in a separate terminal window:

curl --header "Content-Type: application/json" \\

--request POST \\

--data '{"description":"Take Quarkus MS Learn","details":"Take the MS
Learn on deploying Quarkus to Azure Container Apps","done": "true"}' \\

<http://127.0.0.1:8080/api/todos>

![A computer screen with white text Description automatically
generated](./media/image43.png)

curl --header "Content-Type: application/json" \\

--request POST \\

--data '{"description":"Take Azure Container Apps MS
Learn","details":"Take the ACA Learn module","done": "false"}' \\

<http://127.0.0.1:8080/api/todos>

![A screenshot of a computer program Description automatically
generated](./media/image44.png)

3.  Next, check that the to-dos are in the database by accessing the GET
    endpoint that's defined in the to-do app:

+++curl http://127.0.0.1:8080/api/todos+++

You should see the following output:

![A screenshot of a computer program Description automatically
generated](./media/image45.png)

If you see this output, you have successfully run the Quarkus
application and connected to the remote PostgreSQL database.

# Exercise 3 : Deploy a Quarkus application to Azure Container Apps

In this exercise , you create the Azure Container Apps environment by
using the Azure CLI.

## Task 1 : Set up the Dockerfile for the Quarkus application

1.  Container Apps is used to deploy containerized applications. So you
    first need to containerize the Quarkus application into a Docker
    image. This process is easy because the Quarkus Maven plugin has
    already generated some Dockerfiles under **src/main/docker**.

![](./media/image46.png)

4.  Use this command to rename one of these
    **Dockerfiles, *Dockerfile.jvm*,** to ***Dockerfile*** and move it
    to the root folder:

+++mv src/main/docker/Dockerfile.jvm ./Dockerfile+++

![A black screen with white text Description automatically
generated](./media/image47.png)

5.  Replace the content after the long comment in the **Dockerfile**
    with the following:

FROM registry.access.redhat.com/ubi8/openjdk-17:1.18

ENV LANGUAGE='en_US:en'

\# We make four distinct layers so if there are application changes the
library layers can be re-used

COPY --chown=185 target/quarkus-app/lib/ /deployments/lib/

COPY --chown=185 target/quarkus-app/\*.jar /deployments/

COPY --chown=185 target/quarkus-app/app/ /deployments/app/

COPY --chown=185 target/quarkus-app/quarkus/ /deployments/quarkus/

EXPOSE 8080

USER 185

ENV JAVA_OPTS_APPEND="-Dquarkus.http.host=0.0.0.0
-Djava.util.logging.manager=org.jboss.logmanager.LogManager"

ENV JAVA_APP_JAR="/deployments/quarkus-run.jar"

ENTRYPOINT \[ "/opt/jboss/container/java/run/run-java.sh" \]

![](./media/image48.png)

6.  This Dockerfile expects the Quarkus application to be packaged as
    a ***quarkus-run.jar* file**. This name is the default name for the
    Quarkus application when it's packaged as a JAR file. You need to
    make sure that the Quarkus application is packaged as a JAR file. To
    do so, run the following Maven command:

+++./mvnw package+++ \# On Mac or Linux

![A screen shot of a computer code Description automatically
generated](./media/image49.png)

![A screenshot of a computer program Description automatically
generated](./media/image50.png)

Note : mvnw.cmd package \# On Windows

7.  This command packages the Quarkus application into a JAR file and
    generates a ***quarkus-run.jar*** file in
    the ***target/quarkus-app*** folder.

![](./media/image51.png)

## Task 2 : Create the Container Apps environment and deploy the container

1.  Now that the Dockerfile is in the right location, you can create the
    Container Apps environment and deploy the container by using a
    single Azure CLI command. Run the following command at the root of
    the project:

az containerapp up --name "$AZ_CONTAINERAPP" --environment
"$AZ_CONTAINERAPP_ENV" --location "$AZ_LOCATION" --resource-group
"$AZ_RESOURCE_GROUP" --ingress external --target-port 8080 --source .

![A screenshot of a computer program Description automatically
generated](./media/image52.png)

![A screenshot of a computer program Description automatically
generated](./media/image53.png)

![A screenshot of a computer Description automatically
generated](./media/image54.png)

![A screenshot of a computer Description automatically
generated](./media/image55.png)

2.  This command does several things:

- Creates a Container Apps environment if it doesn't exist

- Creates an Azure registry if it doesn't exist

- Creates a Log Analytics workspace if it doesn't exist

- Builds the Docker image and pushes it to the Azure registry

- Deploys the Docker image to the Container Apps environment

The az containerapp up command takes some time to run. You should see
output that's similar to the following:

![A screenshot of a computer screen Description automatically
generated](./media/image56.png)

## Task 3 : Validate the deployment

You can validate that the deployment has succeeded in several ways. The
easiest way is to search for your resource group on the [Azure
portal](https://portal.azure.com/). You should see resources similar to
the following:

1.  Open a browser and go to <https://portal.azure.com> and sign in with
    your Azure subscription account. Click on Resource Group tile.

![](./media/image57.png)

2.  Click on the resource group name.

> ![](./media/image58.png)

![A screenshot of a computer Description automatically
generated](./media/image59.png)

3.  You can also check the deployment by running the following command.
    It lists all the resources created by the az containerapp
    up command.

az resource list --location "$AZ_LOCATION" --resource-group
"$AZ_RESOURCE_GROUP" --output table

You should see output that's similar to this:

## ![A screenshot of a computer Description automatically generated](./media/image60.png)

## Task 4 : Run the deployed Quarkus application

1.  You can now run the deployed Quarkus application. First, you need to
    get the URL of the application.

2.  Switch back to Gitbash and run below command to get the URL of the
    application.

export AZ_APP_URL=$(

az containerapp show \\

--name "$AZ_CONTAINERAPP" \\

--resource-group "$AZ_RESOURCE_GROUP" \\

--query "properties.configuration.ingress.fqdn" \\

--output tsv \\

)

echo "AZ_APP_URL=$AZ_APP_URL"

![A screenshot of a computer program Description automatically
generated](./media/image61.png)

3.  Your application is ready
    at https://\<app-name\>.azurecontainerapps.io/. Notice
    the https protocol. That protocol is used because the application is
    deployed with a TLS certificate. To test the application, you can
    use cURL:

curl --header "Content-Type: application/json" \\

--request POST \\

--data '{"description":"Configuration","details":"Congratulations, you
have set up your Quarkus application correctly!","done": "true"}' \\

<https://$AZ_APP_URL/api/todos>

![A screenshot of a computer program Description automatically
generated](./media/image62.png)

4.  Retrieve the data by using a new cURL request:

+++curl <https://$AZ_APP_URL/api/todos>+++

5.  This command returns the list of all to-do items from the database:

![A computer screen with white text Description automatically
generated](./media/image63.png)

6.  Switch back to the Azure portal and click on your container app
    name.

![](./media/image64.png)

7.  click on the application url link. It opens app in browser tab.

![](./media/image65.png)

![A screenshot of a computer Description automatically
generated](./media/image66.png)

5\. Run this command, you can stream the logs for your container when
you create new to-dos:

az containerapp logs show --name "$AZ_CONTAINERAPP" --resource-group
"$AZ_RESOURCE_GROUP" –follow

![A black screen with white text Description automatically
generated](./media/image67.png)

8.  Run more cURL commands. You should see the logs scrolling in the
    terminal.

+++curl <https://$AZ_APP_URL/api/todos>+++

** **![A screenshot of a computer program Description automatically
generated](./media/image68.png)

# 

# Exercise 4 : Delete resources

## Task 1 : Delete resources.

1.  Switch back to your Gitbash and run below command to delete resource
    goup

> +++az group delete --name $AZ_RESOURCE_GROUP --yes+++

![A black screen with yellow text Description automatically
generated](./media/image69.png)

## **Summary.**

You learnt how to use Maven to bootstrap the application and an
integrated development environment (IDE) to edit the code.. You learnt
how to use Docker to start a local PostgreSQL database so you can run
and test the application locally. You have successfully run the Quarkus
application and connected to the remote PostgreSQL database.