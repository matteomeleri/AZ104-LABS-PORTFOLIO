PROJECT 1
Secure Multi-Team Azure Environment
Identity & Governance  |  Azure AD / Entra ID, RBAC, MFA, Conditional Access

What I Built
I built a secure multi-team Azure environment with three user groups (rg-Dev – rg-Ops – rg-Finance), each restricted to their own resource group using role-based access control. I enforced MFA for all users using Security Defaults and documented the licensing limitations I hit during this project.
Architecture Diagram
 <img width="975" height="592" alt="image" src="https://github.com/user-attachments/assets/0179c522-b774-4b0e-a5a7-08a477559c73" />

Azure Services Used
Service	Purpose
Microsoft Entra ID	Created security groups (grp-Dev, grp-Ops, grp-Finance) and test users, then assigned users to their respective groups
RBAC (Role-Based Access Control)	Assigned Contributor role to grp-Dev and grp-Ops, and Reader role to grp-Finance, each scoped to their own resource group
Security Defaults / Conditional Access	Enabled Security Defaults to enforce MFA for all users tenant-wide, Conditional Access requires P1/P2, while Security Defaults is the free alternative with less granularity
Resource Groups	Created rg-Dev, rg-Ops, and rg-Finance to logically separate and scope access for each team

Key Concepts Demonstrated
•	Principle of least privilege 
•	Group-based RBAC 
•	MFA enforcement 
Step-by-Step Summary
A high-level overview of what I did — not a full tutorial, just enough context to follow the screenshots.
1.	Created 9 users (3 for each department)
2.	Created three Entra ID security groups and assigned users to groups
3.	Created three resource groups: rg-Dev, rg-Ops, rg-Finance
4.	Assigned RBAC roles (Contributor for rg-Dev and rg-Ops/ Reader for rg-Finance) scoped to each resource group
5.	Verified access boundaries by logging in as a test user in a private browser session
6.	Enabled Security Defaults to enforce MFA for all users tenant-wide
Screenshots
1. Created 9 Users in Entra ID
 <img width="975" height="423" alt="image" src="https://github.com/user-attachments/assets/64ba0d5b-5f75-4c39-a209-c4a0fe509d0a" />

Created 9 users, 3 for each group
2. Three Groups Created
 <img width="972" height="338" alt="image" src="https://github.com/user-attachments/assets/5547f25a-29e8-4060-9e09-e85ce5cdd3af" />

Created three groups (grp-Dev, grp-Ops, grp-Finance) each containing three users

3. Resource Groups Created
 <img width="973" height="252" alt="image" src="https://github.com/user-attachments/assets/627317ba-bc0c-477b-bb85-83584e7e8af0" />

Resource groups created (rg-Dev, rg-Ops, rg-Finance)
4. Assigned each group to its corresponding Resource group
 <img width="975" height="561" alt="image" src="https://github.com/user-attachments/assets/29e6c28c-78a9-49ef-a7cf-95b73abb8696" />

 Assigned grp-Dev group with contributor role to Resource Group rg-Dev





<img width="973" height="603" alt="image" src="https://github.com/user-attachments/assets/0cd6a300-90f5-4631-aa30-2169b3f7855b" />


Assigned group grp-Finance with Reader role to Resource Group rg-Finance
 <img width="975" height="614" alt="image" src="https://github.com/user-attachments/assets/fbeb561e-bd8a-406a-8445-bb4748c15677" />

Assigned group grp-Ops with Contributor role to Resource Group rg-Ops
4. Logged in with user from Dev Group to verify role inheritance
 <img width="972" height="233" alt="image" src="https://github.com/user-attachments/assets/cfc4eafe-4ba2-4e4a-8d0f-ecd686a1c8d5" />

Logged in as user from the grp-Dev group to verify role inheritance, after logging in as dev.user1 only rg-Dev is visible meaning the role inheritance was successful.  
5. User from Dev Group cannot access other Resource Groups
 <img width="975" height="433" alt="image" src="https://github.com/user-attachments/assets/41bee181-8e07-4736-9260-be5af8b15981" />

Logged in as user from the grp-Dev group it shows that I have no access to other resource groups proving once again successful role inheritance from the Resource group. 
6. Enabled Security defaults
 <img width="725" height="803" alt="image" src="https://github.com/user-attachments/assets/acf5aca1-c34e-4302-a525-93f3ffda383a" />

Security defaults enforces multifactor authentication tenant wide
7. Logged in as finance.user1 to check enforcement of multifactor authentication
 <img width="973" height="1058" alt="image" src="https://github.com/user-attachments/assets/34b604c5-09b2-4dfc-8c14-afc5125b23b8" />

Originally planned to use Conditional Access for more granular multifactor authentication and location policy but P1/P2 license was required. Decided to opt for Security Defaults which enforced multifactor authentication for free. 



What Broke and How I Fixed It
Problem 1: License Issue
•	Originally planned to use Conditional Access to set a location policy which would restrict logging to the US only. Unfortunately, a P1/P2 license was required and decided to use Security Defaults which enforces multifactor authentication for free, but unfortunately doesn’t allow for custom rules to be implemented. 
What I Learned
This project gave me a deep understanding of how roles are inherited from Resource groups, and how groups and Resource groups can be used to manage users in a very granular way. Also, I’ve learned the practical difference between Entra ID Premium-only features and free alternatives like Security Defaults. 
Cost
Resource	Approximate Cost
Entra ID, RBAC, Security Defaults	Free
Total for this lab	$0.00

Resources and References
•	Microsoft Learn 
•	AZ-104 Study Guide — Identity and Governance section

Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.

