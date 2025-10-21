# AI Ready Manage hands-on exercise: Create an Azure AI Foundry resource and project level storage connection

## Background information
Azure AI Foundry provides a unified environment for building, managing, and deploying AI workloads. In this model, deployments can be hub-based or resource-based. A hub acts as a higher-level construct that organizes and manages multiple Azure AI Foundry resources, typically used in enterprise-scale setups that require multi-region management or shared governance across environments. A resource-based setup focuses on managing projects within a single Azure AI Foundry resource, where governance, networking, and identity settings are shared among those projects.

In a resource-based deployment, each project operates as a child resource of the Azure AI Foundry resource, inheriting its security and governance settings while enabling collaboration within a shared environment. Projects can also define their own managed identities and connections, allowing precise access control to external Azure services. By default, all projects under the same resource share core configurations such as networking, deployment settings, and integrated tools, but they can also establish project-specific connections for resources that need to remain private to an individual project.

Connections in Azure AI Foundry define secure integrations between a Foundry resource or a specific project and external Azure services, such as Azure Storage, Azure AI Search, or Key Vault. A resource-level connection created on the Foundry resource is shared with its projects, while a project-level connection is scoped to a single project to preserve data and operational isolation when required. Authentication and authorization vary depending on the target service and connection type. When using Microsoft Entra ID for Azure services, access can be granted via methods such as user identities, managed identities, or service principals, with permissions enforced through Azure role-based access control (RBAC), eliminating the need for stored keys or credentials.

## Scenario
Your company plans to establish a secure and scalable development environment using Azure AI Foundry projects to manage AI workloads such as dataset preparation, model experimentation, and fine-tuning. As part of this initiative, the company will create an Azure AI Foundry resource that hosts multiple resource-based projects, each representing a distinct team or workload domain. This structure enables different teams to work independently on separate AI solutions while maintaining shared access to common services, infrastructure, and governance policies.

To enable access to Azure Storage, the company will configure two types of connections across these projects: a shared, resource-level connection to a storage account used for storing common artifacts, and project-level connections to separate storage accounts dedicated to each project’s private datasets and outputs. Both connection types will rely on Microsoft Entra ID authentication, ensuring secure, keyless access through system-assigned managed identities for automated operations and user-based credentials for interactive development in notebooks or playgrounds. This configuration will balance collaboration and isolation, supporting efficient and compliant AI development across the organization.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).
- **Familiarity with Azure Storage authorization**: To learn more, refer to [Prevent Shared Key authorization for an Azure Storage account](https://learn.microsoft.com/en-us/azure/storage/common/shared-key-authorization-prevent?tabs=portal).
- **Familiarity with Azure Role-Based Access Control (RBAC)**: To learn more, refer to [Azure RBAC documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/).

## Estimated duration
25 minutes

### Task 1: Create Azure AI Foundry resource and the default project

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **Azure AI Foundry**.
1. On the **AI Foundry \| Azure AI Foundry** page, select **+ Create**.
1. On the **Basics** tab of the **Create an Azure AI Foundry resource** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **storage-projects-RG**|
   |Name|**storage-project-resource**|
   |Region|The name of the Azure region where you intend to create Azure AI Foundry projects|
   |Default project name|**storageproject01**|

1. On the **Network** tab of the **Create an Azure AI Foundry resource** page, ensure that the option **All networks, including the internet can access this resource** is selected and then select **Next**.
1. On the **Identity** tab, accept the default settings (with **System assigned** as the **Identity type**) and select **Next**.
1. On the **Encryption** tab, accept the default settings (encryption using Microsoft-managed keys) and select **Next**.
1. On the **Tags** tab, select **Next**.
1. On the **Review + create** tab, select **Create**.

   **Note**: Wait until the resource and project are provisioned. This might take about one minute. 

### Task 2: Create an additional Azure AI Foundry project

1. In the web browser displaying the Azure AI Foundry resource's deployment status page in the Azure portal, select **Go to resource** to navigate to the project's **Overview** page.
1. On the **storage-project-resource \| Overview** page, in the vertical menu on the left side, in the **Resource Management** section, select **Projects**.
1. On the **storage-project-resource \| Projects** page, select **+ New**.
1. On the **New project** pane, specify the following settings and then select **Create**:

   |Setting|Value|
   |---|---|
   |Name|**storageproject02**|
   |Description|Non-default project 02|

   **Note**: Wait until the project is provisioned. This might take about one minute. 

1. Back on the **storage-project-resource \| Projects** page, select **storageproject01**.
1. On the **storageproject01** page, in the vertical menu on the left side, in the **Resource Management** section, select **Identity**.
1. On the **storageproject01 \| Identity** page, on the **System assigned** tab, verify that the **Status** is set to **On**.
1. Navigate back to the **storage-project-resource \| Projects** page, select **storageproject02**.
1. On the **storageproject02** page, in the vertical menu on the left side, in the **Resource Management** section, select **Identity**.
1. On the **storageproject02 \| Identity** page, on the **System assigned** tab, verify that the **Status** is set to **On**.

### Task 3: Provision an Azure storage account for shared resource-level access

1. In the web browser displaying the **storageproject02 \| Identity** page in the Azure portal, use the **Search** text box at the top of the page to search for **Storage accounts** and, in the list of results, select **Storage accounts**.
1. On the **Storage accounts \| Storage accounts (Blobs)** page, select **+ Create**.
1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**storage-projects-RG**|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of lowercase letters and digits, starting with a letter|
   |Region|The name of the Azure region where you created the Azure AI Foundry resource|
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
1. On the **New container** pane, in the **Name** text box, enter **shared-ai-artifacts** and select **Create**.

   >**Note**: The **Anonymous access level** should be set to **Private (no anonymous access)**.

1. Back on the Storage account **Container** page, in the vertical menu on the left side, select **Access Control (IAM)**.
1. On the storage account's **Access Control (IAM)** page, select the **+ Add** and, in the drop-down menu, select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, under the **Job function roles** listing, in the **Search** text box, enter **Storage Blob Data Contributor**, in the list of search results, select **Storage Blob Data Contributor**, and select **Next**.
1. On the **Members** tab of the **Add role assignment** page, in the **Assign access to** section, select **Managed identity** and then click **+ Select members**. 
1. On the **Select members** pane, in the **Managed identity** drop-down list, select **Azure AI Foundry project** entry, in the list of search results, select **storageproject01** and **storageproject02**, and then click **Select**.
1. Back on the **Members** tab of the **Add role assignment** page, select **Next**.
1. On the **Conditions** tab of the **Add role assignment** page, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

### Task 4: Provision an Azure storage account for an individual project-level access

1. In the web browser displaying the Azure Storage account page in the Azure portal, navigate back to the **Storage accounts \| Storage accounts (Blobs)** page.
1. On the **Storage accounts \| Storage accounts (Blobs)** page, select **+ Create**.
1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**storage-projects-RG**|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of lowercase letters and digits, starting with a letter|
   |Region|The name of the Azure region where you created the Azure AI Foundry resource|
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
1. On the **New container** pane, in the **Name** text box, enter **project-ai-artifacts** and select **Create**.

   >**Note**: The **Anonymous access level** should be set to **Private (no anonymous access)**.

1. Back on the Storage account **Container** page, in the vertical menu on the left side, select **Access Control (IAM)**.
1. On the storage account's **Access Control (IAM)** page, select the **+ Add** and, in the drop-down menu, select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, under the **Job function roles** listing, in the **Search** text box, enter **Storage Blob Data Contributor**, in the list of search results, select **Storage Blob Data Contributor**, and select **Next**.
1. On the **Members** tab of the **Add role assignment** page, in the **Assign access to** section, select **Managed identity** and then click **+ Select members**. 
1. On the **Select members** pane, in the **Managed identity** drop-down list, select **Azure AI Foundry project** entry, in the list of search results, select **storageproject01**, and then click **Select**.
1. Back on the **Members** tab of the **Add role assignment** page, select **Next**.
1. On the **Conditions** tab of the **Add role assignment** page, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

### Task 5: Connect the shared Azure Storage account to Azure AI Foundry resource

1. In the web browser displaying the Azure Storage account page in the Azure portal, open another tab and navigate to the **All resources** page of the Azure AI Foundry portal at [https://ai.azure.com/allResources](https://ai.azure.com/allResources).
1. On the **All resources** page of the **Azure AI Foundry** portal, locate the **storageproject01** entry and, in the **Type** column, select the blue link to the **storage-project-resource** resource. This will display the **storage-project-resource** page in the Azure AI Foundry **Management center**.
1. On the **storage-project-resource** page, in the vertical menu on the left side, in the **Resource (storage-project-resource)** section, select **Connected resources**.
1. On the **Manage connected resources in this project** pane, select **+ New connection**.
1. On the **Add a connection to external assets** pane, in the **Data** section, select **Storage account**.
1. On the **Add a storage account** pane, locate the entry representing the first of the two Azure Storage account you created earlier in this exercise, in the **Authentication** drop-down list, select **Microsoft Entra ID**, select **Add connection**, and then select **Close**.
1. Back on the **Manage connected resources in this project** pane, review the updated list of connections and confirm that the connection to the Storage account resource is listed as **Shared to all projects**.

### Task 6: Connect the shared Azure Storage account to an individual Azure AI Foundry project

1. In the web browser displaying the **Manage connected resources in this project** pane in the Azure AI Foundry portal, in the vertical menu on the left side, in the **Project (storageproject01)** section, select **Connected resources**.
1. On the **Manage connected resources in this project** pane, select **+ New connection**.
1. On the **Add a connection to external assets** pane, in the **Data** section, select **Storage account**.
1. On the **Add a storage account** pane, locate the entry representing the second of the two Azure Storage account you created earlier in this exercise, in the **Authentication** drop-down list, select **Microsoft Entra ID**, select **Add connection**, and then select **Close**.
1. Back on the **Manage connected resources in this project** pane, review the updated list of connections and confirm that the connection to the second Azure Storage account resource is listed as **storageproject01 only**.
1. On the **Manage connected resources in this project** pane, in the vertical menu on the left side, in the **Resource (storage-project-resource)** section, select **Connected resources**, review the updated list of connections and confirm that the connection to the second Azure Storage account resource is listed as **storage-project-resource only**.
1. On the **Manage connected resources in this project** pane, in the vertical menu on the left side, select **Overview**.
1. On the **storage-project-resource** page, in the list of resources, select **storageproject02**. This will display its **Overview** page.
1. On the **Overview** page, in the vertical menu on the left side, in the **Project (storageproject02)** section, select **Connected resources**.
1. On the **Manage connected resources in this project** pane, review the list of connections and confirm that it contains only the connection to the Storage account resource listed as **Shared to all projects**.

### Task 7: Perform cleanup

1. In the web browser displaying the **Manage connected resources in this project** pane in the Azure AI Foundry portal, switch back to the tab displaying the Azure portal.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **storage-projects-RG** and, in the list of results, select **storage-projects-RG**.
1. On the **storage-projects-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **storage-projects-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.