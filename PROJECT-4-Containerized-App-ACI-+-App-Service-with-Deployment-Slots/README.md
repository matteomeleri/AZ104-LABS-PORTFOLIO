**PROJECT 4**

**Containerized App — ACI + App Service with Deployment Slots
Compute  |  Azure Container Instances, App Service, Deployment Slots, Zero-Downtime Swap**

**What I Built:**

I deployed a containerized web application two ways. First I used Azure Container Instances (ACI) to run an Nginx container as a fast serverless deployment, which means no infrastructure to manage and running within seconds. Then I deployed the same application to Azure App Service with a staging deployment slot, changed the staging slot to run Apache (httpd) to simulate a new application version, and performed a zero-downtime slot swap to push the new version to production. The slot swap is the key real-world technique because it allows a new version to be tested in staging before being promoted to production with no downtime, and rolled back instantly if needed.

**Architecture Diagram**
 
<img width="975" height="691" alt="image" src="https://github.com/user-attachments/assets/84377bf1-c284-4278-a0f8-dcdf0cd0871a" />


**Azure Services Used**

Azure Container Instances (ACI): Deployed an Nginx container as a fully managed serverless container — no VM, no App Service Plan required. Used to demonstrate the fastest way to run a container in Azure

App Service Plan (Premium V4): The underlying compute that hosts the App Service web app. Premium V4 tier required to support deployment slots — Basic tier does not support this feature

Azure App Service (Web App): Hosted the containerized web application with built-in deployment slot support, auto-scaling, and managed TLS

Deployment Slots: Created a staging slot as a separate environment to test a new container image before promoting it to production via a zero-downtime swap
	

**Key Concepts Demonstrated**

•	ACI vs App Service — ACI is best for short-lived, stateless, or batch workloads with no infrastructure management. App Service is better for long-running production web applications that need deployment slots, auto-scaling, and managed certificates

•	Deployment slots — each slot is a fully independent App Service instance with its own URL, settings, and container image. Swapping slots redirects traffic instantly at the routing layer with zero downtime

•	Zero-downtime deployment — by deploying a new version to staging first, testing it, then swapping, production users never see downtime or a failed deployment

•	One-click rollback — if the new version has a problem after swapping, swapping again reverts instantly — no redeployment needed

•	Standard tier or above required for slots — Basic App Service Plans do not support deployment slots. This is a common AZ-104 exam question and a real-world gotcha

**Step-by-Step Summary**

1.	Created resource group rg-containers-lab in West US 2

2.	Deployed an ACI container (aci-web) running nginx:latest with a public DNS name label and port 80 open

3.	Accessed the ACI container via its FQDN in the browser and confirmed Nginx was serving

4.	Viewed container logs and connected to the running container via the browser-based terminal in the Portal

5.	Created an App Service Plan (asp-lab) on Premium V4 tier in West US 2 — chosen after East US and East US 2 were blocked by quota limits

6.	Created a Web App (webapp-az104-matteo) running the NGINX Quickstart container image on port 80

7.	Created a staging deployment slot cloned from the production slot settings

8.	Navigated to Deployment Center on the staging slot and changed the container image to httpd:latest to simulate a new application version

9.	Visited both production and staging URLs to confirm they were running different container images

10.	Performed a zero-downtime slot swap — production now runs Apache, staging runs Nginx

11.	Performed a second swap to roll back — confirmed production returned to Nginx

12.	Deleted resource group rg-containers-lab to stop all billing

**Screenshots:**

1. ACI container running — Overview page showing FQDN and Running status

 <img width="975" height="375" alt="image" src="https://github.com/user-attachments/assets/7bdca375-18a7-4b85-a8d8-e5afa57a8a22" />

*aci-web container instance showing Running status, FQDN, and public IP on the Overview page.*

2. ACI Nginx page in browser

 <img width="975" height="227" alt="image" src="https://github.com/user-attachments/assets/10fcd05b-6443-40af-898b-e1ddeba05a17" />

*Visiting the ACI FQDN in the browser returns the Nginx welcome page, confirming the container is running and accessible.*

3. App Service deployment slots — production and staging listed

 <img width="761" height="222" alt="image" src="https://github.com/user-attachments/assets/1c94e652-4f62-4a66-a1a9-7d8f659699eb" />

*Deployment slots page showing both the production slot and staging slot, each with their own separate URL.*

4. Staging URL showing Apache (httpd) vs Production URL showing Nginx — before swap

 <img width="975" height="186" alt="image" src="https://github.com/user-attachments/assets/ab7e97f4-f186-4438-bc7f-b29e36c7e86e" />

*Production slot serving Nginx before the slot swap.*

5. Slot swap confirmation screen

 <img width="975" height="219" alt="image" src="https://github.com/user-attachments/assets/93cb3695-f71f-4c84-9a3e-b74d8641a855" />

*The swap confirmation dialog showing source (staging) and target (production).* 

6. Staging & Production URLs — after swap

 <img width="975" height="214" alt="image" src="https://github.com/user-attachments/assets/49ba7db2-ccb5-4f57-965b-11826b6f0fc8" />

*Production slot now serving Apache after the swap completed — the new version is live with zero downtime.*

**What Broke and How I Fixed It**

Problem 1: App Service quota limit — East US and East US 2 both blocked

•	What happened: Clicking Create returned error 'Operation cannot be completed without additional quota. Current Limit (Total VMs): 0' for both East US and East US 2

•	What I tried: Attempted to request a quota increase via the MyQuotas page but received 'You don't have permissions to adjust quotas. You must be assigned the Contributor role or higher'

•	What actually fixed it: Changed the region to West US 2 which had available quota. App Service region does not need to match other lab resources since this project is standalone — any region works

Problem 2: Container image settings not visible in the Portal UI

•	What happened: The guide referenced a 'Container settings' blade in the left menu for changing the container image on the staging slot, but this option did not exist in the current Portal

•	What I tried: Checked Configuration → General settings and Environment variables — neither had container image settings

•	What actually fixed it: Found the container image configuration under Deployment Center in the left menu. This is where the current Azure Portal manages container image settings for App Service — the UI was reorganized since the original guide was written

**What I Learned**

This project made the difference between ACI and App Service genuinely clear to me, ACI was running within about 60 seconds with no infrastructure decisions to make, which is impressive, but it has no concept of deployment slots, auto-scaling policies, or managed certificates. App Service trades that simplicity for production-grade features like zero-downtime deployments. The slot swap was the most interesting part of the lab: what looks like a complex operation is actually just Azure redirecting routing rules at the load balancer level, which is why it completes in seconds with no downtime. I also learned two practical lessons. First, that quota limits are region-specific and switching regions is often faster than requesting an increase, and second, that the Azure Portal UI changes frequently enough that feature locations shift between guide versions, making it important to know what you're looking for conceptually rather than just following menu paths by memory.

**Lab Specifications**

ACI container image	nginx:latest

ACI region	East US

App Service region	West US 2 (East US blocked by quota)

App Service Plan tier	Premium V4 — Extra Small

Production slot image	NGINX Quickstart (mcr.microsoft.com/appsvc/staticsite:latest)

Staging slot image	httpd:latest (Apache)

Container image management	Deployment Center

Port:	80

**Cost**

ACI container (~2 hr)	~$0.003 — negligible

App Service Plan Premium V4 (~3 hr)	~$0.50–0.60

Deployment slots	Included in App Service Plan

Total for this lab	~$0.60 or less

**Resources and References**

•	Microsoft Learn — Configure Azure App Service deployment slots

•	Microsoft Learn — Run container instances in Azure

•	AZ-104 Study Guide — Deploy and manage Azure compute resources

Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.
