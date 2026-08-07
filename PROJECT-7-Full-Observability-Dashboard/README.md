**PROJECT 7**

**Full Observability Dashboard
Monitor & Maintain  |  Azure Monitor, Log Analytics, Alerts, KQL, Custom Dashboard**

**What I Built**

I built a centralised monitoring setup for a VM in Central US using Azure Monitor and Log Analytics. I created a Log Analytics workspace and connected vm-monitored to it using a Data Collection Rule, collecting CPU, memory, disk, and network performance metrics. I configured diagnostic settings to send VM metrics and the subscription Activity Log to the workspace. I created an alert rule that emails me when CPU exceeds 80%, triggered it with a CPU stress test, and confirmed the alert email arrived. I ran KQL queries against the Log Analytics workspace to analyse CPU performance data as a time chart. Finally I built a custom Azure Dashboard pinning CPU, network, and disk metrics alongside the KQL chart. 

**Architecture Diagram**

<img width="975" height="610" alt="image" src="https://github.com/user-attachments/assets/30a8b1a7-aa0a-450c-b32f-fa49f9e05e1c" />

 
**Azure Services Used**

Log Analytics Workspace (law-monitoring)	Central store for all VM logs and metrics — the data destination for the Data Collection Rule, diagnostic settings, and Activity Log

Data Collection Rule (DCR)	Configured to collect Basic performance counters (CPU, memory, disk, network) from vm-monitored and send them to law-monitoring

Azure Monitor Agent (AMA)	Installed automatically on vm-monitored when the DCR was created — replaces the older Log Analytics Agent (MMA)

Diagnostic Settings	Configured on vm-monitored to send all metric categories to law-monitoring. Also configured on the subscription Activity Log to capture Administrative, Security, Alert, and Policy events

Alert Rule — High CPU Alert	Monitors Percentage CPU metric on vm-monitored — fires when average CPU exceeds 80% over 5 minutes. Severity 2 Warning

Action Group (ag-email-alert)	Email action group attached to the alert rule — sends notification to my email when the High CPU Alert fires

KQL Queries	Wrote and ran Kusto Query Language queries against the Log Analytics workspace to analyse CPU performance as a time chart

Custom Azure Dashboard	Built a custom dashboard pinning CPU metrics chart, network in/out, disk read/write, KQL time chart.

**Key Concepts Demonstrated**

•	Azure Monitor Metrics vs Azure Monitor Logs — Metrics are numerical time-series data stored in a dedicated database and available for 93 days. Logs are structured text data stored in Log Analytics and queryable with KQL. Metrics are better for real-time alerting; Logs are better for deep analysis and long-term retention

•	Data Collection Rules (DCR) — the current Azure Monitor Agent architecture uses DCRs to define what data to collect, where to collect it from, and where to send it. DCRs replace the older workspace-linked configuration of the legacy Log Analytics Agent

•	Azure Monitor Agent vs Log Analytics Agent — AMA is the current standard. MMA/OMS Agent is legacy and being deprecated. AMA uses DCRs for configuration, supports multiple destinations per agent, and has better security

•	Diagnostic settings vs DCR — Diagnostic settings control platform-level metrics and resource logs (what Azure emits about the resource). DCRs control guest OS-level data collection (what the agent inside the VM collects). Both are needed for full observability

•	Alert rule evaluation cycle — the alert rule checks the metric every 5 minutes over a 5-minute aggregation window. There is an inherent delay between CPU spiking and the alert firing — typically 5-10 minutes after the threshold is first crossed

•	KQL basics — Kusto Query Language is used to query Log Analytics. Key operators: where (filter), summarize (aggregate), project (select columns), render (visualise). Knowing even basic KQL is a differentiator in Azure admin interviews

•	Activity Log vs Diagnostic Logs — Activity Log records control-plane operations (who created, modified, or deleted a resource). Diagnostic Logs record data-plane operations (what happened inside the resource). Both are important for security auditing and compliance

**Step-by-Step Summary**

1.	Created resource group rg-monitoring-lab in Central US

2.	Created Log Analytics workspace law-monitoring in Central US (Pay-as-you-go Per GB tier)

3.	Deployed vm-monitored — Standard_D2s_v3, Ubuntu 24.04, Central US

4.	Created a Data Collection Rule (dcr-vm-monitoring) — Platform: Linux, added vm-monitored as a resource, added Performance Counters (Basic) data source, destination: law-monitoring

5.	Configured diagnostic settings on vm-monitored — all metric categories sent to law-monitoring

6.	Configured Activity Log diagnostic settings — Administrative, Security, Alert, and Policy sent to law-monitoring

7.	Waited 10 minutes for data to start flowing into the workspace

8.	Created alert rule High CPU Alert — scope: vm-monitored, metric: Percentage CPU, operator: Greater than, threshold: 80, aggregation: Average over 5 minutes

9.	Created action group ag-email-alert with email notification to my address

10.	SSH'd into vm-monitored, installed stress tool, ran stress --cpu 2 --timeout 600

11.	Waited 8 minutes — received High CPU Alert email notification confirming alert fired

12.	Opened Log Analytics workspace → Logs → ran KQL CPU time chart query — results returned successfully

13.	Pinned KQL chart to a new custom dashboard named VM Operations Dashboard

14.	Added Metrics chart tiles for CPU, Network In/Out, Disk Read/Write, and Alert summary tile to the dashboard

15.	Deleted resource group rg-monitoring-lab to stop all billing

**Screenshots**

1. Log Analytics workspace overview
 
<img width="975" height="584" alt="image" src="https://github.com/user-attachments/assets/88f033b5-9c9a-4cf4-a13f-989048e462b6" />

*law-monitoring workspace created in Central US.*

2. Data Collection Rule configuration
 
<img width="975" height="317" alt="image" src="https://github.com/user-attachments/assets/2bcd18a6-919d-44e0-ad92-1c7399e5b86b" />

<img width="972" height="327" alt="image" src="https://github.com/user-attachments/assets/74fedb03-0dd2-449d-a06e-f89d58ac5370" />

*dcr-vm-monitoring showing vm-monitored as the resource, Basic Performance Counters as the data source, and law-monitoring as the destination.*

3. Diagnostic settings on vm-monitored
 
<img width="973" height="638" alt="image" src="https://github.com/user-attachments/assets/6027f46e-a67c-4c4b-9be5-a687274d08ad" />

*vm-monitored diagnostic settings sending all metric categories to law-monitoring workspace.*
 
<img width="975" height="330" alt="image" src="https://github.com/user-attachments/assets/20a3d8b7-af50-466e-9d6f-6c498c133f4f" />

*vm-monitored has AzureMonitorLinuxAgent provisioned to send data for the alert to work.*

4. Alert rule configuration — High CPU Alert

 <img width="973" height="389" alt="image" src="https://github.com/user-attachments/assets/85f6ae1a-5e9a-4aa4-8818-7287035b4b0f" />

*High CPU Alert rule showing Percentage CPU metric, Greater than 80% threshold, 5-minute aggregation window, and ag-email-alert action group attached.*
 
<img width="973" height="306" alt="image" src="https://github.com/user-attachments/assets/d8c5234f-0cb6-4d62-8301-4c4e499e4d71" />

*Action group used to configure email alert.*



5. CPU stress test running in terminal

 <img width="973" height="353" alt="image" src="https://github.com/user-attachments/assets/c4d6d6a8-907d-4afe-9adc-eac01cf74bd0" />


*stress tool running on vm-monitored via SSH — dispatching 2 CPU hogs for 600 seconds to trigger the alert threshold.*

6. Alert fired — email notification received

 <img width="973" height="492" alt="image" src="https://github.com/user-attachments/assets/384fb6a5-d6e9-4ade-9363-6331b63e81bf" />

*Email notification from Azure Monitor showing the High CPU Alert fired — confirms the alert rule evaluated correctly and the action group email delivery worked.*

7. Alert shown in Monitor → Alerts

 <img width="975" height="292" alt="image" src="https://github.com/user-attachments/assets/c3953c0a-aa52-4674-99ad-94e2516f0a67" />

*Azure Monitor Alerts page showing the High CPU Alert in fired state with timestamp.*

8. KQL query results — CPU time chart

 <img width="975" height="464" alt="image" src="https://github.com/user-attachments/assets/caf3c3e8-27f5-4afe-a1d1-66fd46c9045a" />

*KQL query in Log Analytics returning CPU performance data as a time chart — shows the CPU spike during the stress test period.*

9. Custom Azure Dashboard — VM Operations Dashboard
 
<img width="973" height="653" alt="image" src="https://github.com/user-attachments/assets/c5951713-8023-41b5-a0dd-05a3d2c24a5a" />

*Custom dashboard showing CPU metrics chart, Network In/Out, Disk Read/Write, pinned KQL time chart, and alert summary tile — single-pane view of VM health.*

**What Broke and How I Fixed It**

Problem 1: No data in Log Analytics after 5 minutes

•	No data in Log Analytics after 5 minutes — wait 10-15 minutes after creating the DCR before expecting data. The Azure Monitor Agent installs asynchronously and data ingestion has a lag

**What I Learned**

This project completed the monitoring picture that the previous six projects were missing. The most important conceptual lesson was understanding the difference between Azure Monitor Metrics and Azure Monitor Logs. Metrics are fast, cheap, and great for real-time alerting, while Logs are more powerful for analysis but have higher latency and cost. Seeing both in the same lab made the distinction genuinely concrete rather than just a definitional difference. Writing KQL queries against real performance data I had generated myself, including the CPU spike from the stress test, made the language feel practical rather than academic. The custom dashboard is also the single most visually impressive screenshot in the entire portfolio series: a single pane showing CPU, network, disk, and alert state simultaneously is something that immediately communicates operational maturity to anyone reviewing the work. I also learned that the Data Collection Rule architecture (AMA + DCR) is meaningfully different from the older Log Analytics Agent approach, and knowing that distinction is an exam question.

**What I'd Do Differently / Next Steps**

•	Add KQL-based alert rules (Log Search Alerts) in addition to metric-based alerts — useful for detecting patterns that metrics alone cannot capture, such as repeated authentication failures in the syslog

•	Configure Azure Monitor Workbooks instead of a simple Dashboard — Workbooks support interactive parameters and conditional formatting, making them much more powerful for operational reporting

•	Set up alert processing rules to suppress alerts during maintenance windows — prevents alert fatigue from planned reboots or patching

**KQL Queries Used**

Purpose	Query

CPU usage time chart	Perf | where TimeGenerated > ago(1h) | where CounterName == "% Processor Time" | summarize AvgCPU = avg(CounterValue) by Computer, bin(TimeGenerated, 5m) | render timechart
	

**Lab Specifications**

Region	Central US

VM	vm-monitored — Standard_D2s_v3 — Ubuntu 24.04

Log Analytics workspace	law-monitoring — Pay-as-you-go Per GB

DCR	dcr-vm-monitoring — Basic performance counters

Alert rule	High CPU Alert — CPU > 80% avg over 5 min — Severity 2

Action group	ag-email-alert — email notification

Stress test	stress --cpu 2 --timeout 600

Alert confirmed	Yes — email received

KQL queries	CPU time chart + failed SSH logins

Dashboard	VM Operations Dashboard — 5 tiles


**Cost**

vm-monitored D2s_v3 (~4 hr)	~$0.38

Log Analytics ingestion (~1 VM, 4 hr)	~$0.01 — negligible at this scale

Alert rules, action groups	Free

Custom dashboard	Free

Total for this lab	~$0.40 or less

**Resources and References**

•	Microsoft Learn — Monitor virtual machines with Azure Monitor

•	Microsoft Learn — Analyze monitoring data with KQL

•	Microsoft Docs — Azure Monitor Agent overview

•	Microsoft Docs — Data Collection Rules

•	AZ-104 Study Guide — Monitor and maintain Azure resources


Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.
