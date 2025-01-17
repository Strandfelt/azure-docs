---
title: Query logs from Azure Automation Start/Stop VMs during off-hours
description: This article tells how to use Azure Monitor to query log data generated by Start/Stop VMs during off-hours.
services: automation
ms.subservice: process-automation
ms.date: 02/28/2023
ms.topic: conceptual
ms.custom: engagement-fy23
---

# Query logs from Start/Stop VMs during off-hours

> [!NOTE]
> Start/Stop VM during off-hours version 1 is unavailable in the marketplace now as it will retire by 30 September 2023. We recommend you start using [version 2](/azure/azure-functions/start-stop-vms/overview), which is now generally available. The new version offers all existing capabilities and provides new features, such as multi-subscription support from a single Start/Stop instance. If you have the version 1 solution already deployed, you can still use the feature, and we will provide support until 30 September 2023. The details of the announcement will be shared on March 31st 2023.  

Azure Automation forwards two types of records to the linked Log Analytics workspace: job logs and job streams. This article reviews the  data available for [query](../azure-monitor/logs/log-query-overview.md) in Azure Monitor.

## Job logs

|Property | Description|
|----------|----------|
|Caller |  Who initiated the operation. Possible values are either an email address or system for scheduled jobs.|
|Category | Classification of the type of data. For Automation, the value is JobLogs.|
|CorrelationId | GUID that is the Correlation ID of the runbook job.|
|JobId | GUID that is the ID of the runbook job.|
|operationName | Specifies the type of operation performed in Azure. For Automation, the value is Job.|
|resourceId | Specifies the resource type in Azure. For Automation, the value is the Automation account associated with the runbook.|
|ResourceGroup | Specifies the resource group  name of the runbook job.|
|ResourceProvider | Specifies the Azure service that supplies the resources you can deploy and manage. For Automation, the value is Azure Automation.|
|ResourceType | Specifies the resource type in Azure. For Automation, the value is the Automation account associated with the runbook.|
|resultType | The status of the runbook job. Possible values are:<br>- Started<br>- Stopped<br>- Suspended<br>- Failed<br>- Succeeded|
|resultDescription | Describes the runbook job result state. Possible values are:<br>- Job is started<br>- Job Failed<br>- Job Completed|
|RunbookName | Specifies the name of the runbook.|
|SourceSystem | Specifies the source system for the data submitted. For Automation, the value is OpsManager|
|StreamType | Specifies the type of event. Possible values are:<br>- Verbose<br>- Output<br>- Error<br>- Warning|
|SubscriptionId | Specifies the subscription ID of the job.
|Time | Date and time when the runbook job executed.|

## Job streams

|Property | Description|
|----------|----------|
|Caller |  Who initiated the operation. Possible values are either an email address or system for scheduled jobs.|
|Category | Classification of the type of data. For Automation, the value is JobStreams.|
|JobId | GUID that is the ID of the runbook job.|
|operationName | Specifies the type of operation performed in Azure. For Automation, the value is Job.|
|ResourceGroup | Specifies the resource group  name of the runbook job.|
|resourceId | Specifies the resource ID in Azure. For Automation, the value is the Automation account associated with the runbook.|
|ResourceProvider | Specifies the Azure service that supplies the resources you can deploy and manage. For Automation, the value is Azure Automation.|
|ResourceType | Specifies the resource type in Azure. For Automation, the value is the Automation account associated with the runbook.|
|resultType | The result of the runbook job at the time the event was generated. A possible value is:<br>- InProgress|
|resultDescription | Includes the output stream from the runbook.|
|RunbookName | The name of the runbook.|
|SourceSystem | Specifies the source system for the data submitted. For Automation, the value is OpsManager.|
|StreamType | The type of job stream. Possible values are:<br>- Progress<br>- Output<br>- Warning<br>- Error<br>- Debug<br>- Verbose|
|Time | Date and time when the runbook job executed.|

When you perform any log search that returns category records of **JobLogs** or **JobStreams**, you can select the **JobLogs** or **JobStreams** view, which displays a set of tiles summarizing the updates returned by the search.

## Sample log searches

The following table provides sample log searches for job records collected by Start/Stop VMs during off-hours.

|Query | Description|
|----------|----------|
|Find jobs for runbook ScheduledStartStop_Parent that have finished successfully | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Find jobs for runbook ScheduledStartStop_Parent that have not completed successfully | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Find jobs for runbook SequencedStartStop_Parent that have finished successfully | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Find jobs for runbook SequencedStartStop_Parent that have not completed successfully | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## Next steps

* To set up the feature, see [Configure Stop/Start VMs during off-hours](automation-solution-vm-management-config.md).
* For information on log alerts during feature deployment, see [Create log alerts with Azure Monitor](../azure-monitor/alerts/alerts-log.md).
* To resolve feature errors, see [Troubleshoot Start/Stop VMs during off-hours issues](troubleshoot/start-stop-vm.md).