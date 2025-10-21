# AI Ready Manage hands-on exercise: Create a project level service connection

## Background information
When a new Azure AI Foundry project is created, it automatically has access to a set of platform-managed resources that are required for core functionality, such as temporary compute for notebooks and training jobs, default artifact storage for model checkpoints, datasets, experiment logs, and intermediate files, and built-in playground/model experiences. These resources are managed internally by Azure AI Foundry and do not appear in the project's Connected Resources pane in the Azure AI Foundry portal, nor do they require explicit connections to use. 

Explicit connections in an Azure AI Foundry project allow the project to access cloud resources that are scoped specifically to that project. For example, a project can connect to an Azure Storage account to retrieve datasets or store artifacts, or to model deployments hosted by another AI Foundry resource. Connections store credentials securely, and the required authentication and authorization methods depend on the resource type.

Many Azure resources support Microsoft Entra ID authentication, which offers secure, role-based access without sharing keys or secrets. When a project initiates a job—such as a training run or data processing workflow—Azure AI Foundry can use a managed identity to authenticate automatically to the connected resource, eliminating the need for user credentials. Users accessing the same resource interactively (for example, in a notebook or playground) use their personal Entra ID credentials, with access permissions enforced by Azure roles.

Once a project-level connection is established, external resources such as datasets or models can be used directly within the project workspace. Any fine-tuning or model deployment jobs executed in the project implicitly use the connection and the appropriate authentication method, either the managed identity for automated tasks or the user identity for interactive sessions.

## Scenario
Your company is developing an enterprise AI platform using Azure AI Foundry projects to manage multiple AI workloads independently. As part of the platform's setup, your company will configure a project-level connection to an Azure Storage account. This connection will use Microsoft Entra ID authentication, relying on the project's system-assigned managed identity to securely access the storage account without relying on user credentials. The managed identity will be used to authorize automated operations initiated by the project, including reading training datasets, writing model artifacts, and logging experiment results. Users accessing the project interactively, for example in a notebook or playground session, will authenticate via their individual Entra ID credentials, with access permissions enforced by the storage account's role assignments. This configuration will ensure that the project can securely leverage Azure Storage for its workflows, while keeping the storage account scoped exclusively to that project and fully managed via Entra ID authentication.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).
- **Familiarity with Azure Storage authorization**: To learn more, refer to [Prevent Shared Key authorization for an Azure Storage account](https://learn.microsoft.com/en-us/azure/storage/common/shared-key-authorization-prevent?tabs=portal).
- **Familiarity with Azure Role-Based Access Control (RBAC)**: To learn more, refer to [Azure RBAC documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/).

## Estimated duration
15 minutes

### Task 1: Create Azure AI Foundry project

1. Start a web browser, navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. On the **All resources** page of the Azure AI Foundry portal, select **Create new**.
1. On the **Create project** pane, ensure that the **Azure AI Foundry resource** option is selected and then select **Next**.
1. On the **Create a project** pane, expand the **Advanced options** section and perform the following tasks:

   - In the **Project** text box, enter an arbitrary project name (we will name it **foundry-connections-project**).
   - In the **Subscription** drop-down list, select the subscription you are using in this exercise.
   - Select **Create new resource group** link, in the **Create new resource group** text box, enter **foundry-connections-project-RG** and select **OK**.
   - Accept the default value of the **Azure AI Foundry resource** (**foundry-connections-project-resource**).
   - In the **Region** drop-down list, select the Azure region in which you intend to provision the project resource.
   - Select **Create**.

   **Note**: Wait until the resource is provisioned. This might take about one minute. Once the project is created, the web browser should display the project's **Overview** page.

### Task 2: Review the list of connected resources of the Azure AI Foundry project

1. In the web browser displaying the Azure AI Foundry portal, on the **Overview** page of **foundry-connections-project**, in the vertical menu on the left side, in the **Resource** section, select **Connected resources**. 
1. On the **Manage connected resources in this project** pane, verify that there are no pre-created connections.
1. In the web browser displaying the Azure AI Foundry portal, on the **Overview** page of **foundry-connections-project**, in the vertical menu on the left side, in the **Project** section, select **Connected resources**. 
1. On the **Manage connected resources in this project** pane, verify that there are no pre-created connections.

   **Note**: For Azure AI Foundry projects, connections to resources such as Azure AI Foundry and Azure Storage are managed by the platform and not explicitly listed in the interface.

### Task 3: Provision an Azure resource for a custom connection from the Azure AI Foundry project

1. Open another tab in the web browser displaying the Azure AI Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Storage accounts** and, in the list of results, select **Storage accounts**.
1. On the **Storage accounts \| Storage accounts (Blobs)** page, select **+ Create**.
1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**foundry-connections-project-RG**|
   |Storage account name|Any globally unique name between 3 and 24 in length consisting of lower case letters and digits, starting with a letter|
   |Region|The name of the Azure region where you created the Azure AI Foundry project|
   |Preferred storage type|**Azure Blob Storage or Azure Data Lake Storage Gen 2**|
   |Performance|**Standard**|
   |Redundancy|**Locally redundant storage (LRS)**|

   >**Note**: Record the storage account name. You'll need it later in this exercise.

1. On the **Advanced** tab, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Enable storage account key access|**Disabled**|

1. On the **Networking** tab, ensure that the **Public network access** option is set to **Enable**, the **Public network access type** is set to **Enable from all networks**, and then select **Review + create**.
1. On the **Review + create** tab, wait for the validation process to complete, and then select **Create**.

   >**Note**: Wait for the Storage account provisioning to complete. This should take less than one minute.

1. On the deployment page, select **Go to resource**.
1. On the Storage account page, in the vertical menu on the left side, in the **Data storage** section, select **Containers**.
1. On the Storage account **Container** page, select **+ Add container**.
1. On the **New container** pane, in the **Name** text box, enter **ai-artifacts** and select **Create**.

   >**Note**: The **Anonymous access level** should be set to **Private (no anonymous access)**.

1. Back on the Storage account **Container** page, in the vertical menu on the left side, select **Access Control (IAM)**.
1. On the storage account's **Access Control (IAM)** page, select the **+ Add** and, in the drop-down menu, select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, under the **Job function roles** listing, in the **Search** text box, enter **Storage Blob Data Contributor**, in the list of search results, select **Storage Blob Data Contributor**, and select **Next**.
1. On the **Members** tab of the **Add role assignment** page, in the **Assign access to** section, select **Managed identity** and then click **+ Select members**. 
1. On the **Select members** pane, enter the name of the Azure AI Foundry project you created earlier in this exercise (**foundry-connections-project**), select the project from the list of results, and then click **Select**.
1. Back on the **Members** tab of the **Add role assignment** page, select **Next**.
1. On the **Conditions** tab of the **Add role assignment** page, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

### Task 4: Create an Azure AI Foundry project connection

1. Switch to the web browser tab displaying the **Manage connected resources in this project** pane in the Azure AI Foundry portal.
1. On the **Manage connected resources in this project** pane, select **+ New connection**.
1. On the **Add a connection to external assets** pane, in the **Data** section, select **Storage account**.
1. On the **Add a storage account** pane, in the **Search for a resource** text box, enter the name you assigned to the storage account you created earlier in this exercise. 
1. In the list of results, in the storage account entry, in the **Authentication** drop-down list, select **Microsoft Entra ID**, next select **Add connection**, and then select **Close**.
1. Back on the **Manage connected resources in this project** pane, confirm that the connection to the Storage account resource has been added. 

### Task 5: Perform cleanup

1. Open another tab in the web browser displaying the Azure AI Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **foundry-connections-project-RG** and, in the list of results, select **foundry-connections-project-RG**.
1. On the **foundry-connections-project-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **foundry-connections-project-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.