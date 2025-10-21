# AI Ready Manage hands-on exercise: Create a hub-level service connection (shared to all projects)

## Background information
Connections let you access objects in Azure AI Foundry portal that are managed outside of your hub. For example, they might be used for retrieving data uploaded to an Azure Storage account, leveraging an external Azure AI Search index for Retrieval Augmented Generation, or connecting to model deployments on another AI Foundry resource. Connections support storing shared credentials, so developers can access remote objects during development without having to authenticate or request authorization. 
After connections to Azure AI Foundry are created, model deployments are accessible via playground experiences. When using fine-tuning experiences in a hub-based project, fine-tuning jobs are implicitly executed on the connected AI Foundry resource.

## Scenario
Your company is developing an enterprise AI platform using Azure AI Foundry to improve consistency, visibility, and coordination across multiple AI projects. As part of the platform's setup, your company will configure connections to integrate key Azure services with the hub and its projects. An Azure AI Search service will be connected at the hub level, allowing all projects within the hub to access a shared search index for retrieval-augmented generation and other search-driven use cases. In contrast, an Azure Blob Storage account will be connected at the project level, ensuring that its data remains accessible only to that specific project. This configuration will leverage shared and project-specific resources to optimize management of hub-based projects in Azure AI Foundry.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).

## Estimated duration
20 minutes

### Task 1: Create Azure AI Foundry hub and hub-based projects

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, select the **AI hub resource** option and then select **Next**.
1. On the **Create a new project** pane, perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **hub-connections-project**).
   - In the **Hub** drop-down list, select **Create a new hub**.
   - Select the link **Rename hub** on top of the **Hub** text box, enter an arbitrary hub name (we will name it **connections-hub**) and expand the **Advanced options** section.
   - In the **Advanced options** section, in the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select the link **Create new resource group**, in the **Create new resource group** text box, enter **connections-hub-RG** and select **OK**.
   - Select the link **Create new AI Foundry**, in the **Create new AI Foundry** text box, enter **ai-connections-hub** and select **OK**.
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the hub and project resources.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the project's **Overview** page.

### Task 2: Review pre-created connections in the Azure AI Foundry hub

1. In the web browser displaying the Azure AI Foundry portal, on the **Overview** page of **hub-connections-project**, in the vertical menu on the left side, select **Management center**. 
1. On the **Management center** page, on the **hub-connections-project** pane, in the vertical menu on the left side, in the **Hub (connections-hub)** section, select **Connected resources**. 
1. On the **Manage connected resources in this hub** pane, review the list of connections created automatically when you provisioned the hub earlier in this exercise. They should include connections to **Azure OpenAI** via an API key, **Azure AI Foundry** via an API key, and **Azure Blob Storage** via a Shared Access Signature (SAS) key. 

   **Note**: There are two pre-created connections to Azure Blob Storage. The blob artifact store connection is a specialized connection to an underlying blobstore (which serves as all-purpose storage service for binary data). Its purpose is to store AI-specific artifacts produced during model development, such as model checkpoints and weights, training and evaluation datasets, experiment logs and metrics, as well as intermediate files from Prompt Flows.

   **Note**: The pre-created connections to **Azure OpenAI** and **Azure AI Foundry** are shared to all projects in the same hub. The two pre-created connections to Azure Blob Storage are for exclusive use of the current project. 

1. On the **Management center** page, in the vertical menu on the left side, in the **Project (hub-connections-project)** section, select **Connected resources**. 
1. On the **Manage connected resources in this project** pane, review the list of connections created automatically when you provisioned the project earlier in this exercise. They should include the same connection as those you reviewed earlier on the **Manage connected resources in this hub** pane.

### Task 3: Provision Azure resources for custom connections from the Azure AI Foundry hub

1. Open another tab in the web browser displaying the Azure AI Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **AI Search** and, in the list of results, select **AI Search**.
1. On the **AI Foundry \| AI Search** page, select **+ Create**.
1. On the **Basics** tab of the **Create a search service** page, specify the following settings and select **Next: Scale**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**connections-hub-RG**|
   |Service name|**hub-connections-aisearch**|
   |Region|The name of the Azure region where you created the Azure AI Foundry hub|
   |Pricing tier|**Free**|
 
1. On the **Scale** tab of the **Create a search service** page, accept the default settings and then select **Review + create**. 
1. On the **Review + create** tab, select **Create**.

   **Note**: Do not wait until the resource is provisioned but instead proceed to the next step in this task. The provisioning might take about 3 minutes. 

1. In the Azure portal, use the **Search** text box at the top of the page to search for **Storage accounts** and, in the list of results, select **Storage accounts**.
1. On the **Storage accounts \| Storage accounts (Blobs)** page, select **+ Create**.
1. On the **Basics** tab of the **Create storage account** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**connections-hub-RG**|
   |Storage account name|Any globally unique name between 3 and 24 in length consisting of lower case letters and digits, starting with a letter|
   |Region|The name of the Azure region where you created the Azure AI Foundry hub|
   |Preferred storage type|**Azure Blob Storage or Azure Data Lake Storage Gen 2**|
   |Performance|**Standard**|
   |Redundancy|**Locally redundant storage (LRS)**|

   >**Note**: Record the storage account name. You'll need it later in this exercise.

1. On the **Advanced** tab, ensure that the **Enable storage account key access** checkbox is checked and then select **Next**.
1. On the **Networking** tab, ensure that the **Public network access** option is set to **Enable**, the **Public network access type** is set to **Enable from all networks**, and then select **Review + create**.
1. On the **Review + create** tab, wait for the validation process to complete, and then select **Create**.

   >**Note**: Wait for the Storage account provisioning to complete. This should take less than one minute.

1. On the deployment page, select **Go to resource**.
1. On the Storage account page, in the vertical menu on the left side, in the **Data storage** section, select **Containers**.
1. On the Storage account **Container** page, select **+ Add container**.
1. On the **New container** pane, in the **Name** text box, enter **ai-artifacts** and select **Create**.

   >**Note**: The **Anonymous access level** should be set to **Private (no anonymous access)**.

1. Back on the Storage account **Container** page, in the vertical menu on the left side, in the **Security + networking** section, select **Access keys**.
1. On the Storage account **Access key** page, in the **key1** section, next to the **Key** label, select **Show**, and then select the **Copy** icon.
1. On your computer, launch Notepad and paste into it the key value you copied in the previous step.

   >**Note**: You'll need the key value later in this exercise.

### Task 4: Create a shared hub-level service connection

1. Switch to the web browser tab displaying the **Manage connected resources in this project** pane in the Azure AI Foundry portal.
1. On the **Manage connected resources in this project** pane, in the vertical menu on the left side, in the **Hub (connections-hub)** section, select **Connected resources**. 
1. On the **Manage connected resources in this hub** pane, select **+ New connection**.
1. On the **Add a connection to external assets** pane, select **Azure AI Search**.
1. On the **Connect an Azure AI Search resource** pane, ensure that the **Browse resources** option is selected, verify that the Azure AI Search resource you provisioned in the previous task is displayed in the **Displaying resources** section, ensure that the **API key** entry appears in the **Authentication** drop-down list and select **Add connection**.
1. Once the Azure AI Search resource is listed as connected, select **Close**.
1. On the **Manage connected resources in this hub** pane, review the updated list of connections and confirm that the connection to the Azure AI Search resource is listed as **Shared to all projects**. 

### Task 5: Create a project-level service connection

1. In the Azure portal displaying the **Manage connected resources in this hub** pane, in the vertical menu on the left side, in the **Project (hub-connections-project)** section, select **Connected resources**. 
1. On the **Manage connected resources in this project** pane, select **+ New connection**.
1. On the **Add a connection to external assets** pane, in the **Data** section, select **Azure Blob Storage**.
1. On the **Add a connection to external assets** pane, specify the following settings (leave others with their default values) and select **Create connection**:

   |Setting|Value|
   |---|---|
   |Service|**Azure Blob Storage**|
   |Manually enter account information|Disabled|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Storage account|The name you assigned to the Storage account you created earlier in this exercise|
   |Blob container|**ai-artifacts**|
   |Authentication method|**Credential based**|
   |Authentication type|**Account key**|
   |Account key|The value of the key you copied earlier in this exercise|
   |Connection name|The name you assigned to the Storage account you created earlier in this exercise|

1. Back on the **Manage connected resources in this project** pane, review the updated list of connections and confirm that the connection to the Storage account resource is listed as **hub-connections-project-only**. 

### Task 6: Perform cleanup

1. Open another tab in the web browser displaying the Azure AI Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **connections-hub-RG** and, in the list of results, select **connections-hub-RG**.
1. On the **connections-hub-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **connections-hub-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.