# Usecase 01 - Incorporate Quarkus Todo applications into Container Apps and integrate them with a relational database.

**Objective:**

This usecase shows how to develop, configure, and deploy a secure
Quarkus To-d- application in Container Apps that's connected to a Azure
Database for PostgreSQL. When you're finished, you'll have a Quarkus app
running on Azure App Service on Linux.

**Key technologies used** -- Java 17, Azure Database for PostgreSQL

**Estimated duration** -- 45 minutes

**Lab Type:** Instructor Led

### Task 0: Setup Environmental variables

1.  Search Environmental variable from Windows start menu and select Edit System Environment variable.

    ![](./media/image1.jpeg)

2.  Click on **Environment Variable** button.

    ![](./media/image2.jpeg)

3.  Select **JAVA_HOME** under **User variable for Admin** and then click on **Edit**.

    ![A screenshot of a computer Description automatically generated](./media/image3.jpeg)

4.  Enter the variable value as +++C:\Program Files\Java\jdk-17+++ and then click on **Ok**.

    ![](./media/image4.jpeg)

5.  Navigate to the folder **C:\Software** and right click on **apache-maven-3.9.4-bin.zip** folder and select **Extract All**.

    ![](./media/image5.jpeg)

6.  **Extract** in the same folder.

    ![A screenshot of a computer Description automatically generated](./media/image6.jpeg)

7.  Switch back to the Edit Environment variable window, select **MAVEN_HOME** and click on **Edit**.

    ![A screenshot of a computer Description automatically generated](./media/image7.jpeg)

8.  Enter the Variable value as +++C:\Software\apache-maven-3.9.4-bin\apache-maven-3.9.4+++ and then click on **OK**.

    ![A screenshot of a computer Description automatically generated](./media/image8.jpeg)

9.  On **Environmental Variable**s window, click on **Ok** and
    again **OK**.

    ![](./media/image9.jpeg)

    ![A screenshot of a computer Description automatically generated](./media/image10.jpeg)

10. Update **JAVA_HOME** as per lab requirements before you run lab.

## Exercise 1 : Generate the Quarkus application by using Maven

There are several ways to generate a Quarkus project structure. You can
use the Quarkus web interface, an IDE plugin, or the Quarkus Maven
plugin. Let's use the Maven plugin to generate the project structure.

You generate your application with several dependencies:

- The resteasy dependency to expose a REST endpoint

- The jackson dependency to serialize and deserialize JSON

- The hibernate dependency to interact with the database

- The postgresql dependency to connect to the PostgreSQL database

- The docker dependency to build a Docker image

You don't need to specify Azure dependencies because you run your
application locally first and then deploy a containerized version of it
to Azure Container Apps.

### Task 1 : Generate the Quarkus application

1.  Open **Git Bash** from the Window start menu and run below command

    ```bash
    mvn -U io.quarkus:quarkus-maven-plugin:3.7.3:create \
    -DplatformVersion=3.7.3 \
    -DprojectGroupId=com.example.demo \
    -DprojectArtifactId=todo \
    -DclassName="com.example.demo.TodoResource" \
    -Dpath="/api/todos" \
    -DjavaVersion=17 \
    -Dextensions="resteasy-jackson, hibernate-orm-panache, jdbc-postgresql, docker"
    ```    
    ![](./media/image11.jpeg)

2.  This command creates a new Quarkus project. It generates a Maven
    directory structure (src/main/java for source code and src/test/java
    for tests). It creates some Java classes, some tests, and some
    Dockerfiles. It also generates a *pom.xml* file with all the needed
    dependencies (Hibernate, RESTEasy, Jackson, PostgreSQL, and Docker):

3.  Click on Search and type +++IntelliJ IDE+++ and then select **IntelliJ IDE**

    ![A screenshot of a computer Description automatically generated](./media/image12.jpeg)

4.  Select confirmation check box and then click on **Continue** button.

    ![A screenshot of a computer screen Description automatically generated](./media/image13.jpeg)

5.  Close the Data sharing window.

    ![](./media/image14.jpeg)

6.  Select the **Start trial** radio button and then click on
    the **Start trial** button.

    ![A screenshot of a computer Description automatically generated](./media/image15.jpeg)

7.  Click on the **Allow access** button.

    ![](./media/image16.jpeg)

8.  Close the browser, switch back to IntelliJ license window and click
    on the **Continue** button.

    ![A screenshot of a computer Description automatically generated](./media/image17.jpeg)

9.  Click on **Open** folder.

    ![](./media/image18.jpeg)

10. Browse to **C:\Users\Admin\todo** and select **todo** project folder and then click on+++**OK**.

    ![A screenshot of a computer Description automatically generated](./media/image19.jpeg)

11. Click on the **Trust project** button.

    ![](./media/image20.jpeg)

12. Open **pom.xml** and you should see below xml format.

    ```xml
    <dependencies>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-hibernate-orm-panache</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-resteasy-jackson</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-jdbc-postgresql</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-container-image-docker</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-arc</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-hibernate-orm</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-resteasy</artifactId>
        </dependency>
        <dependency>
    		<groupId>io.quarkus</groupId>
    		<artifactId>quarkus-junit5</artifactId>
    		<scope>test</scope>
        </dependency>
        <dependency>
    		<groupId>io.rest-assured</groupId>
    		<artifactId>rest-assured</artifactId>
    		<scope>test</scope>
        </dependency>
    </dependencies>
    ```

    **Note** All the dependencies in the *pom.xml* file are defined in the Quarkus BOM (bill of materials) io.quarkus.platform:quarkus-bom.

    ![](./media/image21.jpeg)

### Task 2 : Code the application

1.  Go to **src/main/java/com.example.demo** and right click
    on **MyEntity.Java -\> Refactor -\> Rename**.

    ![](./media/image22.jpeg)

2.  Rename the generated ***MyEntity.java*** class
    to \`\`**Todo.java\`\`** (located in the same folder as
    the *TodoResource.java* file)

    ![](./media/image23.jpeg)

3.  Replace the existing code with the following Java code. It uses Java
    Persistence API (jakarta.persistence.\* package) to store and
    retrieve data from your PostgreSQL server. It also uses \[Hibernate
    ORM with Panache\]{.underline} (inheriting from
    io.quarkus.hibernate.orm.panache.PanacheEntity) to simplify the
    persistence layer.

4.  You use a JPA entity (@Entity) to map the Java Todo object directly
    to the PostgreSQL Todo table. The TodoResource REST endpoint then
    creates a new Todo entity class and persists it. This class is a
    domain model that\\'s mapped on the Todo table. The table is
    automatically created by JPA.

5.  Extending PanacheEntity gets you a number of generic create, read,
    update, and delete (CRUD) methods for your type. So you can do
    things like saving and deleting Todo objects in just one line of
    Java code.

6.  Setup JDK in IntelliJ if it was not already set.

    ![A screenshot of a computer Description automatically generated](./media/image24.jpeg)

7.  Replace the existing code with the following Java code to the Todo
    entity:

    ```java
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
                    "id=" + id + '\\' + 
                    ", description='" + description + '\\' +
                    ", details='" + details + '\\' +
                    ", done=" + done +
                    ", createdAt=" + createdAt +
                    '}';
        }
    }
    ```

    ![A screenshot of a computer Description automatically generated](./media/image25.jpeg)

8.  To manage that class, update the **TodoResource** so that it can
    publish REST interfaces to store and retrieve data by using HTTP.
    Open the **TodoResource** class and replace the code with the
    following:

    ```java
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
    
            UriBuilder uriBuilder = uriInfo.getAbsolutePathBuilder().path(todo.id.toString());
    
            return Response.created(uriBuilder.build()).entity(todo).build();
    
        }
    
        @GET
    
        public List <Todo> getTodos() {
    
            logger.info("Getting all todos");
    
            return Todo.listAll();
    
        }
    
    }
    ```

### ![A screenshot of a computer program Description automatically generated](./media/image26.jpeg)

### **Task 3 : Run the application**

When you run the application in development mode, Docker Desktop needs
to be running. That's because Quarkus detects that you need a PostgreSQL
database (because of the PostgreSQL dependency quarkus-jdbc-postgresql
declared in the *pom.xml* file), downloads the PostgreSQL Docker Desktop
image, and starts a container with the database. It then automatically
creates the Todo table in the database.

1.  Double-click on **Docker Desktop** and minimize the window. Make sure it’s running.(no need to Sign in )

    ![A computer screen with a white background Description automatically generated](./media/image27.jpeg)

2.  Go back to Gitbash and run the to-do application by using this
    command:

    +++cd todo+++
    
    +++./mvnw quarkus:dev+++

    ![](./media/image28.jpeg)

3.  The Quarkus application should start and connect to your database.
    You should see the following output:

    ![A screenshot of a computer program Description automatically generated](./media/image29.jpeg)
    
    ![A computer screen with text and images Description automatically generated](./media/image30.jpeg)
    
    ![](./media/image31.jpeg)
    
    ![A screen shot of a computer program Description automatically generated](./media/image32.jpeg)
    
4.  Click on **Allow access**.

    ![](./media/image31.jpeg)

5.  To test the application, you can use cURL.

    In a separate new instance of Gitbash , create a new to-do item in the database with the following command. You should see the log in the Quarkus console:

    ```bash

    curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"Take Quarkus MS Learn","details":"Take the MS Learn on deploying Quarkus to Azure Container Apps","done": "true"}' \
    http://127.0.0.1:8080/api/todos
    ```


    ![A computer screen with white text Description automatically generated](./media/image33.jpeg)

6.  This command should return the created item (with an identifier):

    ![A computer screen with white text Description automatically generated](./media/image33.jpeg)

7.  Create a second to-do by using the following cURL command:

    ```bash
    curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"Take Azure Container Apps MS Learn","details":"Take the ACA Learn module","done": "false"}' \
    http://127.0.0.1:8080/api/todos
    ```


    ![A screenshot of a computer program Description automatically generated](./media/image34.jpeg)

8.  Next, retrieve the data by using a new cURL request:

    +++curl http://127.0.0.1:8080/api/todos+++

    This command returns the list of to-do items, including the items you created:

    ![](./media/image35.jpeg)

### Task 4 : Test the application

To test the application, you can use the existing **TodoResourceTest** class. It needs to test the REST endpoint.
To test the endpoint, it uses \[RESTAssured\]{.underline}.

1.  Switch back to Intellij and open **TodoResourceTest** class
    from **src/test/java/com.example.demo**. Replace code in
    the **TodoResourceTest** class with the following code:

    ```java
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
    
    ```

    ![A computer screen shot of a program Description automatically generated](./media/image36.jpeg)

2.  When you test the application, Docker Desktop needs to be running
    because Quarkus detects that it needs the PostgreSQL database for
    testing.

3.  Switch back to **Gitbash** and Ctrl + C. Run below commands to test
    the application by using this command:

    ```bash
    ./mvnw clean test
    ```
    ![A computer screen with text and images Description automatically generated](./media/image37.jpeg)

    You should see output that looks similar to this:

    ![A computer screen with text and numbers Description automatically generated](./media/image38.jpeg)

## Exercise 2 - Set up Azure Container Apps

In this Exercise, you create an Azure resource group that contains the
resources for the application. You then set up the PostgreSQL database
by using the Azure CLI. Finally, you configure the Quarkus application
to access the remote PostgreSQL database. Use a terminal of your choice
to run the commands.

**Task 1 : Prepare the working environment**

You need to set up some environment variables. Here are some notes about
the variables you'll create:

[TABLE]

**Note:** You can name your Azure resources in any way that you want.
This article provides example abbreviations for many Azure resources
(for example, rg for resource groups and ca for container apps).

1.  Use the following commands to set up the variables. Be sure to
    modify the values as described in the preceding table. These
    environment variables are used throughout the rest of this module.

    **Note:** PostgreSQL is supported only in **Westus** . Try in westus location first and it you have any issues then try in location near to you.

    

    +++export AZ_PROJECT_Quarkus="azure-deploy-quarkus-"$RANDOM+++
    
    +++export AZ_RESOURCE_GROUP="rg${AZ_PROJECT_Quarkus}"+++
    
    +++export AZ_LOCATION="Location near to you"+++
    
    +++export AZ_CONTAINERAPP="ca${AZ_PROJECT_Quarkus}"+++
    
    +++export AZ_CONTAINERAPP_ENV="cae${AZ_PROJECT_Quarkus}"+++
    
    +++export AZ_POSTGRES_DB_NAME="postgres${AZ_PROJECT_Quarkus}"+++
    
    +++export AZ_POSTGRES_USERNAME="azuser123"+++
    
    +++export AZ_POSTGRES_PASSWORD="P@55w.rd12345"+++
    
    +++export AZ_POSTGRES_SERVER_NAME="psql${AZ_PROJECT_Quarkus}"+++
    

    ![A computer screen with text Description automatically generated](./media/image39.jpeg)

2.  Run   +++az login+++  It opens the default browser to sign in. Sign in with the User Credentials account (Resources Tab).

    ![A screenshot of a computer Description automatically generated](./media/image40.jpeg)

3.  Switch back to Gitbash and run below command to create a resource
    group in Azure Portal.

    
    +++az group create --name $AZ_RESOURCE_GROUP --location $AZ_LOCATION+++
    

    ![A computer screen with white text Description automatically generated](./media/image41.jpeg)

    > **Note:** If the command fails, it is because you did not change the location in the **AZ_LOCATION** variable (``export AZ_LOCATION="Location near to you"``). Try in westus location first and it you have any issues then try in location near to you.

### Task 2 : Create an instance of Azure Database for PostgreSQL

1.  You'll now create a managed PostgreSQL server. Run the following
    command to create a small instance of Azure Database for PostgreSQL:

    
    +++az postgres flexible-server create --resource-group "$AZ_RESOURCE_GROUP" --location "$AZ_LOCATION" --name "$AZ_POSTGRES_SERVER_NAME" --database-name "$AZ_POSTGRES_DB_NAME" --admin-user "$AZ_POSTGRES_USERNAME" --admin-password "$AZ_POSTGRES_PASSWORD" --public-access "All" --tier "Burstable" --sku-name "Standard_B1ms" --storage-size 32 --version "16"+++
    
    ![A screen shot of a computer code Description automatically generated](./media/image42.jpeg)

2.  This command creates a small PostgreSQL server that uses the
    variables that you set up earlier.

    ![A screenshot of a computer screen Description automatically generated](./media/image43.jpeg)

### Task 3 : Configure Quarkus to access the PostgreSQL database

1.  You'll now connect the Quarkus application to the PostgreSQL
    database. To do so, you first need to obtain the connection string
    for the database:

2.  Run below command to obtain the connection string for the database.


    +++export POSTGRES_CONNECTION_STRING=$(az postgres flexible-server show-connection-string --server-name "$AZ_POSTGRES_SERVER_NAME" --database-name "$AZ_POSTGRES_DB_NAME" --admin-user "$AZ_POSTGRES_USERNAME" --admin-password "$AZ_POSTGRES_PASSWORD" --query "connectionStrings.jdbc" --output tsv)+++

    +++export POSTGRES_CONNECTION_STRING_SSL="$POSTGRES_CONNECTION_STRING&ssl=true&sslmode=require"+++

    +++echo "POSTGRES_CONNECTION_STRING_SSL=$POSTGRES_CONNECTION_STRING_SSL"+++
    

    ![A computer screen with white text Description automatically generated](./media/image44.jpeg)

4.  Note the connection string that\\'s returned.

    ![A computer screen with white text Description automatically generated](./media/image45.jpeg)

### Task 4 :Configure the Quarkus application to connect to the PostgreSQL database

1.  Switch back to Intellij IDE. Update the **application.properties** file in the **src/main/resources** folder of the project to configure the
    connection string to the PostgreSQL database.

    ![A screenshot of a computer Description automatically generated](./media/image46.jpeg)

2. Add **quarkus.hibernate-orm.database.generation=update**.

3. Add connection string for the database previously output **$POSTGRES_CONNECTION_STRING_SSL** value.

4. At the end of the connection string add **&ssl=true&sslmode=require**. This forces the driver to use SSL, a requirement for Azure Database for PostgreSQL.

    ```java
    quarkus.hibernate-orm.database.generation=update
    
    quarkus.datasource.jdbc.url=<the POSTGRES_CONNECTION_STRING_SSL value\>&ssl=true&sslmode=require
    ```

    ![A screenshot of a computer Description automatically generated](./media/image47.jpeg)

### Task 5 : Run the Quarkus application locally to test the remote database connection

1.  Switch back to Gitbash and run below command to run the application
    locally:

    ```bash
    ./mvnw clean quarkus:dev
    ```

    ![A computer screen with text and images Description automatically generated](./media/image48.jpeg)


    ![A screenshot of a computer program Description automatically generated](./media/image49.jpeg)

    ![](./media/image50.jpeg)

2.  When Quarkus is running, create a few to-dos by using the following
    cURL commands in a separate terminal window:


    ```bash
    curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"Take Quarkus MS Learn","details":"Take the MS Learn on deploying Quarkus to Azure Container Apps","done": "true"}' \
    http://127.0.0.1:8080/api/todos
    ```

    ![A screenshot of a computer program Description automatically generated](./media/image51.jpeg)

    ```bash
    curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"Take Azure Container Apps MS Learn","details":"Take the ACA Learn module","done": "false"}' \
     http://127.0.0.1:8080/api/todos
    ```

    ![](./media/image52.jpeg)

3.  Next, check that the to-dos are in the database by accessing the GET
    endpoint that\\'s defined in the to-do app:

    ```bash
    curl http://127.0.0.1:8080/api/todos
    ```

    You should see the following output:

    ![A computer screen with white text Description automatically generated](./media/image53.jpeg) 

    If you see this output, you have successfully run the Quarkus application and connected to the remote PostgreSQL database.

## Exercise 3 : Deploy a Quarkus application to Azure Container Apps

In this exercise, you create the Azure Container Apps environment by
using the Azure CLI.

### Task 1 : Set up the Dockerfile for the Quarkus application

1.  Container Apps is used to deploy containerized applications. So you
    first need to containerize the Quarkus application into a Docker
    image. This process is easy because the Quarkus Maven plugin has
    already generated some Dockerfiles under **src/main/docker**.

    ![A screenshot of a computer Description automatically generated](./media/image54.jpeg)

2.  Switch back to Gitbash and press Ctrl+C .Run below command to rename
    one of
    these **Dockerfiles, *Dockerfile.jvm*,** to ***Dockerfile*** and
    move it to the root folder:

    \`\`mv src/main/docker/Dockerfile.jvm ./Dockerfile\`\`

    ![A black screen with white text Description automatically generated](./media/image55.jpeg)
    
    ![A screenshot of a computer Description automatically generated](./media/image54.jpeg)

3.  Replace the content after the long comment in
    the **Dockerfile** with the following i.e at Line \# 80

    ```text
    FROM registry.access.redhat.com/ubi8/openjdk-17:1.18
    
    ENV LANGUAGE='en_US:en'
    
    # We make four distinct layers so if there are application changes the library layers can be re-used
    COPY --chown=185 target/quarkus-app/lib/ /deployments/lib/
    COPY --chown=185 target/quarkus-app/\*.jar /deployments/
    COPY --chown=185 target/quarkus-app/app/ /deployments/app/
    COPY --chown=185 target/quarkus-app/quarkus/ /deployments/quarkus/

    EXPOSE 8080

    USER 185
    
    ENV JAVA_OPTS_APPEND="-Dquarkus.http.host=0.0.0.0 -Djava.util.logging.manager=org.jboss.logmanager.LogManager"

    ENV JAVA_APP_JAR="/deployments/quarkus-run.jar"
    
    ENTRYPOINT \[ "/opt/jboss/container/java/run/run-java.sh" \]
    ```

    ![A screenshot of a computer Description automatically generated](./media/image56.jpeg)

4.  This Dockerfile expects the Quarkus application to be packaged as
    a ***quarkus-run.jar* file**. This name is the default name for the
    Quarkus application when it\\'s packaged as a JAR file. You need to
    make sure that the Quarkus application is packaged as a JAR file. To
    do so, run the following Maven command:

    ```bash
    ./mvnw package
    ```

    ![A computer screen with white text Description automatically generated](./media/image57.jpeg)

    ![](./media/image58.jpeg)

5.  This command packages the Quarkus application into a JAR file and
    generates a ***quarkus-run.jar*** file in
    the ***target/quarkus-app*** folder.

    ![A screenshot of a computer Description automatically generated](./media/image59.jpeg)

### Task 2 : Create the Container Apps environment and deploy the container

1.  Now that the Dockerfile is in the right location, you can create the
    Container Apps environment and deploy the container by using a
    single Azure CLI command. Run the following command at the root of
    the project:

    ```bash
    az containerapp up --name "$AZ_CONTAINERAPP" --environment "$AZ_CONTAINERAPP_ENV" --location "$AZ_LOCATION" --resource-group "$AZ_RESOURCE_GROUP" --ingress external --target-port 8080 --source .
    ```

    ![A screenshot of a computer program Description automatically generated](./media/image60.jpeg)
    
    ![A screenshot of a computer program Description automatically generated](./media/image60.jpeg)
    
    ![A screenshot of a computer program Description automatically generated](./media/image61.jpeg)
    
    ![A screenshot of a computer Description automatically generated](./media/image62.jpeg)
    
2.  This command does several things:

    - Creates a Container Apps environment if it doesn't exist

    - Creates an Azure registry if it doesn't exist

    - Creates a Log Analytics workspace if it doesn't exist

    - Builds the Docker image and pushes it to the Azure registry

    - Deploys the Docker image to the Container Apps environment

    The az containerapp up command takes some time to run. You should see output that's similar to the following:

    ![A computer screen with white text Description automatically generated](./media/image63.jpeg)

### Task 3 : Validate the deployment

You can validate that the deployment has succeeded in several ways. The
easiest way is to search for your resource group on the Azure portal.
You should see resources similar to the following:

1.  Open a browser and go to ``https://portal.azure.com/`` and sign
    in with your Azure subscription account. Click on Resource Group
    tile.

    ![A screenshot of a computer Description automatically generated](./media/image64.jpeg)

2.  Click on the resource group name.

    ![A screenshot of a computer Description automatically generated](./media/image65.jpeg)
    
    ![A screenshot of a computer Description automatically generated](./media/image66.jpeg)

3.  You can also check the deployment by running the following command.
    It lists all the resources created by the az containerapp up
    command.

    ```bash
    az resource list --location "$AZ_LOCATION" --resource-group "$AZ_RESOURCE_GROUP" --output table
    ```

    You should see output that's similar to this:

    ![A screenshot of a computer program Description automatically generated](./media/image67.jpeg)

### Task 4 : Run the deployed Quarkus application

1.  You can now run the deployed Quarkus application. First, you need to
    get the URL of the application.

2.  Switch back to Gitbash and run below command to get the URL of the
    application.

    
    +++export AZ_APP_URL=$(az containerapp show --name "$AZ_CONTAINERAPP" --resource-group "$AZ_RESOURCE_GROUP" --query "properties.configuration.ingress.fqdn" --output tsv)+++

    +++echo "AZ_APP_URL=$AZ_APP_URL"+++
    

    ![A computer screen with white text Description automatically generated](./media/image68.jpeg)

3.  Your application is ready at
    https://\\\<app-name\>.azurecontainerapps.io/. Notice the https
    protocol. That protocol is used because the application is deployed
    with a TLS certificate. To test the application, you can use cURL:

    ```bash
    curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"Configuration","details":"Congratulations, you have set up your Quarkus application correctly!","done": "true"}' \
    https://$AZ_APP_URL/api/todos
    ```

    ![A computer screen with white text Description automatically generated](./media/image69.jpeg)

4.  Retrieve the data by using a new cURL request:

    +++curl https://$AZ_APP_URL/api/todos+++


5.  This command returns the list of all to-do items from the database:

    ![A computer screen with white text Description automatically generated](./media/image70.jpeg)

6.  Switch back to the Azure portal and click on your container app name.

    ![A screenshot of a computer Description automatically generated](./media/image71.jpeg)

7.  Click on the application url link. It opens app in browser tab.

    ![A screenshot of a computer Description automatically generated](./media/image72.jpeg)

    ![A screenshot of a computer Description automatically generated](./media/image73.jpeg)

8.  Run this command, you can stream the logs for your container when
    you create new to-dos:

    +++az containerapp logs show --name "$AZ_CONTAINERAPP" --resource-group "$AZ_RESOURCE_GROUP" -–follow+++


    ![A computer screen with white text Description automatically generated](./media/image74.jpeg)

9.  Run more cURL commands. You should see the logs scrolling in the
    terminal.

    +++curl https://$AZ_APP_URL/api/todos+++


    ![A computer screen with white text Description automatically generated](./media/image75.jpeg)

## Exercise 4 : Delete resources

### Task 1 : Delete resources.

1.  Switch back to your Gitbash and run below command to delete resource
    goup

    
    +++az group delete --name $AZ_RESOURCE_GROUP --yes+++

    ![A black screen with yellow text Description automatically generated](./media/image76.jpeg)

**Summary**

You learnt how to use Maven to bootstrap the application and an integrated development environment (IDE) to edit the code.. You learnt
how to use Docker to start a local PostgreSQL database so you can run and test the application locally. You have successfully run the Quarkus application and connected to the remote PostgreSQL database.
