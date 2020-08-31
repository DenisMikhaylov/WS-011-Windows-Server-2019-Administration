---
lab:
    title: 'Lab: Implementing failover clustering'
    type: 'Answer Key'
    module: 'Module 6: High availability in Windows Server'
---

# Lab answer key: Implementing failover clustering

## Exercise 1: Configuring iSCSI storage

### Task 1: Install Failover Clustering

1. On **SEA-ADM1**, select **Start**, right-click or access the context menu for **Windows PowerShell**, and then select **Run as Administrator**. 

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    Install-WindowsFeature –Name Failover-Clustering –IncludeManagementTools
    ```

1. Wait until the installation process is complete and a command prompt appears.

1. On **SEA-ADM1**, repeat step 1 to open a new PowerShell session that you'll use to connect to the **SEA-SVR2** server.

1. In the new **Administrator:Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    $cred=Get-Credential
    ```

1. When prompted, sign in as **Contoso\Administrator** with password **Pa55w.rd**.

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
       $sess = New-PSSession -Credential $cred -ComputerName sea-svr2.contoso.com
    ```

1. After running the previous command, enter the following command, and then select Enter:

    ```powershell
   Enter-PSSession $sess
   ```

1. Verify that **`sea-svr2.contoso.com`** appears at the beginning of the command prompt.

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR2**, enter the following command, and then select Enter:

    ```powershell
   Install-WindowsFeature –Name Failover-Clustering –IncludeManagementTools
   ```

1. On **SEA-ADM1**, repeat step 1 to open a new PowerShell session that you'll use to connect to the **SEA-SVR3** server.

1. To install the Failover Clustering feature on **SEA-SVR3**, in the new **Administrator: Windows PowerShell** window, enter the following command, and then select Enter.

    ```powershell
    $cred=Get-Credential
    ```

1. When prompted, sign in as **Contoso\Administrator** with password **Pa55w.rd**.

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
       $sess = New-PSSession -Credential $cred -ComputerName sea-svr3.contoso.com
    ```

1. After running the previous command, enter the following command, and then select Enter:

    ```powershell
   Enter-PSSession $sess
   ```

1. Verify that **`sea-svr3.contoso.com`** appears at the beginning of the command prompt.

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR3**, enter the following command, and then select Enter:

     ```powershell
     Install-WindowsFeature –Name Failover-Clustering –IncludeManagementTools
     ```

1. On **SEA-ADM1**, in the **Administrator: Windows PowerShell** window for the local server, enter the following command, and then select Enter:

     ```powershell
     Add-WindowsFeature FS-iSCSITarget-Server
     ```

1. Wait until the installation finishes and the command prompt returns.

1. In the PowerShell window for **SEA-SVR2**, enter the following command, and then select Enter:

    ```powershell
    Restart-Computer
    ```

1. In the PowerShell window for **SEA-SVR3**, enter the following command, and then select Enter:

    ```powershell
    Restart-Computer
    ```

1. In the PowerShell window for **SEA-ADM1**, enter the following command, and then select Enter:

    ```powershell
    Restart-Computer
    ```

1. Wait for 3–4 minutes for all three servers to restart.

### Task 2: Configure iSCSI virtual disks

1. Sign in to **SEA-ADM1** as **Contoso\Administrator** with password **Pa55w.rd**.

1. Select **Start**, right-click or access the context menu for **Windows PowerShell**, and then select **Run as Administrator**.

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    New-IscsiVirtualDisk c:\Storage\disk1.VHDX –size 10GB
    ```

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    New-IscsiVirtualDisk c:\Storage\disk2.VHDX –size 10GB
    ```

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    New-IscsiVirtualDisk c:\Storage\disk3.VHDX –size 10GB
    ```

1. To open another **Windows PowerShell** window to connect to **SEA-SVR2**, on **SEA-ADM1**, select **Start**, right-click or access the context menu for **Windows PowerShell**, and then select **Run as Administrator**.

1. In the new **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

    ```powershell
    $cred=Get-Credential
    ```

1. When prompted, sign in as **Contoso\Administrator** with password **Pa55w.rd**.

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

     ```powershell
        $sess = New-PSSession -Credential $cred -ComputerName sea-svr2.contoso.com
     ```

1. After running the previous command, enter the following command, and then select Enter:

     ```powershell
     Enter-PSSession $sess
     ```

1. Verify that **`sea-svr2.contoso.com`** appears at the beginning of the command prompt.

1. To open another **Windows PowerShell** window to connect to **SEA-SVR3**, on **SEA-ADM1**, select **Start**, right-click or access the context menu for **Windows PowerShell**, and then select **Run as Administrator**.

1. In the new **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

     ```powershell
     $cred=Get-Credential
     ```

1. When prompted, sign in as **Contoso\Administrator** with password **Pa55w.rd**.

1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

     ```powershell
        $sess = New-PSSession -Credential $cred -ComputerName sea-svr3.contoso.com
     ```

1. After running the previous command, enter the following command, and then select Enter:

     ```powershell
     Enter-PSSession $sess
     ```

1. Verify that **`sea-svr3.contoso.com`** appears at the beginning of the command prompt.

     > **Note:** You should have three **Windows PowerShell** windows opened. Ensure that you always use the proper PowerShell session window that's connected to the server where you want to run a command.

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR2**, start the Microsoft iSCSI Initiator service by entering the following commands, each followed by selecting Enter:

   ```powershell
   Start-Service msiscsi

   Set-Service msiscsi -startuptype "automatic"
   ```

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR3**, start the Microsoft iSCSI Initiator service by entering the following commands, each followed by selecting Enter:

   ```powershell
   Start-Service msiscsi

   Set-Service msiscsi -startuptype "automatic"
   ```

1. On **SEA-ADM1**, in the **Administrator: Windows PowerShell** window for the local machine, enter:

   ```powershell
   New-IscsiServerTarget iSCSI-MOD6 –InitiatorIds “IQN:iqn.1991-05.com.microsoft:sea-svr2.contoso.com","IQN:iqn.1991-05.com.microsoft:sea-svr3.contoso.com"
   ```

### Results

After completing this exercise, you should have successfully installed the Failover Clustering feature and configured the Internet SCSI (iSCSI) Target Server.

## Exercise 2: Configuring a failover cluster

### Task 1: Connect clients to the iSCSI targets

1. In the PowerShell window for **SEA-ADM1**, enter each of the following commands, each followed by selecting Enter:

    ```powershell
    Add-IscsiVirtualDiskTargetMapping iSCSI-MOD6 c:\Storage\Disk1.VHDX

    Add-IscsiVirtualDiskTargetMapping iSCSI-MOD6 c:\Storage\Disk2.VHDX

    Add-IscsiVirtualDiskTargetMapping iSCSI-MOD6 c:\Storage\Disk3.VHDX
    ```

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR2**, enter the following commands, and after each, select Enter:

    ```powershell
    New-iSCSITargetPortal –TargetPortalAddress SEA-ADM1.contoso.com
    
    Connect-iSCSITarget –NodeAddress iqn.1991-05.com.microsoft:sea-adm1-iscsi-mod6-target
    
    Get-iSCSITarget | fl
    ```
1. Verify that after running the last command, the value for the *IsConnected* variable is True.
1. In the **Administrator: Windows PowerShell** window for **SEA-SVR3**, enter the following commands, and then select Enter after each:

    ```powershell
    New-iSCSITargetPortal –TargetPortalAddress SEA-ADM1.contoso.com

    Connect-iSCSITarget –NodeAddress iqn.1991-05.com.microsoft:sea-adm1-iscsi-mod6-target

    Get-iSCSITarget | fl
    ```

1. Verify that after you run the last command, the value for the *IsConnected* variable is True.

### Task 2: Initialize the disks

1. In the **Administrator: Windows PowerShell** window for **SEA-SVR2**, enter the following command, and then select Enter:

    ```powershell
    Get-Disk
    ```

1. After running this command, ensure that three disks are in the **Offline** operational status. These should be disks with numbers 4, 5, and 6.
1. In the **Administrator: Windows PowerShell** window for **SEA-SVR2**, enter the following commands, selecting Enter after each:

    ```powershell
    Get-Disk | Where OperationalStatus -eq 'Offline' | Initialize-Disk -PartitionStyle MBR

    New-Partition -DiskNumber 4 -Size 5gb -AssignDriveLetter

    New-Partition -DiskNumber 5 -Size 5gb -AssignDriveLetter

    New-Partition -DiskNumber 6 -Size 5gb -AssignDriveLetter

    Format-Volume -DriveLetter E -FileSystem NTFS

    Format-Volume -DriveLetter F -FileSystem NTFS

    Format-Volume -DriveLetter G -FileSystem NTFS
    ```

### Task 3: Validate and create a failover cluster

1. Switch to **SEA-SVR2**, and then sign in as **Contoso\Administrator** with password **Pa55w.rd**.

    > **Note:** You must sign in on **SEA-SVR2** because you can't run cluster commands over remote PowerShell.

1. Enter **PowerShell**, and then select Enter.

1. At the command prompt on **SEA-SVR2**, enter **Test-Cluster SEA-SVR2, SEA-SVR3**, and then select Enter.

1. Wait for a few minutes for the cluster validation test to complete. You can expect a few warning messages to appear, but there should be no errors.

1. At the command prompt on **SEA-SVR2**, enter the following command, and then select Enter:

    ```powershell
    New-Cluster -Name WFC2019 -Node sea-svr2 -StaticAddress 172.16.10.125
    ```

1. You should receive a cluster name as a result. It should display **Name WFC2019**. Verify that there are no errors.

1. At the command prompt on **SEA-SVR2**, enter the following command, and then select Enter:

    ```powershell
    Add-ClusterNode -Name SEA-SVR3
    ```

1. When a command prompt appears, verify that no errors are reported.

### Results

After completing this exercise, you should have installed and configured the Failover Clustering feature.

## Exercise 3: Deploying and configuring a highly available file server

### Task 1: Add the file server application to the failover cluster

1. On **SEA-ADM1**, open **Server Manager**, and then select **Failover Cluster Manager** in the **Tools** menu.
1. In the **Failover Cluster Manager** console, select **Connect to Cluster**.
1. To connect to the cluster that you created in the previous exercise, in the **Select Cluster** window, enter **WFC2019**, and then select **OK**.
1. Expand **`WFC2019.contoso.com`**, select **Roles**, and then notice that no roles display. This is because no cluster roles are configured yet.
1. Select **Nodes**, and then notice that the **SEA-SVR2** and **SEA-SVR3** nodes both display a status of **Up**.
1. Expand **Storage**, and then select **Disks**. Notice that three cluster disks have a status of **Online**.
1. On the **Failover Cluster Manager** page, right-click or access the context menu for **Roles**, and then select **Configure role**.
1. On the **Before You Begin** page, select **Next**.
1. On the **Select Role** page, select **File Server**, and then select **Next**.
1. On the **File Server Type** page, select **File Server for general use**, and then select **Next**.
1. On the **Client Access Point** page, in the **Name** box, enter **FSCluster**.
1. In the **Address** box, enter **172.16.10.130**, and then select **Next**.
1. On the **Select Storage** page, select **Cluster Disk 1** and **Cluster Disk 2**, and then select **Next**.
1. On the **Confirmation** page, select **Next**.
1. On the **Summary** page, select **Finish**.
1. In the **Storage** node, select **Disks**.
1. Verify that three cluster disks are online. **Cluster Disk 1** and **Cluster Disk 2** should be assigned to **FSCluster**.

### Task 2: Add a shared folder to a highly available file server

1. On **SEA-ADM1**, in **Failover Cluster Manager**, select **Roles**, select **FSCluster**, and then in the right pane, select **Add File Share**.
1. On the **Select Profile** page, select **SMB Share - Quick**, and then select **Next**.
1. On the **Share Location** page, select **Next**.
1. On the **Share Name** page, enter **Docs** for the share name, and then select **Next**.
1. On the **Other Settings** page, select **Next**.
1. On the **Permissions** page, select **Next**.
1. On the **Confirmation** page, select **Create**.
1. On the **View results** page, select **Close**.

### Task 3: Configure the failover and failback settings

1. **On SEA-ADM1**, in the **Failover Cluster Manager** console, select **Roles**, select **FSCluster**, and then select **Properties**.
1. Select the **Failover** tab, and then select **Allow failback**.
1. Select **Failback between**, and then enter:
    - **4** in the first text box
    - **5** in the second text box.
1. Select the **General** tab.
1. Under **Preferred owners**, select both **SEA-SVR2** and **SEA-SVR3**.
1. Select the **SEA-SVR3** object, select **Up**, and then select **OK**.

### Results

After completing this exercise, you should have configured a highly available file server.

## Exercise 4: Validating the deployment of the highly available file server

### Task 1: Validate the highly available file server deployment

1. On **SEA-ADM1**, open File Explorer, and then try to access the **\\\FSCluster** location.
1. Verify that you can access the **Docs** folder.
1. Inside the **Docs** folder, right-click or access the context menu in an empty area of the folder, select **New**, and then select **Text Document**.
1. To accept the default name of the document as **New Text Document.txt**, select Enter.
1. In the **Failover Cluster Manager** console, right-click or access the context menu for **FSCluster**, select **Move**, select **Select node**, choose the available node from the **Cluster nodes** list, and then select **OK**.
1. On **SEA-ADM1**, in File Explorer, verify that you can still access the **\\\FSCluster** location.

### Task 2: Validate the failover and quorum configuration for the File Server role

1. On **SEA-ADM1**, in **Failover Cluster Manager**, determine the current owner for the **FSCluster** role. If you select **Roles** and observe the value in the **Owner Node** column, it will be **SEA-SVR2** or **SEA-SVR3**.
1. Select **Nodes**, and then right-click or access the context menu for the node that's the current owner of the **FSCluster** role.
1. Select **More Actions**, and then select **Stop Cluster Service**.
1. Try to access **\\\FSCluster** from **SEA-ADM1** to verify that **FSCluster** has moved to another node and that the **\\\FSCluster** location is still available.
1. Select **Nodes**, and then right-click or access the context menu for the node that has the status of **Down**.
1. Select **More Actions**, and then select **Start Cluster Service**.
1. In the **Failover Cluster Manager** console, right-click or access the context menu for the **`WFC2019.Contoso.com`** cluster, select **More Actions**, and then select **Configure Cluster Quorum Settings**.
1. On the **Before you begin** page, select **Next**.
1. On the **Select Quorum Configuration Options** page, select **Use default quorum configuration**, and then select **Next**.
1. Select **Next**, and then select **Finish**.
1. Browse to the **Disks** node, select the disk marked **witness disk in Quorum**, and then select **Take Offline**.
1. When prompted, select **Yes**.
1. Verify that **FSCluster** is still available by trying to access it from **SEA-ADM1**.
1. Select the disk marked **witness disk in Quorum**, and then select **Bring Online**.
1. Close all open windows.

### Results

After completing this exercise, you should have validated high availability with Failover Clustering.
