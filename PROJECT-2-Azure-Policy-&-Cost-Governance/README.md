**PROJECT 2**

**Azure Policy & Cost Governance
Identity & Governance  |  Azure Policy, Management Groups, Cost Management**

**What I Built**

Built a governance layer using Management Groups and Azure Policy. I enforced mandatory tagging on all resources, restricted which Virtual Machines sizes and regions could be used, and set up a budget alert to avoid overspending. 

**Architecture Diagram:**
 <img width="975" height="678" alt="image" src="https://github.com/user-attachments/assets/1874fe92-9cf3-4593-af2c-bf6066ac4103" />

**Azure Services Used:**
Service	Purpose
Management Groups	Created a two-level hierarchy (mg-company → mg-production) and moved my subscription under it, to practice applying governance at scale above the subscription level
Azure Policy	Assigned policies to require environment and owner tags on all resources, restrict VM sizes to cost-efficient SKUs, and restrict deployments to East US only
Cost Management / Budgets	Created a $20/month budget with email alerts at 80% actual, 100% actual, and 110% forecasted spend
Resource Groups	Used existing resource groups (rg-Dev, rg-Ops, rg-Finance) as the deployment target to test that each policy correctly blocked non-compliant resources

**Key Concepts Demonstrated**
•	Policy as code 
•	Tagging strategy 
•	Deny effect 

Step-by-Step Summary
1.	Created a Management Group hierarchy (mg-company → mg-production) and moved my subscription under it
2.	Assigned a 'Require a tag on resources' policy twice — once for environment, once for owner
3.	Assigned an 'Allowed virtual machine size SKUs' policy restricting VMs to B1s/B1ms/B2s
4.	Assigned 'Allowed locations' policies restricting deployments to East US
5.	Created a $20/month budget with email alerts at 80%, 100%, and 110% forecasted
6.	Tested every policy by trying to violate it and capturing the result

**Screenshots:**

1. Management Group hierarchy
 <img width="975" height="333" alt="image" src="https://github.com/user-attachments/assets/2927bdbc-6dd8-4c70-8d35-47f843d667f5" />

*Created a two-level hierarchy (mg-company → mg-production) and moved my subscription under it*

2. Created Policies
<img width="975" height="317" alt="image" src="https://github.com/user-attachments/assets/ea7dbc57-634b-4e8e-8cdc-7dbd9e423b2e" />

 
*Policies to require environment and owner tags on all resources, restrict VM sizes to cost-efficient SKUs, and restrict deployments to East US only*

3. Policy blocked — missing tags
 <img width="975" height="394" alt="image" src="https://github.com/user-attachments/assets/a2e218f0-311c-44ef-afd6-4cd57354f227" />

*Tried to create a storage account without Tags and was blocked by policy.* 

4. Policy blocked — disallowed location
 <img width="975" height="1141" alt="image" src="https://github.com/user-attachments/assets/94a22caa-3d04-40dc-b677-f5ab6ab43075" />

*Tried to create a storage account in Central US and policy blocked it because only East US was allowed.*

5. Policy blocked — disallowed VM size
 <img width="983" height="302" alt="image" src="https://github.com/user-attachments/assets/bf9c7e79-52e0-4a9a-b583-9941fd250824" />

*Policy blocks certain VM sizes.*

6. Policy compliance dashboard
 <img width="973" height="330" alt="image" src="https://github.com/user-attachments/assets/00ac67ce-5b3e-45b9-8a5c-33328e020d1f" />

*Dashboard shows all policies are compliant.* 

7. Budget alert configuration
 <img width="972" height="273" alt="image" src="https://github.com/user-attachments/assets/56d27624-1001-41cf-95f7-6558842a2947" />

*Set up a budget alert to avoid overspending during the following projects.*

**What Broke and How I Fixed It**

Problem 1: 
No major issues during this project but I did notice that policy compliance evaluation isn't instant. After assigning the policies, the Compliance dashboard initially showed 'Not started' rather than reflecting my test results right away. I had to manually trigger an evaluation scan and wait several minutes before the dashboard updated. This taught me that policy enforcement  and policy compliance reporting  operate on different timelines.

**What I Learned**

This project made me understand how powerful Management Group hierarchy and Azure Policy truly are. In a real-world environment with many professionals working on the same tenant, being able to set these boundaries allows for better consistency, cost control, and accountability instead of relying on individual admins to remember to tag resources or pick the right region, the platform enforces it automatically. This is the kind of governance that scales works the same whether you have 5 resources or 5,000.

**Cost:**

Resource	Approximate Cost
Management Groups, Azure Policy, Budgets	Free
Total for this lab	$0.00

**Resources and References**
•	Microsoft Learn — Implement Azure Policy
•	AZ-104 Study Guide — Identity and Governance section

*Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.*
