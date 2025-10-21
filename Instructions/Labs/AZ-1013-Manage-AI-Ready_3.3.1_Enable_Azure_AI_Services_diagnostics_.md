# AI Ready Manage hands-on exercise: Enable diagnostics for Azure AI Services

## Background information
Diagnostic settings in Azure AI Foundry enable administrators to collect and route operational data—such as logs and metrics—from Foundry resources to destinations like Log Analytics workspaces, Azure Storage accounts, or Event Hubs. These settings support centralized monitoring, troubleshooting, and auditing across the Foundry environment, allowing consistent data collection and analysis. When configuring diagnostic settings, administrators can select specific log and metric categories or predefined category groups (such as audit and allLogs) that automatically include relevant log types for each resource.

The specific diagnostic categories available differ between AI hubs, hub-based projects, and Azure AI Foundry projects. AI hubs provide options to collect ComputeInstanceEvents and AllMetrics, which capture activity and performance data for compute instances. Hub-based projects include a broader set of categories under audit and allLogs, encompassing operational and security-related events across project components. Azure AI Foundry projects expose the most extensive telemetry options, including Audit Logs, Request and Response Logs, Azure OpenAI Request Usage, Trace Logs, and AllMetrics, reflecting their focus on AI model execution and API interactions.

All three resource types share the same diagnostic destination options, enabling consistent monitoring across the Foundry ecosystem. Administrators can send collected data to a Log Analytics workspace for analysis and querying, archive it to a Storage account for long-term retention, or stream it to an Event Hub for integration with external monitoring and automation systems. This unified destination model ensures that diagnostics from hubs, projects, and project resources can be correlated and analyzed together to provide a comprehensive view of AI operations and performance.

## Scenario
Your company is planning to establish an enterprise AI platform using Azure AI Foundry to enable consistent monitoring, governance, and diagnostics across a wide range of AI development and operational workloads. These workloads will include prompt flow orchestration, large-scale model fine-tuning, vector index creation for retrieval-augmented generation, and data transformation pipelines supporting both experimentation and production scenarios. To ensure full visibility into these activities, diagnostic settings will be configured for AI hubs, hub-based projects, and Azure AI Foundry projects. The configuration will collect all available log and metric categories (including Audit Logs, ComputeInstanceEvents, Request and Response Logs, Azure OpenAI Request Usage, Trace Logs, and AllMetrics) in order to provide deep insight into user actions, model execution performance, and system health. This comprehensive logging strategy will support auditing, troubleshooting, and optimization of complex, multi-stage AI workflows that span compute, data, and orchestration components.

Collected telemetry will be routed to both a Log Analytics workspace and a Storage account. The Log Analytics workspace will enable interactive querying, dashboarding, and integration with Microsoft Sentinel for proactive threat detection and correlation across AI resources. The Storage account will provide long-term, cost-efficient archival for compliance and forensic analysis, preserving the complete operational history of AI workloads. This approach ensures that diagnostic data from Azure AI Foundry remains comprehensive, actionable, and aligned with the organization’s requirements for reliability, transparency, and security throughout the AI development lifecycle.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).

## Estimated duration
25 minutes

### Task 1: Create Azure AI Foundry hub

1. Start a web browser, navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com) and sign in by providing the credentials of a user account which has the Owner role assigned at the Azure subscription level.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **+ Create** and, in the drop-down menu, select **Hub**.
1. On the **Basics** tab of the **Azure AI hub** page, specify the following settings (leave others with their default values) and select **Next: Storage**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **diagnostics-projects-RG**|
   |Region|The name of the Azure region in which you intend to provision the hub|
   |Name|**diagnostics-hub**|
   |Friendly name|**Diagnostics hub**|
   |Default project resource group|**Same as hub resource group**|

1. On the **Storage** tab of the **Azure AI hub** page, specify the following settings and select **Next: Inbound Access**:

   |Setting|Value|
   |---|---|
   |Storage account|The default name of a new storage account that will be automatically provisioned|
   |Credential store|**Azure key vault**|
   |Key vault|The default name of a new key vault that will be automatically provisioned|
   |Application insights|**None**|
   |Container registry|**None**|

   **Note**: You could pre-provision a storage account, key vault, and container registry and use them instead. 

1. On the **Inbound Access** tab, accept the default option for **Public network access** (**All networks**) and select **Next: Outbound Access**.
1. On the **Outbound Access** tab, accept the default option of **Public** and select **Next: Encryption**.
1. On the **Encryption** tab, accept the default settings and select **Next: Identity**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type** and **Credential-based access** as the **Storage account access type**) and select **Review + create**.

   **Note**: The user assigned identity option is supported only when using an existing storage account, key vault, and container registry.

1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Wait until the hub and the corresponding resources, including the storage account, are provisioned. This might take about one minute. Once the hub is created, on the deployment page, select **Go to resource** to navigate to the hub's **Overview** page. 

### Task 2: Create an Azure AI Foundry hub-based project

1. In the web browser displaying the **Overview** page of the **diagnostics-hub** hub, in the vertical menu on the left side, in the **Settings** section, select **Projects**. 
1. On the **diagnostics-hub \| Projects** page, select **+ Add**.
1. On the **Basics** tab of the **Azure AI project** page, specify the following settings (leave others with their default values) and select **Next: Identity**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **diagnostics-projects-RG**|
   |Name|**diagnostics-hub-project**|
   |Friendly name|**Diagnostics hub project**|
   |Hub|**diagnostics-hub**|

1. On the **Identity** tab of the **Azure AI project** page, accept the default settings (with **System assigned identity** as the **Identity type** and **Credential-based access** as the **Storage account access type**) and select **Review + create**.
1. Once you selected **Review + create**, wait for the validation to complete and then select **Create**.

   **Note**: Do not wait until the project is provisioned but instead proceed to the next task. Project provisioning might take about one minute. 

### Task 3: Create an Azure AI Foundry project

1. In the web browser displaying the project deployment status, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **Azure AI Foundry**.
1. On the **AI Foundry \| Azure AI Foundry** page, select **+ Create**.
1. On the **Basics** tab of the **Create an Azure AI Foundry resource** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **diagnostics-projects-RG**|
   |Name|**diagnostics-project-resource**|
   |Region|The name of Azure region in which you provisioned the hub|
   |Default project name|**diagnostics-project**|

1. On the **Network** tab of the **Create an Azure AI Foundry resource** page, ensure that the option **All networks, including the internet can access this resource** is selected and then select **Next**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type**) and select **Next**.
1. On the **Encryption** tab, accept the default settings (encryption using Microsoft-managed keys) and select **Next**.
1. On the **Tags** tab, select **Next**.
1. On the **Review + create** tab, select **Create**.

   **Note**: Do not wait until the project is provisioned but instead proceed to the next task. Project provisioning might take about one minute. 

### Task 4: Create an Azure Storage account

1. In the web browser displaying the project's deployment status page in the Azure portal, use the **Search** text box at the top of the page to search for **Storage accounts** and, in the list of results, select **Storage accounts**.
1. On the **Storage accounts \| Storage accounts (Blobs)** page, select **+ Create**.
1. On the **Basics** tab of the **Create storage account** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**diagnostics-projects-RG**|
   |Storage account name|Any globally unique name between 3 and 24 in length consisting of lower case letters and digits, starting with a letter|
   |Region|The name of the Azure region where you created the Azure AI Foundry resources earlier in this exercise|
   |Preferred storage type|**Azure Blob Storage or Azure Data Lake Storage Gen 2**|
   |Performance|**Standard**|
   |Redundancy|**Locally redundant storage (LRS)**|

   >**Note**: Record the storage account name. You'll need it later in this exercise.

1. On the **Advanced** tab, ensure that the **Enable storage account key access** checkbox is checked and then select **Next**.
1. On the **Networking** tab, ensure that the **Public network access** option is set to **Enable**, the **Public network access type** is set to **Enable from all networks**, and then select **Next**.
1. On the **Data protection** tab, specify the following settings (leave others with their default values) and then select **Review + create**:

   |Setting|Value|
   |---|---|
   |Enable soft delete for blobs|Disabled|
   |Enable soft delete for containers|Disabled|
   |Enable soft delete for file shares|Disabled|

   >**Note**: You might consider disabling soft delete for blobs in a diagnostics-only Azure Storage account unless there is a specific compliance or troubleshooting requirement regarding data retention. Diagnostic data such as logs and metrics are typically transient, automatically collected, and often managed by retention policies or lifecycle rules rather than manual recovery. Enabling soft delete in this context could potentially add unnecessary storage costs and complexity, as deleted diagnostic blobs would continue to occupy space until the retention period expires.

1. On the **Review + create** tab, wait for the validation process to complete, and then select **Create**.

   >**Note**: Do not wait for the Storage account provisioning to complete but instead proceed to the next task. The provisioning should take less than one minute.

### Task 5: Create an Azure Log Analytics workspace

1. In the web browser displaying the deployment status page of the Azure Storage account, use the **Search** text box at the top of the page to search for **Log Analytics workspaces** and, in the list of results, select **Log Analytics workspaces**.
1. On the **Log Analytics workspaces** page, select **+ Create**.
1. On the **Basics** tab of the **Create Log Analytics workspace** page, specify the following settings (leave others with their default values) and select  **Review + Create**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**diagnostics-projects-RG**|
   |Name|**ai-foundry-la-workspace**|
   |Region|The name of the Azure region where you created the Azure AI Foundry resources earlier in this exercise|

1. On the **Review + Create** tab, wait for the validation process to complete, and then select **Create**.

   >**Note**: Wait for the Log Analytics workspace provisioning to complete. This should take less than one minute.

### Task 6: Configure diagnostics for Azure AI Foundry resources

1. In the web browser displaying the deployment status page of the Log Analytics workspace in the Azure portal, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **Azure AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **diagnostics-hub**.
1. On the **diagnostics-hub** page, in the vertical menu on the left side, in the **Monitoring** section, select **Diagnostic settings**.
1. On the **diagnostics-hub \| Diagnostic settings** page, verify that the type of logs you can collect include **ComputeInstanceEvents** and **AllMetrics** and then select **+ Add diagnostic settings**.
1. On the **Diagnostic settings** page, perform the following tasks:

- In the **Diagnostic setting name** text box, enter **AI Foundry hub full diagnostics**
- In the **Logs** section, select **audit** and **allLogs** category groups. This will automatically select the **ComputeInstanceEvent** category. 
- In the **Metrics** section, select **AllMetrics**.
- In the **Destination details** section, select the **Send to Log Analytics workspace** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select **ai-foundry-la-workspace**.
- In the **Destination details** section, select the **Archive to a storage account** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select the name of the storage account you created earlier in this exercise.
- Select **Save**.

   >**Note**: Category groups are collections of different logs combined to help you achieve different monitoring goals. These groups are defined dynamically and managed by Microsoft.

1. In the web browser, navigate back to the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **AI Hubs**.
1. On the **AI Foundry \| AI Hubs** page, select **diagnostics-hub-project**.
1. On the **diagnostics-hub-project** page, in the vertical menu on the left side, in the **Monitoring** section, select **Diagnostic settings**.
1. On the **diagnostics-hub-project \| Diagnostic settings** page, review a long list of logs you can collect, including **AllMetrics** and then select **+ Add diagnostic settings**.
1. On the **Diagnostic settings** page, perform the following tasks:

- In the **Diagnostic setting name** text box, enter **AI Foundry hub project full diagnostics**
- In the **Logs** section, select **audit** and **allLogs** category groups. This will automatically select all log categories. 
- In the **Metrics** section, select **AllMetrics**.
- In the **Destination details** section, select the **Send to Log Analytics workspace** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select **ai-foundry-la-workspace**.
- In the **Destination details** section, select the **Archive to a storage account** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select the name of the storage account you created earlier in this exercise.
- Select **Save**.

1. In the web browser, navigate back to the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **Azure AI Foundry**.
1. On the **AI Foundry \| Azure AI Foundry** page, select **diagnostics-project-resource**.
1. On the **diagnostics-project-resource** page, in the vertical menu on the left side, in the **Monitoring** section, select **Diagnostic settings**.
1. On the **diagnostics-project-resource \| Diagnostic settings** page, review the list of logs you can collect, including **Audit Logs**, **Request and Response Logs**, **Azure OpenAI Request Usage**, **Trace Logs**, and **AllMetrics**, and then select **+ Add diagnostic settings**.
1. On the **Diagnostic settings** page, perform the following tasks:

- In the **Diagnostic setting name** text box, enter **AI Foundry project full diagnostics**
- In the **Logs** section, select **audit** and **allLogs** category groups. This will automatically select all log categories. 
- In the **Metrics** section, select **AllMetrics**.
- In the **Destination details** section, select the **Send to Log Analytics workspace** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select **ai-foundry-la-workspace**.
- In the **Destination details** section, select the **Archive to a storage account** checkbox, ensure that the Azure subscription you are using in this exercise appears in the **Subscription** drop-down list and, in the **Log Analytics workspace**, select the name of the storage account you created earlier in this exercise.
- Select **Save**.

### Task 7: Perform cleanup
1. In the web browser window displaying the Azure AI Foundry portal, switch back to the tab displaying the Azure portal.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **diagnostics-projects-RG** and, in the list of results, select **diagnostics-projects-RG**.
1. On the **diagnostics-projects-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **diagnostics-projects-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.