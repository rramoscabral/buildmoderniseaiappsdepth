**Usecase 08 - Building and deploying Contoso Real Estate chat app to
support customers.**

**Objective**

This usecase demonstrates a few approaches for creating ChatGPT-like
experiences over your own data using the Retrieval Augmented Generation
pattern. It uses Azure OpenAI Service to access the ChatGPT model
(gpt-35-turbo), and Azure AI Search for data indexing and retrieval.

![Retrieval Augmented Generation Architecture](./media/image1.png)

The usecase includes sample data so it's ready to try end to end. In
this sample application we use a fictitious company called Contoso Real
Estate, and the experience allows its customers to ask support questions
about the usage of its products. The sample data includes a set of
documents that describe its terms of service, privacy policy and a
support guide.

The application is made from multiple components, including:

- **Search service**: the backend service that provides the search and
  retrieval capabilities.

- **Indexer service**: the service that indexes the data and creates the
  search indexes.

- **Web app**: the frontend web application that provides the user
  interface and orchestrates the interaction between the user and the
  backend services.

![App Architecture](./media/image2.png)

- Chat and Q&A interfaces

- Explores various options to help users evaluate the trustworthiness of
  responses with citations, tracking of source content, etc.

- Shows possible approaches for data preparation, prompt construction,
  and orchestration of interaction between model (ChatGPT) and retriever
  (Azure AI Search)

- Settings directly in the UX to tweak the behavior and experiment with
  options

- Optional performance tracing and monitoring with Application Insights

**Key technologies used** – Azure OpenAI Service, ChatGPT model
(gpt-35-turbo), and Azure AI Search

**Estimated duration –** 40 minutes

## Exercise 1 : Deploy the application and test it from the browser

## Task 1: Open development environment

1.  Open your browser, navigate to the address bar, type or paste the
    following URL:
    +++<https://github.com/technofocus-pte/azure-search-openai-javascript>+++
    and sign in with your Github account.

![A screenshot of a computer Description automatically
generated](./media/image3.png)

2.  Click on **Fork**.

![](./media/image4.png)

3.  Enter the repository name and then click on **Create fork**.

![](./media/image5.png)

4.  Click on **Code -\> Codespaces -\> +**

![](./media/image6.png)

5.  Wait for the environment to setup. It takes 5-10 minutes.

![A screenshot of a computer Description automatically
generated](./media/image7.png)

![A screenshot of a computer Description automatically
generated](./media/image8.png)

## Task 2: Deploy chat app to Azure

1.  Sign in to Azure with the Azure Developer CLI. Run the following
    command on the Terminal. Copy the code and press enter

> **+++azd auth login+++**

![](./media/image9.png)

2.  Default browser opens to sign in. Sign in with your Azure
    subscription account.

![](./media/image10.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image11.png)
>
> ![A screenshot of a login page Description automatically
> generated](./media/image12.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image13.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image14.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image15.png)

3.  Run below command to command to initialize a git repository. Enter
    ‘Y’ when asked” Continue initializing an app in
    '/workspaces/azure-search-openai-javascript'?”

+++azd init -t azure-search-openai-javascript+++

![](./media/image16.png)

4.  To create an environment for Azure resources, run the following
    Azure Developer CLI command.It asks you to enter environment name
    .Enter any name of your choice and press enter (eg
    :+++**ragpgpy+++**)

**Note:** When creating an environment, ensure that the name consists of
lowercase letters.

![A screenshot of a computer Description automatically
generated](./media/image17.png)

5.  Run the following Azure Developer CLI command to provision the Azure
    resources and deploy the code.

> **+++azd up+++**

![A screenshot of a computer Description automatically
generated](./media/image18.png)

6.  When prompted, select a **subscription** to create the resources and
    select the region closest to your location; in this lab, we have
    chosen the **East US2** region.

![](./media/image19.png)

7.  When prompted, **enter a value for the ‘openAILocation’
    infrastructure parameter** select the region closest to your
    location; in this lab, we have chosen the **North Central US**
    region

![](./media/image20.png)

8.  Deployment will take around 15 - 25 min. While the deployment is
    going on, You can go next Task 3 and verify deployed resources.

![A screenshot of a computer Description automatically
generated](./media/image21.png)

![](./media/image22.png)

9.  Deployment completed and front end hosted successfully. Click on the
    generated webapp URL

![A screenshot of a computer Description automatically
generated](./media/image23.png)

![](./media/image22.png)

![A screenshot of a computer Description automatically
generated](./media/image24.png)

## Task 3: Verify deployed resources in the Azure portal

1.  On Home page of Azure portal, click on **Resource Groups**.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

2.  Click on your resource group name

![](./media/image26.png)

3.  Make sure the below resource got deployed successfully

- Container App

- AI Search

- Application Insights

- Container Apps Environment

- Log Analytics workspace

- Azure OpenAI

- Container registry

> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

4.  Click on **Azure OpenAI** resource name.

![](./media/image28.png)

5.  On **Overview** in the left navigation menu, right click **Go to
    Azure OpenAI Studio** and select to open a new tab.

![](./media/image29.png)

6.  Click on **Deployments** from left navigation menu and make sure
    **gpt-35-turbo**, **text-embedding-ada-002** should be deployed
    successfully

![A screenshot of a computer Description automatically
generated](./media/image30.png)

## Task 5: Clean up all the resources

To clean up all the resources created by this sample:

1.  Goback Visual Studio terminal and run +++**azd down –purge+++**

![](./media/image31.png)

2.  When asked if you are sure you want to continue, enter y

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

3.  When asked if you want to permanently delete the resources,
    enter **y**

![A screenshot of a computer Description automatically
generated](./media/image33.png)

**Summary**:

This use case thought you , deploying a chat application for the
Retrieval Augmented Generation pattern running on Azure, using Azure AI
Search for retrieval and Azure OpenAI and LangChain large language
models (LLMs) to power ChatGPT-style and Q&A experiences