**PROJECT 3**

**Auto-Scaling Web App with Load Balancer
Compute  |  VM Scale Sets, Azure Load Balancer, Autoscale, Cloud-Init**

**What I Built**

I deployed a VM Scale Set running Nginx web servers behind an Azure Standard Load Balancer. I configured autoscale rules to automatically add VMs when CPU exceeds 70% and remove them when it drops below 30%. Instead of using a Custom Script Extension, I used cloud-init (Custom data) to install and configure Nginx automatically on every instance at first boot a more reliable approach I discovered after troubleshooting the extension method. I also registered required Azure resource providers that were missing from my new subscription.

**Architecture Diagram:**

<img width="975" height="559" alt="image" src="https://github.com/user-attachments/assets/d379ddf3-03d9-40e3-9a6c-d2743112b322" />

 
**Azure Services Used**

• Virtual Network + Subnet	Created vnet-lab with subnet-backend (10.0.1.0/24) to host the scale set instances privately with no public IPs on individual VMs

• Public IP Address (Standard)	Created pip-lb as a static Standard SKU public IP — the single entry point for all web traffic into the environment

• Azure Load Balancer (Standard)	Distributed incoming HTTP traffic across all VM instances using a health probe on port 80 to detect and route around unhealthy VMs

• VM Scale Set (VMSS)	Deployed multiple identical Ubuntu 24.04 VMs behind the load balancer, starting with 2 instances and configured to scale between 1 and 5

• Cloud-Init (Custom data)	Used a bash script in the Custom data field to automatically install Nginx, enable it as a service, and write a hostname-specific index.html on every instance at first boot

• Autoscale	Configured scale-out (CPU > 70% → add 1 VM) and scale-in (CPU < 30% → remove 1 VM) rules with 5-minute cooldowns via Azure Monitor

• Network Security Group	Added inbound rules manually to allow HTTP (port 80) and SSH (port 22) traffic to reach the VM instances

• Microsoft.Insights provider	Registered the missing resource provider on the subscription to enable autoscale and Azure Monitor functionality

**Key Concepts Demonstrated**

•	VM Scale Sets vs individual VMs — VMSS manages a group of identical VMs as a single resource, making scaling and updates far simpler than managing each machine separately

•	Load balancer health probes — the load balancer continuously checks each VM on port 80 and automatically removes unhealthy instances from the rotation until they recover

•	Cloud-init vs Custom Script Extension — cloud-init runs at first boot as part of Ubuntu's built-in startup system and is more reliable than the Azure Custom Script Extension for initial VM configuration

•	Autoscale cooldown period — the 5-minute cooldown prevents rapid back-and-forth scaling (thrashing) when CPU fluctuates around the threshold boundary

•	Resource provider registration — Azure services like autoscale require specific resource providers (microsoft.insights) to be registered on the subscription before they can be used

•	NSG rules and traffic flow — without an explicit inbound allow rule for port 80, the load balancer's public IP cannot reach the VMs even if everything else is configured correctly

**Step-by-Step Summary**

1.	Created resource group rg-scaleset-lab, virtual network vnet-lab with subnet-backend

2.	Created a Standard public IP (pip-lb) separately before the load balancer

3.	Created Standard Load Balancer lb-web with frontend IP config, backend pool be-pool, HTTP health probe on port 80, and load balancing rule for port 80

4.	Created VM Scale Set vmss-web with 2 instances, linked to lb-web backend pool at creation time, with cloud-init script in Custom data field to auto-install Nginx

5.	Manually added NSG inbound rules for port 80 (HTTP) and port 22 (SSH) after deployment

6.	Waited 5 minutes for cloud-init to complete, then confirmed Nginx was accessible via the load balancer public IP

7.	Verified load balancing by refreshing the browser and observing different hostnames served from different VM instances

8.	Registered microsoft.insights, microsoft.operationalinsights, and microsoft.alertsmanagement resource providers on the subscription

9.	Configured autoscale rules via Azure Monitor — scale out at CPU > 70%, scale in at CPU < 30%, with min 1 / max 5 / default 2 instances

10.	Ran CPU stress test on one instance to trigger and validate the scale-out rule

11.	Deleted resource group rg-scaleset-lab to stop all billing

**Screenshots:**

All Resources have been created inside a single Recource Group

<img width="973" height="527" alt="image" src="https://github.com/user-attachments/assets/61d50565-3bcb-406f-bcbe-1e1b4527fbee" />

 
1. VM Scale Set overview showing 2 running instances

 <img width="975" height="216" alt="image" src="https://github.com/user-attachments/assets/cdca2f2b-e827-44be-b138-f7fb02110efd" />

*vmss-web deployed successfully with 2 instances running and linked to lb-web backend pool.*

2. Browser showing Nginx page with hostname — instance 1

<img width="952" height="336" alt="image" src="https://github.com/user-attachments/assets/2e11d7bd-d463-4631-b993-802533505518" />


*Visiting the load balancer public IP returns the Nginx page with the hostname of the first instance.*

3. Browser showing different hostname after refresh — instance 2

 <img width="975" height="422" alt="image" src="https://github.com/user-attachments/assets/5a7feb4f-b1cf-4a1f-966b-0e1e840b6af5" />

*Refreshing the same URL returns a different hostname, proving the load balancer is distributing traffic across both VMs.*

4. Autoscale rules configuration

 <img width="975" height="917" alt="image" src="https://github.com/user-attachments/assets/16aa9da0-80f6-49de-ab78-7dc0c4673cdb" />

*Custom autoscale configured with scale-out at CPU > 70% and scale-in at CPU < 30%, min 1 / max 5 / default 2 instances.*

5. Scale-out event firing — instance count increased to 3

 <img width="975" height="352" alt="image" src="https://github.com/user-attachments/assets/0f85f366-c337-49c1-a6c0-9b6d2f01006c" />

*This command spikes CPU for 5 minutes just to prove scaling rules are functioning correctly.*
 <img width="973" height="188" alt="image" src="https://github.com/user-attachments/assets/a5bb074c-8730-4514-a502-69fe67776e23" />

*After a couple of minutes, a third instance was created because of the CPU usage spike.*

 <img width="972" height="281" alt="image" src="https://github.com/user-attachments/assets/c5515eee-3c6e-47d0-a324-5a1704b5a739" />

*Used Run History Graph to better visualize CPU spike with corresponding third instance creation, then after CPU usage went down 30% autoscaling rules automatically reduced instances gradually to 1.*
 
<img width="975" height="242" alt="image" src="https://github.com/user-attachments/assets/abf05bf8-6644-4dbe-9418-3dfc1cc172c9" />

*Showing just one instance running after stress test was completely over.* 

6. Load balancer backend pool 

 <img width="973" height="248" alt="image" src="https://github.com/user-attachments/assets/59558d8f-a36a-45b9-af76-a52bb374cc7a" />

*Load balancer health probe.* 

7. Resource provider registration — microsoft.insights registered

 <img width="973" height="278" alt="image" src="https://github.com/user-attachments/assets/f5ded376-3a76-44e2-8954-525cfb6ec193" />

*Subscription resource providers showing microsoft.insights as Registered — required for autoscale to work.*

**What Broke and How I Fixed It**

Problem 1: B-series VM sizes not available

•	What happened: Standard_B1s and all other B-series VM sizes were unavailable when creating the scale set, with no explanation in the Portal

•	What I tried: Searched for B-series in the size picker across multiple regions

•	What actually fixed it: Used Standard_D2s_v3 instead — same Ubuntu image, identical lab outcome, slightly higher cost but still under $1 for the full session. B-series unavailability is common on new Pay-As-You-Go subscriptions due to temporary quota restrictions

Problem 2: Nginx not accessible after first deployment — timeout error

•	What happened: The load balancer public IP timed out in the browser even after the scale set showed as Succeeded

•	What I tried: Checked NSG rules, health probe, backend pool, and Custom Script Extension status — the extension showed Succeeded but Nginx was not responding

•	What actually fixed it: Deleted the entire resource group and redeployed from scratch, this time using cloud-init (Custom data field) instead of the Custom Script Extension. Also created the Public IP and Load Balancer before the Scale Set, and linked the backend pool at Scale Set creation time rather than after the fact. This sequence resolved the issue completely

Problem 3: Autoscale save failed — MissingSubscriptionRegistration error

•	What happened: Saving autoscale rules returned error: 'The subscription is not registered to use namespace microsoft.insights'

•	What I tried: Attempted to save via both the Scale Set Scaling blade and Azure Monitor Autoscale

•	What actually fixed it: Went to Subscriptions → Resource providers, searched for microsoft.insights and registered it. Also registered microsoft.operationalinsights and microsoft.alertsmanagement at the same time to prevent similar errors in later projects. After registration completed, autoscale rules saved successfully

**What I Learned:**

This project taught me more through troubleshooting than the previous two projects. The most important lesson was about deployment order, in face creating the load balancer and backend pool before the scale set, and linking them at creation time, is far more reliable than trying to attach them after the creation. I also learned the practical difference between the Azure Custom Script Extension and cloud-init: cloud-init is Ubuntu's built-in first-boot configuration system and runs more reliably than the extension layer, which can fail silently. Moreover, the resource provider registration issue was something I had never encountered before and it taught me that Azure services are not all enabled by default on new subscriptions, and that cryptic error messages “MissingSubscriptionRegistration” have straightforward fixes once you know where to look. Finally, seeing the load balancer distribute traffic across different VM instances in real time, and watching a new instance spin up automatically in response to CPU load, made the value of autoscale genuinely click in a way that reading about it never would have.

**What I'd Do Differently / Next Steps**

•	Deploy the entire stack using an ARM template or Terraform so the correct resource creation order is enforced automatically

•	Add Azure Monitor alerts to send an email notification when an autoscale event fires 

•	Replace the basic Load Balancer with an Application Gateway to add Layer 7 routing and WAF capabilities

•	Check and register all required resource providers at the start of every new project to avoid mid-lab errors

**Lab Specifications**

Setting	Value used

VM Size	Standard_D2s_v3 (B1s unavailable on new subscription)

OS Image	Ubuntu Server 24.04 LTS (22.04 LTS unavailable in Portal)

Initial instance count	2

Min / Max instances	1 / 5

Scale-out threshold	CPU > 70% for 5 minutes

Scale-in threshold	CPU < 30% for 5 minutes

Cooldown period	5 minutes

Load balancer SKU	Standard

Health probe	HTTP port 80, path /

VM configuration method	Cloud-init via Custom data field

**Cost**

Resource	Approximate cost

2x Standard_D2s_v3 VMs (~4 hr lab)	~$0.77

Standard Load Balancer	~$0.025/hr 

Public IP address	~$0.004/hr 

VNet, NSG, cloud-init	Free

Total for this lab	~$1.00 or less

**Resources and References**

•	Microsoft Learn — Configure virtual machine availability

•	Microsoft Learn — Implement Azure Load Balancer

•	Microsoft Docs — Register resource providers for Azure services

•	AZ-104 Study Guide — Deploy and manage Azure compute resources

Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.
