---
lab:
    title: 'Lab: Migrating server workloads'
    type: 'Answer Key'
    module: 'Module 12: Upgrade and migration in Windows Server'
---

# Lab answer key: Migrating server workloads

## Exercise 1: Selecting a process to migrate server workloads

### Task 1: Study the scenario

1. Study the lab scenario.
1. Study the exercise scenario.

### Task 2: Plan how to update domain controllers to Windows Server 2019

Answer the following questions based on the scenario:

1. To implement domain controllers running Windows Server 2019, should you upgrade the existing Active Directory Domain Services (AD DS) forest or migrate to a new AD DS forest?

    **Answer:** It's rare to migrate to a new AD DS forest. If your main goal is to update domain controllers to a new version of Windows Server, you should update AD DS in the existing by adding domain controllers running Windows Server 2019. You should only consider migrated to a new AD DS forest when restructuring of domains or forests is required. For example, when two companies merge, the AD DS forest of one company might be migrated into the other.

1. What are the highest domain and forest functional levels that you can implement?

    **Answer:** The highest domain and forest functional levels that you can implement are Windows Server 2016. There is no Windows Server 2019 functional level.

1. Which domain controller operating systems can you use to implement the highest possible domain and forest functional levels?

    **Answer:** You can use domain controllers running Windows Server 2016 and Windows Server 2019 in a domain or forest at the Windows Server 2016 functional level.

1. What steps do you need to take before adding domain controllers running Windows Server 2019 to an existing AD DS forest?

    **Answer:** If you have the correct permissions, you don't need to perform any steps before you install the first domain controller running Windows Server 2019. The domain controller promotion process automatically prepares the forest and domain. However, you do have the option to prepare the domain and forest manually. To prepare the AD DS forest you run Adprep /forestprep. Then you can prepare the domain by running Adprep /domainprep. In a multidomain environment, you need to prepare each domain.

1. What do you need to consider when removing domain controllers running previous Windows Server versions?

   **Answer:** For normal domain authentication, domain controllers are located by using DNS records. Those DNS records automatically update when domain controllers are added or removed. So, basic authentication doesn't require any special steps when removing a domain controller. However, because domain controllers are often used for DNS you need to ensure that clients and servers are updated to use the IP addresses of the new domain controllers. Additionally, some apps are configured to use specific domain controllers as Lightweight Directory Access Protocol (LDAP) servers for authentication. Those apps also need to be reconfigured with the IP address or name of new domain controllers.

### Task 3: Plan how to migrate other server workloads

Answer the following questions based on the scenario:

1. What steps do you need to perform before running the Windows PowerShell cmdlets in the Windows Server Migration Tools on Windows Server 2019.

   **Answer:** To use the Windows Server Migration Tools on Windows Server 2019 you need to install the Windows Server Migration Tools feature. Then before you can use the cmdlets, you need to load the Windows PowerShell snap-in containing the cmdlets by running Add-PSSnapin Microsoft. Windows.Windows.ServerManager.Migration at a Windows PowerShell prompt.

1. What steps do you need to perform on a source server running Windows Server 2012 R2 before you can use the Windows PowerShell cmdlets in the Windows Server Migration Tools?

   **Answer:** To install the Windows Server Migration Tools on a down-level server, you need to run SmigDeploy.exe to create a deployment folder for that specific operating system. The deployment folder copies to the source server and installs by running SmigDeploy.exe from the deployment folder. Then you can load the snap-in for the Windows Server Migration Tools at a Windows PowerShell prompt on the source server.

1. Which cmdlet can you use to verify which features can be migrated from a source server?

   **Answer:** The Get-SmigServerFeature cmdlet lists the Windows features that can be migrated from either a local computer or a migration stored.

1. List the high-level steps for using the Windows Server Migration Tools to migrate settings from a source server to a destination server.

   **Answer:** To migrate feature configuration from a source server to a destination server, you begin by installing the feature on the target server. Then you run Export-SmigServerSetting on the source server and Import-SmigServerSetting on the destination server.

## Exercise 2: Planning how to migrate files by using Storage Migration Service

### Task 1: Study the scenario

1. Study the lab scenario.
1. Study the exercise scenario.

### Task 2: Plan the migration of file servers

Answer the following questions based on the scenario:

1. Can you use Storage Migration Service to migrate files shares from Windows Server 2003 to Windows Server 2019?

   **Answer:** Yes, Storage Migration Service supports migrating file shares from Windows Server 2003 or newer versions of Windows Server.

1. Can you use Storage Migration Service to migrate files on Linux servers?

   **Answer:** Yes, if the source Linux servers are providing file shares accessible to Windows clients by using Samba. Storage Migration Service can't migrate files on Linux servers using only NFS.

1. Can you use Storage Migration Service to combine multiple file servers to a single new server?

   **Answer:** No. Storage Migration Service doesn't have the ability to merge the identities of multiple servers onto a single server.

1. Can you use Storage Migration Service to migrate file shares to a virtual machine in Azure?

    **Answer:** Yes. Storage Migration Service can migrate file shares to a virtual machine in Azure. If Azure is properly configured, Storage Migration Service can create the virtual machine automatically based on specifications that you provide.

### Task 3: Plan how to use Storage Migration Service

Answer the following questions based on the scenario:

1. What software do you need to install to use Storage Migration Service?

     **Answer:** To use Storage Migration Service, you need to install the Storage Migration Service feature on the orchestrator server. On the destination server running Windows Server 2019, you should also install the Storage Migration Service Proxy feature. No software needs to install on the source server.

1. What firewall configuration do you need to implement to use Storage Migration Service?

     **Answer:** The Storage Migration Service needs to copy data and configure the source and destination servers. When you install the Storage Migration Service Proxy on a destination server, the firewall is configured automatically, but you should verify. On source and destination server, the following firewall rules must be enabled: File and Printer Sharing (SMB-In), Netlogon Service (NP-In), Windows Management Instrumentation (DCOM-In), Windows Management Instrumentation (WMI-In). On the orchestrator server, the File and Printer Sharing (SMB-In) rule needs to be enabled.

1. What accounts and permissions must be configured to use Storage Migration Service?

     **Answer:** To perform the migrations, you can use a single account that has administrator permissions on the source server, the orchestrator server, and the destination server. Alternatively, you can split the accounts into a source migration account and a destination migration account. A source migration account needs to have administrator permissions on the source server and the orchestrator server. A destination migration account needs to have administrator permissions in the destination server and the orchestrator server.

1. Which tool do you use to create and manage jobs?

     **Answer:** Storage Migration Service jobs are created and managed from Windows Admin Center in the Storage Migration Service node.

1. What is the relationship between volumes in the source server and the destination server?

     **Answer:** A volume on the source server maps to a volume on the target server. If there are three source volumes, there must be three destination volumes. There is no logic for renaming folders with conflicting names.

1. After cutover, which identity information moves from the source server to the destination server?

     **Answer:** The name and IP addresses of the source server are moved to the destination server. The source server is renamed and give a new IP address.

1. Which data won't be migrated from the source server to the destination server?

     **Answer:** Storage Migration Service can't copy locked files. So, if users have a file open during a copy attempt, the file won't be migrated. Previous versions of files also aren't migrated.
