# AI Ready Manage hands-on exercise: Monitoring Azure AI Services

## Background information

Azure Monitor provides a unified framework for collecting, analyzing, and acting on telemetry from Azure resources, including Azure AI Foundry. It captures platform metrics and resource logs to help you understand the performance, availability, and operational behavior of your AI workloads. Platform metrics are automatically collected and stored in the Azure Monitor time-series database. These metrics are lightweight, support near real-time alerting, and are used to track resource performance trends over time.

Azure Monitor also supports alerts, which proactively notify you when specified conditions are met in your monitoring data. Alerts can be used to identify and address issues before they affect end users or dependent services. For AI workloads, monitoring plays a crucial role in maintaining operational reliability, ensuring compliance, and detecting anomalies in model execution or data flow.

As Azure AI Foundry becomes central to developing and deploying generative AI applications, monitoring across its components (including AI hubs, hub-based projects, and Azure AI Foundry projects) provides visibility into both infrastructure-level events and AI-specific telemetry, such as request patterns, latency, usage, and compute instance activity. By combining metrics and diagnostic logs, teams can correlate system performance with AI model behavior and optimize both development and production environments.

## Scenario
Your company is planning to deploy an enterprise AI platform using Azure AI Foundry to monitor and manage the performance, reliability, and security of project-level resources that host key AI workloads. Azure AI Foundry projects will support advanced use cases such as large language model fine-tuning, prompt flow orchestration, data preprocessing pipelines, and retrieval-augmented generation (RAG) scenarios. To ensure consistent observability across these workloads, your company will focus on collecting and analyzing project-level metrics that reflect the performance and availability of deployed AI services. These metrics, such as Availability Rate, Request Count, Latency, and Usage statistics across Azure OpenAI and Cognitive Services namespaces, will provide actionable insights into model responsiveness, throughput, and reliability. Monitoring these metrics over time will help identify trends, detect anomalies, and validate the impact of configuration or deployment changes on overall service health.

To track service reliability and increase operational awareness, your company will establish a metric-based alerting framework within Azure Monitor. Alert rules will track key availability signals, such as AvailabilityRate, to ensure that any drop below 100% triggers immediate investigation into potential service interruptions, endpoint errors, or infrastructure issues. Complementing this, alert processing rules will automatically suppress notifications outside standard business hours (7:00 PM to 7:00 AM), minimizing alert fatigue while ensuring that critical incidents are promptly surfaced during operational periods.

## Prerequisites
- **Azure subscription**: If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.
- **Permissions**: To create Azure AI Services resources, you should have the Owner role assigned at the Azure subscription.
- **Familiarity with Azure AI Foundry resource types**: To learn more, refer to [Choose an Azure resource type for AI foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/resource-types).

## Estimated duration
25 minutes

### Task 1: Create an Azure AI Foundry project

1. In the web browser displaying the project deployment status, use the **Search** text box at the top of the page to search for **AI Foundry** and, in the list of results, select **AI Foundry**.
1. On the **AI Foundry** page, in the vertical menu on the left side, select **Use with AI Foundry** and then select **Azure AI Foundry**.
1. On the **AI Foundry \| Azure AI Foundry** page, select **+ Create**.
1. On the **Basics** tab of the **Create an Azure AI Foundry resource** page, specify the following settings (leave others with their default values) and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|The name of a new resource group **monitoring-project-RG**|
   |Name|**monitoring-project-resource**|
   |Region|The name of the Azure region in which you provisioned the hub|
   |Default project name|**monitoring-project**|

1. On the **Network** tab of the **Create an Azure AI Foundry resource** page, ensure that the option **All networks, including the internet can access this resource** is selected and then select **Next**.
1. On the **Identity** tab, accept the default settings (with **System assigned identity** as the **Identity type**) and select **Next**.
1. On the **Encryption** tab, accept the default settings (encryption using Microsoft-managed keys) and select **Next**.
1. On the **Tags** tab, select **Next**.
1. On the **Review + create** tab, select **Create**.

   **Note**: Wait until the project is provisioned. This might take about one minute. 

1. In the web browser displaying the project's deployment status page in the Azure portal, select **Go to resource** to navigate to the project's **Overview** page.

### Task 2: Review Azure AI Foundry project metrics

1. In the web browser displaying the Azure portal, on the **monitoring-project-resource** **Overview** page, in the **Monitoring** section, select **Metrics**.
1. On the **monitoring-project-resource \| Metrics** page, perform the following tasks:

- Ensure that the **Scope** is set to **monitoring-project-resource** and that **Metric Namespace** is set to **Cognitive Service standard metrics**.
- Use the **Metric** drop-down list to review the list of available metrics, grouped in the following sections:

   - Azure OpenAI - HTTP Requests
   - Azure OpenAI - Latency
   - Azure OpenAI - Usage
   - Cognitive Services - HTTP Requests
   - Cognitive Services - SLI
   - ContentSafety - Risks&Safety
   - ContentSafety - Usage
   - Models - HTTP Requests
   - Models - Latency
   - Models - Usage
   - SpeechServices - Usage

   **Note**: For the list of available metrics, refer to [Supported metrics for Microsoft.CognitiveServices/accounts](https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-cognitiveservices-accounts-metrics)

- In the **Metric** drop-down list, in the **Azure OpenAI - HTTP Requests** section, select **Availability Rate** and, in the **Aggregation** drop-down list, select **Avg**. 

   **Note**: This metric represents availability percentage with the following calculation: (Total Calls - Server Errors)/Total Calls. Server Errors include any HTTP responses >=500. This is applicable specifically to Azure OpenAI service.

- Select **+ Add metric**. This will allow you to add another metric to the same chart.
- In the **Metric** drop-down list, in the **Cognitive Services - SLI** section, select **Availability Rate** and, in the **Aggregation** drop-down list, select **Avg**. 

   **Note**: This metric also represents availability percentage with the following calculation: (Total Calls - Server Errors)/Total Calls. Server Errors include any HTTP responses >=500. This does not include Azure OpenAI service.

   **Note**: At this point, the line chart should display two color-coded lines.

### Task 3: Create Azure AI Foundry project alert rule

1. In the web browser displaying the Azure portal, on the **monitoring-project-resource \| Metrics** page, in the vertical menu on the left side, in the **Monitoring** section, select **Alerts**.
1. On the **monitoring-project-resource \| Alerts** page, select **Alert rules**.
1. On the **Alert rules** page, select **+ Create**. This will display the **Create an alert rule** page, with the **Scope** automatically set to the **monitoring-project-resource**.
1. On the **Condition** tab of the **Create an alert rule** page, perform the following tasks:

- In the **Signal name** drop-down list, select **AvailabilityRate**.
- In the **Alert logic** section, ensure that the **Threshold type** is set to **Static**

   **Note**: Setting the **Threshold type** to **Dynamic** would cause alerts to trigger based on automatically learned patterns and deviations in the metric's historical behavior rather than a fixed threshold, which may lead to inconsistent or delayed alerts for availability issues.

- In the **Aggregation type** drop-down list, select **Average**.
- In the **Value is** drop-down list, select **Less than**.
- In the **Threshold** drop-down list, select **100**.
- Use the **Preview** section to determine the resulting cost and validate how often the alert would trigger based on historical data, ensuring the configuration aligns with your monitoring and notification requirements.
- Leave **Split by dimension** settings with their default values.
- In the **When to evaluate** section, ensure that **Check every** is set to **1 minute** and **Lookback period** is set to **5 minutes**.
- Select **Next: Actions >**

1. On the **Actions** tab of the **Create an alert rule** page, select **+ Create action group**. This will display the **Create action group** page.
1. On the **Basics** tab of the **Create action group** page, specify the following settings (leave others with their default values) and select **Next: Notifications >**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**monitoring-project-RG**|
   |Region|Global|
   |Action group name|**monitoring-project-action-group**|
   |Display name|**AiFoundryRes**|

   **Note**: Display name is limited to 12 characters.

1. On the **Notifications** tab of the **Create action group** page, perform the following tasks:

- In the **Notification type** drop-down list, select **Email/SMS message/Push/Voice**. This will trigger a display of the **Email/SMS message/Push/Voice** pane.
- In the **Email/SMS message/Push/Voice** pane, select the **Email** checkbox and enter any valid email address, set the **Enable the common alert schema** switch to **Yes**, and then select **OK**.

   **Note**: The common alert schema provides a standardized format for all alert notifications, making it easier to parse, integrate, and automate responses across different monitoring and incident management tools. You might choose not to enable it if existing automation or notification systems are not capable of properly parsing the common alert schema.

- In the **Name** text box, enter a unique, descriptive name for the notification (we'll use **monitoring-project-email-notification**).
- Select **Next: Actions >**.

1. On the **Actions** tab of the **Create action group** page, review the list of entries the **Action type** drop-down list (which include Automation Runbook, Azure Function, Event Hub, ITSM, Logic App, Secure Webhook, and Webhook) without making any changes and then select **Review + create**.
1. On the **Review + create** tab, select **Create**.
1. Back on the **Actions** tab of the **Create an alert rule** page, verify that the **monitoring-project-action-group** is listed in the **Action group name** column and then select **Next: Details >**.
1. On the **Details** tab, specify the following settings (leave others with their default values) and select **Review + create**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**monitoring-project-RG**|
   |Severity|**0 - Critical**|
   |Action rule name|**monitoring-project-availability-alert-rule**|
   |Alert rule description|**Triggers when the average Azure AI Foundry project resource availability rate falls below 100%, indicating a potential service disruption**|
   |Advanced options -> Enable upon creation|Enabled|
   |Automatically resolve alerts|Enabled|

1. On the **Review + create** tab, review the resulting settings, including the pricing information and then select **Create**.
1. Back on the **Alert rules** page, select **Refresh** and verify that the newly created rule appears in the list of rules.

### Task 4: Create Azure AI Foundry project alert processing rule

1. In the web browser displaying the Azure portal, navigate back to the **monitoring-project-resource \| Alerts** page.
1. On the **monitoring-project-resource \| Alerts** page, select **Alert processing rules**.
1. On the **Alert processing rules** page, select **+ Create**. This will display the **Create an alert processing rule** page, with the **Scope** automatically set to the **monitoring-project-resource**.
1. On the **Scope** tab of the **Alert processing rules** page, in the **Filters** drop-down list, select **Alert rule name**, leave **Operator** set to **Equals**, and, in the **Value** drop-down list, select **monitoring-project-availability-alert-rule**.
1. On the **Scope** tab of the **Alert processing rules** page, select **Next: Rule settings >**.
1. On the **Rule settings** tab, in the **Rule type** section, ensure that the **Suppress notification** option is selected and then select **Next: Scheduling >**.
1. On the **Scheduling** tab, in the **Apply the rule** section, select **At a specific time**, specify the following settings, and then select **Next: Details >**:

   |Setting|Value|
   |---|---|
   |Start|tomorrow's date at 7:00 PM local time|
   |End|year from tomorrow's date at 7:00 AM local time| 
   |Time zone|local time zone|

1. On the **Details** tab, specify the following settings, and then select **Review + create**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this exercise|
   |Resource group|**monitoring-project-RG**|
   |Rule name|**monitoring-project-availability-alert-processing-rule**|
   |Alert rule description|**Temporarily suppresses availability alerts during scheduled maintenance window**|
   |Enable upon creation|Enabled|

1. On the **Review + create** tab, review the resulting settings and then select **Create**.

### Task 5: Perform cleanup
1. In the web browser window displaying the Azure AI Foundry portal, switch back to the tab displaying the Azure portal.
1. In the Azure portal, use the **Search** text box at the top of the page to search for **monitoring-project-RG** and, in the list of results, select **monitoring-project-RG**.
1. On the **monitoring-project-RG** page, select **Delete resource group**, on the **Delete a resource group** pane, in the **Enter resource group name to confirm deletion** text box, enter **monitoring-project-RG**, select **Delete**, and, when prompted for confirmation, select **Delete** again.