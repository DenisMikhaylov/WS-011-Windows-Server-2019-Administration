---
lab:
    title: 'Lab: Implementing failover clustering'
    module: 'Module 6: High availability in Windows Server'
---

# Lab: Implementing failover clustering

## Scenario

As the business of Contoso, Ltd. grows, it's becoming increasingly important that many of the applications and services on its network are always available. Contoso has many services and applications that must be available to internal and external users who work in different time zones around the world. Many of these applications can't be made highly available by using Network Load Balancing (NLB). Therefore, you should use a different technology to make these applications highly available.

As one of the senior network administrators at Contoso, you're responsible for implementing failover clustering on the servers that are running Windows Server 2019 to provide high availability for network services and applications. You're also responsible for planning the failover cluster configuration and deploying applications and services on the failover cluster.

## Objectives

After completing this lab, you'll be able to:

- Configure a failover cluster.
- Deploy and configure a highly available file server on the failover cluster.
- Validate the deployment of the highly available file server.

## Estimated time: **60 minutes**

## Lab setup

Virtual machines: **SEA-DC1**, **SEA-ADM1**, **SEA-SVR2**, and **SEA-SVR3**

User name: **Contoso\Administrator**

Password: **Pa55w.rd**

Sign in only to **SEA-ADM1**. Sign in to other virtual machines only when instructed in lab steps.

## Exercise 1: Configuring iSCSI storage

### Scenario

Contoso has important applications and services that it wants to make highly available. Some of these services can't use NLB, so you have decided to implement failover clustering. You decide to use Internet SCSI (iSCSI) storage for failover clustering. First, you'll configure iSCSI storage to support your failover cluster.

The main tasks for this exercise are to:

- Install Failover Clustering.
- Configure iSCSI virtual disks.

### Task 1: Install Failover Clustering

1. On **SEA-ADM1**, use Windows PowerShell to install the **Failover-Clustering** feature with Management Tools and the **FS-iSCSITarget-Server** feature.
1. Create remote PowerShell sessions for **SEA-SVR2** and **SEA-SVR3** to install the **Failover-Clustering** feature with Management Tools.
1. After Failover Clustering is installed on **SEA-ADM1**, **SEA-SVR2**, and **SEA-SVR3**, restart all three computers.
 
### Task 2: Configure iSCSI virtual disks

1. Create three iSCSI virtual disks on **SEA-ADM1** by using the **New-IscsiVirtualDisk** cmdlet with the following values:

    - Disk1:
        - Storage location: **C:\storage**
        - Disk name: **Disk1**
        - Size: **10 GB**
    - Disk2:
        - Storage location: **C:\storage**
        - Disk name: **Disk2**
        - Size: **10 GB**
    - Disk3:
        - Storage location: **C:\storage**
        - Disk name: **Disk3**
        - Size: **10 GB**

1. Use the **Start-Service** and **Set-Service** cmdlets to start the **msiscsi** service on **SEA-SVR2** and **SEA-SVR3**, configuring the service to start automatically.

1. Create a new iSCSI target on **SEA-ADM1** by using the **New-IscsiServerTarget** cmdlet with the following values:

    - Target name: **ISCSI-MOD6**
    - InitiatorsIds: 
        - **"IQN:iqn.1991-05.com.microsoft:sea-svr2.contoso.com"**
        - **"IQN:iqn.1991-05.com.microsoft:sea-svr3.contoso.com"**

### Results

After completing this exercise, you should have successfully installed the Failover Clustering feature and configured the iSCSI Target Server.

## Exercise 2: Configuring a failover cluster

### Scenario

In this exercise, you'll configure a failover cluster. You'll implement the core components for failover clustering. You'll validate the cluster and then create the failover cluster.

The main tasks for this exercise are to:

1. Connect clients to the iSCSI targets.
1. Initialize the disks.
1. Validate and create a failover cluster.

### Task 1: Connect clients to the iSCSI targets

1. On **SEA-ADM1**, use PowerShell with the **Add-IscsiVirtualDiskTargetMapping** cmdlet to map the disks that you created in the previous exercise to the **ISCSI-MOD6** target.
1. Use a remote PowerShell session to **SEA-SVR2** to connect to the **iSCSI Target Portal** by running the following commands:

    ```powershell
    New-iSCSITargetPortal -TargetPortalAddress SEA-ADM1.contoso.com
        
    Connect-iSCSITarget - NodeAddress iqn.1991-05.com.microsoft:sea-adm1.contoso.com
        
    Get-iSCSITarget | fl
    ```

1. Verify that after you run the **Get-iSCSITarget** command, the value for the *IsConnected* variable is True.
1. Repeat steps 2 and 3 for **SEA-SVR3**.

### Task 2: Initialize the disks
1. On **SEA-SVR2**, use Windows PowerShell and the **Initialize-Disk**, **New-Partition**, and **Format-Volume** cmdlets to configure the three disks with the following settings:

    - PartitionStyle: **MBR**
    - New-Partition Size: **5GB**
    - File System : **NTFS**
    - Assign drive letter automatically

### Task 3: Validate and create a failover cluster

1. Sign in to **SEA-SVR2** locally as **Contoso\Administrator**.
1. Use the **Test-Cluster SEA-SVR2, SEA-SVR3** cmdlet to start the **Validate a Configuration Wizard**.
1. Review the results. No errors should appear, but some warnings are expected.
1. Use the **New-Cluster -Name WFC2019 -Node sea-svr2 -StaticAddress 172.16.10.125** command to create a new cluster.
1. Use the **Add-ClusterNode** cmdlet to add **SEA-SVR3** as a cluster node.

### Results

After completing this exercise, you should have configured disks and created a failover cluster.

## Exercise 3: Deploying and configuring a highly available file server

### Scenario

At Contoso, file services are important services that must be made highly available. After you have created a cluster infrastructure, you decide to configure a highly available file server and then implement settings for failover and failback.

The main tasks for this exercise are to:

1. Add the file server application to the failover cluster.
1. Add a shared folder to a highly available file server.
1. Configure the failover and failback settings.

### Task 1: Add the file server application to the failover cluster

1. On **SEA-ADM1**, open the **Failover Cluster Manager** console.
1. Connect to the **WFC2019** cluster.
1. In the **Nodes** node, check that both of the **SEA-SVR2** and **SEA-SVR3** nodes are running.
1. In the **Storage** node, select **Disks**, and then verify that three cluster disks are online.
1. Add **File Server** as a cluster role, and then select the **File Server for general use** option.
1. Specify the following settings:
    - Client Access Name: **FSCluster**
    - Address: **172.16.0.130**
    - Storage: **Cluster Disk 1, Cluster Disk 2**
1. Close the wizard.

### Task 2: Add a shared folder to a highly available file server

1. On **SEA-ADM1**, in the **Failover Cluster Manager** console, select to add a file share to the **FSCluster** role.
1. Specify the file share profile as **SMB Share - Quick**.
1. Accept the default values on the **Select the server and the path for this share** page.
1. Name the shared folder **Docs**.
1. Accept the default values on the **Configure share settings** and **Specify permissions to control access** pages.
1. At the end of the **New Share** wizard, create the share.

### Task 3: Configure the failover and failback settings

1. On **SEA-ADM1**, in the **Failover Cluster Manager** console, open the properties for the **FSCluster** cluster role.
1. Set failback for **between 4 and 5 hours**.
1. Select both **SEA-SVR2** and **SEA-SVR3** as the **Preferred owners**.
1. Move **SEA-SVR3** to be the first in the preferred owners list.

### Results

After completing this exercise, you should have configured a highly available file server.

## Exercise 4: Validating the deployment of the highly available file server

### Scenario

In implementing a failover cluster, you want to perform failover and failback tests. Additionally, you want to change the witness disk in the quorum.

The main tasks for this exercise are to:

1. Validate the highly available file server deployment.
1. Validate the failover and quorum configuration for the File Server role.

### Task 1: Validate the highly available file server deployment

1. On **SEA-ADM1**, open File Explorer, and then try to access the **\\\FSCluster** location.
1. Verify that you can access the **Docs** folder.
1. Create a test text document inside this folder.
1. On **SEA-ADM1**, in the **Failover Cluster Manager** console, move **FSCluster** to another node.
1. On **SEA-ADM1**, in File Explorer, verify that you can still access the **\\\FSCluster** location.

### Task 2: Validate the failover and quorum configuration for the File Server role

1. On **SEA-ADM1**, in **Failover Cluster Manager**, determine the current owner for the **FSCluster** role.
1. Stop the Cluster service on the node that's the current owner of the **FSCluster** role.
1. Try to access **\\\FSCluster** from File Explorer to verify that **FSCluster** has moved to another node and that the **\\\FSCluster** location is still available.
1. Start the Cluster service on the node on which you stopped it in step 2.
1. Configure cluster quorum for **FSCluster** to use the default quorum configuration.
1. Browse to the **Disks** node, and then take the disk marked **witness disk in Quorum** offline.
1. Verify that **FSCluster** is still available by trying to access it from File Explorer on **SEA-ADM1**.
1. Bring the witness disk online.

### Results

After completing this exercise, you should have validated high availability with Failover Clustering.

