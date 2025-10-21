# AI Ready Manage hands-on exercise: Configure Azure AI Foundry hub disaster recovery

## Background information
Azure AI Foundry offers a unified platform for managing AI development and deployment lifecycles, integrating capabilities for data preparation, experimentation, model training, and deployment. While Microsoft provides high availability for Azure services, unplanned regional outages can still occur, making it essential for organizations to plan and implement a disaster recovery (DR) strategy. Azure AI Foundry itself doesn't provide built-in automatic failover, so resilience should be achieved through a customer-managed approach that replicates critical components and configurations across multiple regions. Microsoft documentation illustrates such approach for hub-based deployments.

A sound recovery strategy for Azure AI Foundry hubs depends on several associated Azure services. The Azure AI Foundry infrastructure is managed by Microsoft, but many supporting services (such as Azure Storage, Azure Key Vault, Azure Container Registry, and Application Insights) are customer-managed and must be configured for disaster recovery. For example, Azure Key Vault provides built-in regional redundancy in most regions, while Azure Container Registry Premium supports geo-replication, allowing registry content to be synchronized across paired regions. Other services, such as Storage and Application Insights, require manual configuration and coordination between regions to ensure data consistency and recovery readiness. In particular, Azure Storage accounts used by Azure AI Foundry do not support geo-replication, requiring separate regional instances and custom synchronization of their content to maintain availability during failover.

A multi-region deployment is the foundation of a robust DR plan. It involves creating duplicate AI Foundry hubs and associated resources in two paired Azure regions—one serving as the primary and the other as the secondary (failover) environment. During a regional outage, workloads can be redirected to the secondary region with minimal downtime. Proper planning ensures that compute capacity, credentials, model artifacts, and registry data remain available and synchronized between regions, maximizing the ability to resume operations quickly and maintain continuity of AI-driven services.

## Scenario
Your company plans to build a centralized AI platform on Azure AI Foundry to support enterprise-scale model development, experimentation, and deployment. To safeguard against potential regional service interruptions and ensure business continuity, your company will implement a disaster recovery configuration for its Azure AI Foundry hub. The solution will include deploying a primary hub and a secondary hub in paired Azure regions, ensuring that critical resources, including the Key Vault and Container Registry, are available and synchronized across both locations. The Azure Container Registry will be configured with geo-replication, while the Key Vault will leverage its built-in regional redundancy features to maintain availability of its artifacts. Replication of Storage accounts will be implemented later through a custom, automated process to ensure consistent data availability across regions.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).

## Estimated duration
20 minutes

### Task 1: Create a disaster recovery-capable Azure Key Vault instance

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **Key vaults** and, in the list of results, select **Key vaults**.
1. On the **Key vaults** page, select **+ Create**.
1. On the **Basics** tab of the **Create a key vault** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **dr-aifoundry-RG**|
   |Key vault name|Any unique name between 3 and 24 alphanumeric characters in length, starting with a letter|
   |Region|The name of the Azure region in which you intend to provision the primary instance of Azure AI Foundry hub|
   |Pricing tier|**Standard**|

   **Note**: Choose any Azure region that supports deployments of Azure AI Foundry hubs and which has a corresponding paired region. For the list of regions in this category, refer to [Azure region pairs and nonpaired regions](https://learn.microsoft.com/en-us/azure/reliability/regions-paired).

1. On the **Access configuration** tab of the **Create a key vault** page, ensure that the **Permission model** is set to **Azure role-based access control** and select **Next**:
1. On the **Networking** tab, ensure that the **Enable public access** checkbox is selected, in the **Public Access** section, ensure that **Allow access from** is set to **All networks**, and select **Review + create**.
1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Do not wait until the Azure Key vault instance is provisioned but instead proceed to the next task. Vault provisioning might take about one minute. 

### Task 2: Create a disaster recovery-capable Azure Container Registry instance

1. In the web browser displaying the Azure Key Vault deployment page, use the **Search** text box at the top of the page to search for **Container registries** and, in the list of results, select **Container registries**.
1. On the **Container registries** page, select **+ Create**.
1. On the **Basics** tab of the **Create container registry** page, specify the following settings (leave others with their default values) and select **Next: Networking**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**dr-aifoundry-RG**|
   |Registry name|Any unique name between 5 and 50 alphanumeric characters in length, starting with a letter|
   |Region|The name of the Azure region in which you deployed the Azure Key Vault instance|
   |Pricing tier|**Premium**|

   **Note**: The Premium pricing tier automatically provisions the container registry in a highly available manner across availability zones (in regions that support them).

1. On the **Networking** tab, ensure that the **Connectivity configuration** is set to **Public access (all networks)**, and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default setting which uses Microsoft-managed encryption keys and select **Review + create**.
1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Wait until the Azure Container Registry instance is provisioned. This should take less than one minute. Once the instance is created, on the deployment page, select **Go to resource** to navigate to its **Overview** page. 

1. On the **Overview** page of the Azure Container Registry instance, in the vertical menu on the left side, in the **Services** section, select **Geo-replications**.
1. On the **Geo-replications** page, on the map displaying regions available for geo-replication, select the hexagon representing the other region paired up with the primary region you selected for the deployment of the Azure Container Registry instance. 
1. On the **Create replication** pane, verify that the **Location** drop-down menu includes the name of the target region and select **Create**.

### Task 3: Create the primary Azure AI Foundry hub

1. In the web browser displaying the Azure Container Registry instance **Geo-replications** page, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **+ Create** and, in the drop-down menu, select **Hub**.
1. On the **Basics** tab of the **Azure AI hub** page, specify the following settings (leave others with their default values) and select **Next: Storage**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**dr-aifoundry-RG**|
   |Region|The name of the Azure region where you provisioned the Azure Key Vault and Azure Container Registry instances|
   |Name|**dr-aifoundry-primary-hub**|
   |Friendly name|**DR AI Foundry Primary Hub**|
   |Default project resource group|**Same as hub resource group**|

1. On the **Storage** tab of the **Azure AI hub** page, specify the following settings and select **Next: Inbound Access**:

   |Setting|Value|
   |---|---|
   |Storage account|The default name of a new storage account that will be automatically provisioned|
   |Credential store|**Azure key vault**|
   |Key vault|The name of the Key Vault instance you provisioned earlier in this exercise|
   |Application insights|**None**|
   |Container registry|The name of the Container Registry instance you provisioned earlier in this exercise|

1. On the **Inbound Access** tab, accept the default option for **Public network access** (**All networks**) and select **Next: Outbound Access**.
1. On the **Outbound Access** tab, accept the default option of **Public** and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default settings and select **Next: Identity**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type** and **Credential-based access** as the **Storage account access type**) and select **Review + create**.

   **Note**: The user assigned identity option is supported only when using an existing storage account, key vault, and container registry.

1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Do not wait until the hub and the corresponding resources are provisioned but instead proceed to the next task. The provisioning might take about one minute. 

### Task 4: Create the secondary Azure AI Foundry hub

1. In the web browser displaying the Azure AI Foundry hub's deployment status, navigate back to the **AI Foundry** page.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **+ Create** and, in the drop-down menu, select **Hub**.
1. On the **Basics** tab of the **Azure AI hub** page, specify the following settings (leave others with their default values) and select **Next: Storage**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**dr-aifoundry-RG**|
   |Region|The name of the other region paired up with the primary region you selected for the deployment of the primary Azure AI Foundry hub|
   |Name|**dr-aifoundry-secondary-hub**|
   |Friendly name|**DR AI Foundry Secondary Hub**|
   |Default project resource group|**Same as hub resource group**|

1. On the **Storage** tab of the **Azure AI hub** page, specify the following settings and select **Next: Inbound Access**:

   |Setting|Value|
   |---|---|
   |Storage account|The default name of a new storage account that will be automatically provisioned|
   |Credential store|**Azure key vault**|
   |Key vault|The name of the Key Vault instance you provisioned earlier in this exercise|
   |Application insights|**None**|
   |Container registry|The name of the Container Registry instance you provisioned earlier in this exercise|

1. On the **Inbound Access** tab, accept the default option for **Public network access** (**All networks**) and select **Next: Outbound Access**.
1. On the **Outbound Access** tab, accept the default option of **Public** and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default settings and select **Next: Identity**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type** and **Credential-based access** as the **Storage account access type**) and select **Review + create**.

   **Note**: The user assigned identity option is supported only when using an existing storage account, key vault, and container registry.

1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Do not wait until the hub and the corresponding resources are provisioned but instead proceed to the next task. The provisioning might take about one minute. 

### Task 5: Perform cleanup

1. Open another tab in the web browser displaying the Azure AI Foundry portal, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and, if prompted, sign in by providing the same credentials you have been using throughout this exercise.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **dr-aifoundry-RG** and, in the list of results, select **dr-aifoundry-RG**.
1. On the **dr-aifoundry-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **dr-aifoundry-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.