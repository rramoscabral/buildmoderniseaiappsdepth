# Usecase 06 - Deploying chat app on Azure Container Apps with PostgreSQL Flexible Server

**Objective:**

- To configure the development environment on Windows by installing
  Azure CLI, Node.js, assigning Azure subscription roles, starting
  Docker Desktop, and enabling Visual Studio Code with Dev Containers
  extension.

- To deploy and test Custom Chat Application with PostgreSQL and OpenAI
  on Azure.

![A screenshot of a computer Description automatically
generated](./media/image1.jpeg)

In this use case, you will set up a comprehensive development
environment, deploy a chat application integrated with PostgreSQL, and
verify its deployment on Azure. This involves installing essential tools
like Azure CLI, Docker, and Visual Studio Code ( we have already done it
for you on host env ), configuring user roles in Azure, deploying the
application using Azure Developer CLI, and interacting with the deployed
resources to ensure functionality.

**Key technologies used** -- Python, FastAPI, Azure OpenAI models, Azure
Database for PostgreSQL and azure-container-apps,ai-azd-templates.

**Estimated duration** -- 45 minutes

**Lab Type:** Instructor Led

**Pre-requisites:**

GitHub account -- You are expected to have your own GitHub login
credentials. If you do not have, please create one from here

`https://github.com/signup?user_email=&source=form-home-signupobjectives`

## Exercise 1: Set up environment

### Task 1: Install Azure Cli and set the policy scope to Local machine

1.  In your windows search bar, search for `PowerShell`. Open
    as **Run as administrator**. If you see the dialog box - **Do you
    want to allow this app to make changes to your device?** then click
    on the **Yes** button.

![A screenshot of a computer Description automatically
generated](./media/image2.jpeg)

![A screenshot of a computer error Description automatically
generated](./media/image3.jpeg)

2.  Run below commands to install stable version of winget.

`$progressPreference = 'silentlyContinue'`

`Write-Information "Downloading WinGet and its dependencies..."`

`Invoke-WebRequest -Uri https://aka.ms/getwinget -OutFile Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle`

`Invoke-WebRequest -Uri https://aka.ms/Microsoft.VCLibs.x64.14.00.Desktop.appx -OutFile Microsoft.VCLibs.x64.14.00.Desktop.appx`

`Invoke-WebRequest -Uri  https://github.com/microsoft/microsoft-ui-xaml/releases/download/v2.8.6/Microsoft.UI.Xaml.2.8.x64.appx -OutFile Microsoft.UI.Xaml.2.8.x64.appx`

`Add-AppxPackage Microsoft.UI.Xaml.2.8.x64.appx`

`Add-AppxPackage Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle`

![A blue screen with white text Description automatically
generated](./media/image4.jpeg)

3.  Run the following command to install Azure Cli on the PowerShell

    `winget install microsoft.azd`

![A screenshot of a computer Description automatically
generated](./media/image5.jpeg)

4.  Run the below command to set the policy to **Unrestricted** and
    enter **A** when asked to change the execution policy.

    `Set-ExecutionPolicy Unrestricted`

![A computer screen with white text Description automatically
generated](./media/image6.jpeg)

### Task 2: Assign a user as an owner of an Azure subscription

1.  Open your browser, open Azure
    portal `https://portal.azure.com`.  Sign in with your Azure
    subscription account.

![A screenshot of a computer Description automatically
generated](./media/image7.jpeg)

2.  On Home page, click on **Subscriptions** tile.

![A screenshot of a computer Description automatically
generated](./media/image8.jpeg)

3.  Click on your **subscription name**.

![A screenshot of a computer Description automatically
generated](./media/image9.jpeg)

4.  From the left menu, click on the **Access control(IAM).Click o
    Add -> Add role assignment**

![A screenshot of a computer Description automatically
generated](./media/image10.jpeg)

5.  In the Role tab, select the **Privileged administrator roles** and
    select **Owner** . Click **Next**

![A screenshot of a computer Description automatically
generated](./media/image11.jpeg)

6.  Select Assign access to User group or service principal.
    Under **Members**, click **+Select members** and search for your
    Azure subscription account, select it and then click
    on **Select** button.

![A screenshot of a computer Description automatically
generated](./media/image12.jpeg)

7.  Click on **Next**.

![A screenshot of a computer Description automatically
generated](./media/image13.jpeg)

8.  Select **Allow user to assign all roles** radio button and then
    click on **Review +assign** .

![A screenshot of a computer screen Description automatically
generated](./media/image14.jpeg)

### Task 3: Install Dev Containers extension

1.  In your Windows search box, type `Visual Studio`, then click
    on **Visual Studio Code**.You can also open it form **Desktop**.

![A screenshot of a computer Description automatically
generated](./media/image15.jpeg)

2.  Click on **Extensions** , search for **Dev container**, select it
    and click on **Install**.

![A computer screen shot of a computer Description automatically
generated](./media/image16.jpeg)

## Exercise 2: Deploy the application and test it from the browser

### Task 1 : Run the Docker

1.  On the Desktop, double click on **Docker Desktop**.

![A screenshot of a computer Description automatically
generated](./media/image17.jpeg)

2.  Run the Docker Desktop.

![A screenshot of a computer Description automatically
generated](./media/image18.jpeg)

### Task 2 : Register Service provider

1.  Open a browser and go to `https://portal.azure.com` and sign
    in with your Azure subscription account.

2.  Click on the **Subscription** tile.

![A screenshot of a computer Description automatically
generated](./media/image19.jpeg)

3.  Click on subscription name.

![A screenshot of a computer Description automatically
generated](./media/image20.jpeg)

4.  Click on Resource provider from left navigation menu,
    type `Microsoft.AlertsManagement` and press enter. Select it
    and then click on **Register**.

![](./media/image21.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image22.jpeg)

### Task 3: Open development environment

1.  Open your browser, navigate to the address bar, type or paste the
    following
    URL: `https://github.com/technofocus-pte/rag-postgres-openai-python.git` tab
    opens and ask you to open in Visual studio code. Select **Open
    Visual Studio Code.**

![](./media/image23.jpeg)

2.  Click on **fork** to fork the repo. Give unique name to the repo and
    click on **Create repo** button.

![A screenshot of a computer Description automatically
generated](./media/image24.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image25.jpeg)

3.  Click on **Code -> Codespaces -> codespace+**

![A screenshot of a computer Description automatically
generated](./media/image26.jpeg)

4.  Wait for the codespace environment to setup .It takes few minutes to
    setup completely

![A screenshot of a computer Description automatically
generated](./media/image27.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image28.jpeg)

### Task 4: Deploy chat app to Azure

1.  Sign in to Azure with the Azure Developer CLI. Run the following
    command on the Terminal

`azd auth login`

![A screenshot of a computer Description automatically
generated](./media/image29.jpeg)

2.  Default browser opens to sign in. Sign in with your Azure
    subscription account.

![A screenshot of a computer Description automatically
generated](./media/image30.jpeg)

![A screenshot of a sign in Description automatically
generated](./media/image31.jpeg)

![A screenshot of a login page Description automatically
generated](./media/image32.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image33.jpeg)

3.  To create an environment for Azure resources, run the following
    Azure Developer CLI command.
    
    It asks you to enter environment name
    .Enter any name of your choice and press enter (eg : `ragpgpy`)

**Note:** When creating an environment, ensure that the name consists of
lowercase letters.

`azd env new`

![A screenshot of a computer Description automatically
generated](./media/image34.jpeg)

4.  Run the following Azure Developer CLI command to provision the Azure
    resources and deploy the code.

`azd up`

![A screen shot of a computer Description automatically
generated](./media/image35.jpeg)

5.  When prompted, select a **subscription** to create the resources and
    select the region closest to your location; in this lab, we have
    chosen the **East US2** region.

![A screenshot of a computer Description automatically
generated](./media/image36.jpeg)

6.  When prompted, **enter a value for the 'openAILocation'
    infrastructure parameter** select the region closest to your
    location; in this lab, we have chosen the **North central US** region

![A screenshot of a computer Description automatically
generated](./media/image37.jpeg)

7.  Deployment will take around 19-20 min. While the deployment is going
    on, You can go next Task 3 and verify deployed resources.

![A screenshot of a computer Description automatically
generated](./media/image38.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image39.jpeg)

8.  Deployment completed and front end hosted successfully. Click on the
    generated URL

![A screenshot of a computer Description automatically
generated](./media/image40.jpeg)

![A screenshot of a chat Description automatically
generated](./media/image41.jpeg)

### Task 5: Verify deployed resources in the Azure portal

1.  On Home page of Azure portal, click on **Resource Groups**.

![A screenshot of a computer Description automatically
generated](./media/image42.jpeg)

2.  Click on your resource group name

![A screenshot of a computer Description automatically
generated](./media/image43.jpeg)

3.  Make sure the below resource got deployed successfully

    - Container App

    - Application Insights

    - Container Apps Environment

    - Log Analytics workspace

    - Azure OpenAI

    - Azure Database for PostgreSQL flexible server

    - Container registry

![A screenshot of a computer Description automatically
generated](./media/image44.jpeg)

4.  Click on **Azure OpenAI** resource name.

![A screenshot of a computer Description automatically
generated](./media/image45.jpeg)

5.  On **Overview** in the left navigation menu, right click **Go to
    Azure OpenAI Studio** and select to open a new tab.

![A screenshot of a computer Description automatically
generated](./media/image46.jpeg)

6.  Click on **Deployments** from left navigation menu and make
    sure **gpt-35-turbo**, **text-embedding-ada-002** should be deployed
    successfully

![A screenshot of a computer Description automatically
generated](./media/image47.jpeg)

### Task 6: Use chat app to get answers from files

1.  In the **RAG on database |OpenAI+PoastgreSQL** web app page, **click
    on Best shoe for hiking?** button and observe the output

![A screenshot of a chat Description automatically
generated](./media/image48.jpeg)

![A screenshot of a chat Description automatically
generated](./media/image49.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image50.jpeg)

2.  Click on the **clear chat.**

![A screenshot of a computer Description automatically
generated](./media/image51.jpeg)

3.  In the **RAG on database |OpenAI+PoastgreSQL** web app page, click
    on **Climbing gear cheaper than \\$30** button and observe the
    output

![A screenshot of a chat Description automatically
generated](./media/image52.jpeg)

![A screenshot of a computer Description automatically
generated](./media/image53.jpeg)

4.  Click on the **clear chat.**

### Task 7: Clean up all the resources

To clean up all the resources created by this sample:

1.  Go back Visual Studio terminal and run `azd down –purge`

![A close-up of a computer code Description automatically
generated](./media/image54.jpeg)

2.  When asked if you are sure you want to continue, enter y

![A screenshot of a computer Description automatically
generated](./media/image55.jpeg)

3.  When asked if you want to permanently delete the resources,
    enter **y**

![A screenshot of a computer Description automatically
generated](./media/image56.jpeg)

**Summary:**

This use case walks you through deploying a chat application with
PostgreSQL and OpenAI on Azure, focusing on cloud-based application
deployment and management. you’ve set up the development environment,
installed necessary tools like Azure CLI, configured Azure resources
using Azure Developer CLI, and deployed the application to Azure
Container Apps.
