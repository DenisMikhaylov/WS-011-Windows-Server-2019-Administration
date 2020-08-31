---
lab:
    title: 'Lab: Migrating server workloads'
    module: 'Module 12: Upgrade and migration in Windows Server'
---

# Lab: Migrating server workloads

## Scenario

Contoso, Ltd. is an engineering, manufacturing, and distribution company. The organization is based in London, England, and it has major offices in Toronto, Canada, and Sydney, Australia.

Because Contoso has been in business for many years, the existing servers include many versions of Windows Server. You're planning to migrate those services to servers running Windows Server 2019.

## Objectives

After completing this lab, you'll be able to:

- Select a process to migrate server workloads.
- Plan how to migrate files with Storage Migration Service.

## Estimated time: 20 minutes

## Lab setup

This lab doesn't require virtual machines (VMs).

## Exercise 1: Selecting a process to migrate server workloads

### Scenario

Contoso has an Active Directory Domain Services (AD DS) forest with a single Active Directory domain named `contoso.com`. The domain controllers for the domain are running a mix of Windows Server 2012 R2 and Windows Server 2016. Many applications are installed in the domain; standardizing on using Windows Server 2019 for all domain controllers is the best option for you.

Trey Research, a specialist engineering company, has been purchased by Contoso. Trey has its own AD DS forest connected by a forest trust. Much of the Trey infrastructure is old, so you need to standardize tools and management systems across the two companies.

There are other server workloads on servers running earlier versions of Windows Server. For example, the Toronto location has Dynamic Host Configuration Protocol (DHCP) on a server running Windows Server 2012 R2. You want to migrate as many of these server workloads as possible to Windows Server 2019.

The main tasks for this exercise are to:

1. Study the scenario.
1. Plan how to update domains controllers to Windows Server 2019.
1. Plan how to migrate other server workloads.

### Task 1: Study the scenario

1. Study the lab scenario.
1. Study the exercise scenario.

### Task 2: Plan how to update domain controllers to Windows Server 2019

Answer the following questions based on the scenario:

1. To implement domain controllers running Windows Server 2019, should you upgrade the existing AD DS forest or migrate to a new AD DS forest?
1. What are the highest domain and forest functional levels that you can implement?
1. Which domain controller operating systems can you use to implement the highest possible domain and forest functional levels?
1. What steps do you need to take before adding domain controllers running Windows Server 2019 to an existing AD DS forest?
1. What do you need to consider when removing domain controllers running previous Windows Server versions?

### Task 3: Plan how to migrate other server workloads

Answer the following questions based on the scenario:

1. What steps do you need to perform before running the Windows PowerShell cmdlets in the Windows Server Migration Tools on Windows Server 2019?
1. What steps do you need to perform on a source server running Windows Server 2012 R2 before you can use the Windows PowerShell cmdlets in the Windows Server Migration Tools?
1. Which cmdlet can you use to verify which features can be migrated from a source server?
1. List the high-level steps for using the Windows Server Migration Tools to migrate settings from a source server to a destination server.

## Exercise 2: Planning how to migrate files by using Storage Migration Service

### Scenario

Contoso has file servers running multiple versions of Windows Server. The oldest file server is running Windows Server 2003. There are also a few Linux servers being used for file storage by developers. Some of the Linux servers are using Samba, but others are using Network File System (NFS). A new policy is being implemented that requires all file servers to be migrated to Windows Server 2019.

The main tasks for this exercise are to:

1. Study the scenario.
1. Plan the migration of file servers.
1. Plan how to use Storage Migration Service.

### Task 1: Study the scenario

1. Study the lab scenario.
1. Study the exercise scenario.

### Task 2: Plan the migration of file servers

Answer the following questions based on the scenario:

1. Can you use Storage Migration Service to migrate file shares from Windows Server 2003 to Windows Server 2019?
1. Can you use Storage Migration Service to migrate files on Linux servers?
1. Can you use Storage Migration Service to combine multiple file servers to a single new server?
1. Can you use Storage Migration Service to migrate file shares to a VM in Azure?

### Task 3: Plan how to use Storage Migration Service

Answer the following questions based on the scenario:

1. What software do you need to install to use Storage Migration Service?
1. What firewall configuration do you need to implement to use Storage Migration Service?
1. What accounts and permissions must be configured to use Storage Migration Service?
1. Which tool do you use to create and manage jobs?
1. What is the relationship between volumes in the source server and the destination server?
1. After cutover, which identity information is moved from the source server to the destination server?
1. Which data won't be migrated from the source server to the destination server?
