# AI Ready Manage hands-on exercise: Create a compute instance in an Azure AI Foundry hub

## Background information
Compute resources in Azure AI Foundry provide the processing power required to execute workloads such as model training, data preparation, and experimentation. Within this environment, compute instances serve as dedicated, user-assigned virtual machines designed for interactive development and job execution. Each compute instance is assigned to a single user and cannot be shared. It can be reused across multiple workflows within that user's workspace. Compute instances are currently supported only in hub-based projects—standalone Azure AI Foundry projects do not include this feature. You need a compute instance to:
- use prompt flow in the Azure AI Foundry portal.
- create an index.
- open Visual Studio Code (Web or Desktop) in the Azure AI Foundry portal.

SSH access to a compute instance can be optionally enabled, allowing direct sign-in to the virtual machine for advanced tasks such as installing custom dependencies, debugging environment issues, or performing configurations not supported through the Azure AI Foundry portal. SSH access can include root-level privileges, enabling full control over the instance when deeper system-level access is required.

When a compute instance is provisioned, it uses the most recent virtual machine image available at that time. Microsoft updates these images monthly to include the latest operating system, Python, and dependency versions. However, deployed compute instances are not automatically updated. To maintain security and ensure access to the latest patches and software features, administrators can either recreate the instance to refresh its base image (recommended) or manually apply operating system and package updates.

## Scenario
Your company is developing an enterprise AI platform using Azure AI Foundry to support interactive model development, data preparation, and experimentation within hub-based projects. As part of the platform's setup, your company will provision compute instances to provide dedicated, user-assigned environments for interactive development and job execution. These instances will be created to enable use of prompt flow, index creation, and integration with Visual Studio Code for coding and experimentation. To optimize resource utilization and reduce costs, the compute instances will be configured with scheduled idle shutdown, ensuring that inactive instances do not continue consuming compute resources.

Each compute instance will have a system-assigned managed identity enabled, allowing automated workloads such as training jobs, data processing, and artifact storage to securely access Azure resources without relying on user credentials. SSH and root access will be disabled to maintain security and prevent direct system-level modifications, reducing the risk of configuration drift or unintended exposure while still supporting all required development and operational workflows via the portal interface and integrated tools.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).

## Estimated duration
10 minutes

### Task 1: Create Azure AI Foundry hub

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **+ Create** and, in the drop-down menu, select **Hub**.
1. On the **Basics** tab of the **Azure AI hub** page, specify the following settings (leave others with their default values) and select **Next: Storage**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **compute-hub-RG**|
   |Region|The name of the Azure region in which you intend to provision the hub|
   |Name|**compute-hub**|
   |Friendly name|**Compute Hub**|
   |Default project resource group|**Same as hub resource group**|

1. On the **Storage** tab of the **Azure AI hub** page, specify the following settings and select **Next: Inbound Access**:

   |Setting|Value|
   |---|---|
   |Storage account|The default name of a new storage account that will be automatically provisioned|
   |Credential store|**Azure key vault**|
   |Key vault|The default name of a new key vault that will be automatically provisioned|
   |Application insights|**None**|
   |Container registry|**None**|

   **Note**: You could pre-provision a storage account and a key vault and use them instead. 

1. On the **Inbound Access** tab, accept the default option for **Public network access** (**All networks**) and select **Next: Outbound Access**.
1. On the **Outbound Access** tab, accept the default option of **Public** and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default settings and select **Next: Identity**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type** and **Credential-based access** as the **Storage account access type**) and select **Review + create**.

   **Note**: The user assigned identity option is supported only when using an existing storage account, key vault, and container registry.

1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Wait until the hub and the corresponding resources, including the storage account, are provisioned. This might take about one minute. Once the hub is created, on the deployment page, select **Go to resource** to navigate to the hub's **Overview** page. 

### Task 2: Create a compute instance in an Azure AI Foundry hub

1. In the web browser displaying the hub's **Overview** page in the Azure portal, select **Launch Azure AI Foundry**. 

   **Note**: This should automatically open another tab in the web browser window displaying the **Overview** page of the hub in the Azure AI Foundry portal.

1. In the web browser displaying the Azure AI Foundry portal, on the **Overview** page of **Compute hub**, in the vertical menu on the left side, select **Compute**. 
1. On the **Manage compute resources in this hub** pane, ensure that **Compute instances** tab is selected and then select **+ New**.
1. In the **Required settings** section of the **Create compute instance** pane, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Compute name|**Instance-CPU-GP**|
   |Virtual machine type|**CPU**|
   |Virtual machine size|Any available virtual machine size|

   **Note**: The default name of the compute instance consists of your Entra ID username followed by consecutive integer number, starting with **1**. This helps you distinguish between your compute instances and those created by others using the same hub.

1. In the **Scheduling** section of the **Create compute instance** pane, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Enable idle shutdown|Enabled|
   |Shutdown after|**30 Minutes**|

   **Note**: You have the option of configuring multiple customized schedules defining the startup and shutdown days and times of the compute instance.

1. In the **Security** section of the **Create compute instance** pane, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Assign to another user|Disabled|
   |Assign a managed identity|Enabled and set to **System-assigned**|
   |Enable SSH access|Disabled|
   |Allow root access|Disabled|
   |Enable SSO|Disabled|

   **Note**: For SSH access, you can generate a new key pair or use an existing public key (which you can paste directly into a provided text box or reference an Azure public key resource).

   **Note**: Enable Single Sign-On (SSO) option allows the compute instance to receive tokens on behalf of its owner.

1. In the **Tags** section of the **Create compute instance** pane, select **Review + create**.
1. In the **Review** section, verify that all settings you specified are set and select **Cancel**.

   **Note**: You will skip the instance provisioning in order to minimize cost and duration of this exercise. Under normal circumstances you would select **Create** to initiate the provisioning process.

### Task 3: Perform cleanup
1. In the web browser window displaying the Azure AI Foundry portal, switch back to the tab displaying the Azure portal.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **compute-hub-RG** and, in the list of results, select **compute-hub-RG**.
1. On the **compute-hub-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **compute-hub-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.