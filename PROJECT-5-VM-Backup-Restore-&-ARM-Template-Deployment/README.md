**PROJECT 5**

**VM Backup, Restore & ARM Template Deployment
Compute  |  ARM Templates, Azure Backup, Recovery Services Vault, VM Restore**

**What I Built**

I deployed a VM (vm-backup-test) and exported its ARM template as a reusable Infrastructure as Code artifact, and successfully redeployed it into a resource group by fixing the exported parameters.json file. I then configured Azure Backup on the VM using a Recovery Services Vault with a custom daily backup policy, triggered an on-demand backup, and performed a full VM restore creating an identical vm-restored instance from the backup recovery point. This project covers two major AZ-104 exam areas in one lab: Infrastructure as Code (ARM templates) and backup and recovery (RPO/RTO concepts, Recovery Services Vault, and the three restore types available in Azure Backup).

**Architecture Diagram**

 <img width="975" height="689" alt="image" src="https://github.com/user-attachments/assets/65fb837e-467c-4f0d-9169-2291ae665f35" />


**Azure Services Used**

Virtual Machine (vm-backup-test)	The primary VM deployed in Central US using Standard_D2s_v3 — the source for both the ARM template export and the backup configuration

ARM Template	Exported the VM configuration from the Portal as template.json and parameters.json, which needed editing before redeployment. 

Custom Deployment (Portal)	Used the ‘Deploy a custom template’ feature to redeploy the original template.json with a fixed parameters.json, creating a VM by using an ARM template

Recovery Services Vault (rsv-backup-lab)	The central backup management resource — stores backup data, manages backup policies, and orchestrates restore operations. Must be in the same region as the protected VMs

Azure Backup Policy	Created a custom daily backup policy retaining backups for 30 days daily, 12 weeks weekly, and 12 months monthly

Storage Account (stbackplab1234)	Created as the staging location required for the VM restore operation — Azure uses this temporarily to stage the restored disk before creating the new VM

Restored VM (vm-restored)	A new VM created from the backup recovery point — proves the backup is valid and the restore process works end to end

**Key Concepts Demonstrated**

•	ARM templates as Infrastructure as Code — an exported ARM template captures a VM's complete configuration in JSON format, allowing the same infrastructure to be recreated in any subscription or region with consistent settings

•	Recovery Services Vault region requirement — the vault must be in the same region as the VMs it protects. Cross-region restore is possible but requires additional configuration and is a separate feature

•	Three VM restore types in Azure Backup — (1) Create new virtual machine: builds a complete new VM from the backup. (2) Restore disks: restores only the disk, which you then attach to an existing VM. (3) File recovery: mounts the backup as a drive to recover individual files without restoring the whole VM

•	RPO and RTO — Recovery Point Objective (RPO) is how much data you can afford to lose, defined by backup frequency. Recovery Time Objective (RTO) is how long a restore takes. Daily backups give an RPO of up to 24 hours; instant restore snapshots reduce RTO to minutes

•	Staging storage account — Azure requires a storage account as a temporary staging location when restoring a VM. It is used during the restore process and can be deleted afterward

•	Cost management discipline — previous lab resources and storage accounts were deleted between projects to control costs on a Pay-As-You-Go subscription, requiring the staging storage account to be recreated for this restore

**Step-by-Step Summary**

1.	Created resource group rg-backup-lab in Central US (East US quota exhausted from previous labs)

2.	Deployed vm-backup-test using Standard_D2s_v3 on Ubuntu 24.04 LTS — B-series sizes unavailable, D2s_v3 was cheapest available option

3.	On the Review + create screen clicked 'Download a template for automation' to export the ARM template before deploying

4.	Saved template.json and parameters.json to project folder as the IaC artifact

5.	ARM template redeployed successfully after fixing parameters.json file.

6.	Created Recovery Services Vault rsv-backup-lab in Central US (same region as vm-backup-test)

7.	Created a custom backup policy with daily backups at 11 PM, retaining 30 days daily / 12 weeks weekly / 12 months monthly

8.	Enabled Azure Backup on vm-backup-test using the custom policy

9.	Triggered an on-demand backup and monitored the job in Recovery Services Vault → Backup jobs until it completed

10.	Created storage account stbackplab1234 in Central US as the staging location for the restore — previous storage accounts had been deleted for cost management

11.	Performed a VM restore selecting 'Create new virtual machine', naming it vm-restored, and using stbackplab1234 as the staging location

12.	Confirmed vm-restored appeared in the resource group after the restore job completed

13.	Deleted vm-restored immediately after taking screenshots to avoid unnecessary VM charges

**Screenshots**

1. Used ARM template to Create VM 

 <img width="973" height="775" alt="image" src="https://github.com/user-attachments/assets/50f1c8a7-f781-4c2d-bcda-b7a8a1db2124" />

*Uploaded Template.json to create a VM using an ARM template.*

 <img width="973" height="955" alt="image" src="https://github.com/user-attachments/assets/4f4602ec-eae6-4431-82e4-3c5d50ed8e5d" />

*Uploaded Parameters.json file to upload configuration of the VM.*

 <img width="973" height="1184" alt="image" src="https://github.com/user-attachments/assets/c94a3387-a5a9-49ac-aa08-f51a3f6501a9" />

*ARM template validation successful with no errors.*

 <img width="973" height="583" alt="image" src="https://github.com/user-attachments/assets/962e5288-df7f-4b42-bd8d-132f55bde1f0" />

*Vm Successfully created Using ARM Template.*

  

2. Recovery Services Vault overview

 <img width="973" height="563" alt="image" src="https://github.com/user-attachments/assets/bf9b4b73-1ffb-4e87-a52e-baa70bdc7b3e" />

*rsv-backup-lab created in Central US showing the vault is ready to protect resources.*

3. Backup policy configuration

 <img width="973" height="1111" alt="image" src="https://github.com/user-attachments/assets/c0d59c79-ecc6-4f08-8bf6-1d46b35c8943" />

*Custom backup policy showing daily schedule, instant restore settings, and retention ranges for daily, weekly, and monthly recovery points.*

4. Backup enabled on vm-backup-test

 <img width="975" height="417" alt="image" src="https://github.com/user-attachments/assets/f8a2969c-2f48-4773-906a-3887c8e4feef" />

*vm-backup-test showing backup is enabled with the custom policy assigned and last backup status.*

5. On-demand backup job completing successfully

 <img width="973" height="305" alt="image" src="https://github.com/user-attachments/assets/bdddfff3-da82-420e-8cc4-9dc31f8e39db" />

*Backup jobs page showing the on-demand backup job for vm-backup-test completed successfully with duration and recovery point details.*

6. Restore job completing successfully

 <img width="975" height="320" alt="image" src="https://github.com/user-attachments/assets/75076f32-3885-4474-b22a-c9aaff7cd540" />

*Backup jobs page showing the restore job completed successfully.*

7. vm-restored appearing in the resource group

 <img width="975" height="641" alt="image" src="https://github.com/user-attachments/assets/8bc426ae-9834-4fd6-9f20-7d9891f0e3ee" />

*rg-backup-lab resource group showing vm-restored created from the backup — proves the restore completed end to end.*

**What Broke and How I Fixed It**

Problem 1: East US vCPU quota exhausted — forced to use Central US

•What happened: Attempting to create a VM in East US returned a quota error. East US vCPU quota was exhausted from previous lab projects.

•	What I tried: Checked MyQuotas page but lacked permissions to request an increase directly. Considered submitting a support request but decided against it to save time.

•	What actually fixed it: Deployed all Project 5 resources in Central US instead. This introduced a secondary problem — the Project 2 'Allowed locations for resource groups' policy blocked Central US resource groups, requiring the policy scope to be considered. Created rg-backup-lab in Central US by accepting the policy conflict was limited to this specific project's resource group.

Problem 2: ARM template redeployment blocked by Project 2 location policy

•	What happened: First redeployment attempt failed, the exported parameters.json had adminPublicKey set to null.

•	What I tried: Attempted multiple fixes including editing the parameters file and using a clean template, all failed until I identified that the original template.json was actually valid and only the parameters.json needed updating.

•	What actually fixed it: Edited parameters.json to insert the SSH public key extracted from the downloaded .perm file

Problem 3: No staging storage account available for VM restore

•	What happened: When attempting to restore the VM, the staging storage account dropdown was empty — all storage accounts from previous labs had been deleted between projects to manage costs on the Pay-As-You-Go subscription.

•	What I tried: Checked if an existing storage account could be used — none were available in the subscription.

•	What actually fixed it: Created a new Standard LRS storage account (stbackplab1234) in Central US matching the vault region. Selected this as the staging location in the restore configuration. The restore completed successfully after this was in place.

**What I Learned:**

The most valuable lesson from this project came from the ARM template troubleshooting, in fact the original exported template.json was perfectly valid the whole time, the problem was entirely in the parameters.json file. This taught me to always separate template problems from parameters problems when debugging ARM templates. Also, this project made the Recovery Services Vault's region requirement very concrete, in fact I had to think carefully about keeping the vault, the VM, and the staging storage account all in the same region (Central US) for the backup and restore to work correctly. I also learned the practical difference between the three restore types: creating a new VM is the full recovery option, restoring disks is useful when you want to recover data without replacing the whole VM, and file recovery is the fastest option when you only need a specific file rather than the entire machine. The ARM template export gave me a clear picture of what Infrastructure as Code actually looks like in practice, the JSON file captures every detail of the VM configuration including networking, security settings, and disk type, which is what makes it redeployable. The quota constraints across this project series taught me something genuinely useful: in real enterprise environments, quota management is a routine administrative task, and knowing how to work within quota limits or request increases is a practical Azure admin skill.

**Lab Specifications**

VM size	Standard_D2s_v3 (B-series unavailable)

OS image	Ubuntu Server 24.04 LTS

Region	Central US (East US quota exhausted)

Recovery Services Vault	rsv-backup-lab — Central US

Backup frequency	Daily at 11:00 PM UTC

Retention — daily	30 days

Retention — weekly	12 weeks

Retention — monthly	12 months

Restore type used	Create new virtual machine

Staging storage account	stbackplab1234 — Standard LRS — Central US

Restored VM name	vm-restored

ARM template redeployment	Skipped — region/policy conflict documented

**Cost**

vm-backup-test D2s_v3 (~4 hr)	~$0.38

vm-restored D2s_v3 (~30 min)	~$0.05

Recovery Services Vault	Free

Backup storage (1 small VM backup)	~$0.02–0.05

Storage account stbackplab1234	~$0.01

Total for this lab	~$0.50 or less


**Resources and References**

•	Microsoft Learn — Back up Azure virtual machines

•	Microsoft Learn — Restore Azure virtual machines

•	Microsoft Learn — Deploy resources with ARM templates

•	AZ-104 Study Guide — Deploy and manage Azure compute resources

Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.
