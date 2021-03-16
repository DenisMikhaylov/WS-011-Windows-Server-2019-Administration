---
lab:
    title: 'Lab: Implementing storage solutions in Windows Server'
    module: 'Module 4: File servers and storage management in Windows Server'
---

# Lab: Implementing storage solutions in Windows Server

## Scenario

At Contoso, Ltd., you need to implement the Storage Spaces feature on the Windows Server 2019 servers to simplify storage access and provide redundancy at the storage level. Management wants you to test Data Deduplication to save storage. They also want you to implement Internet Small Computer System Interface (iSCSI) storage to provide a simpler solution for deploying storage in the organization. Additionally, the organization is exploring options for making storage highly available and researching the requirements that it must meet for high availability. You want to test the feasibility of using highly available storage, specifically Storage Spaces Direct.

## Objectives

After completing this lab, you'll be able to:

- Implement Data Deduplication.
- Configure Internet Small Computer System Interface iSCSI storage.
- Configure Storage Spaces.
- Implement Storage Spaces Direct.

## Estimated time: 90 minutes

## Lab setup

**Virtual machines:**

- For Exercises 1-3: **WS-011T00A-SEA-DC1**, **WS-011T00A-SEA-SVR3**, and **WS-011T00A-SEA-ADM1**
- For Exercise 4: **WS-011T00A-SEA-DC1**, **WS-011T00A-SEA-SVR1**, **WS-011T00A-SEA-SVR2**, **WS-011T00A-SEA-SVR3**, and **WS-011T00A-SEA-ADM1**

**Username:** Contoso\Administrator
**Password:** Pa55w.rd

> **Note:** You must revert the virtual machines (VM) between each exercise. Because most of the VMs are Windows Server 2019 Server Core VMs, the time to revert and restart is faster than trying to undo changes made to the storage environment in the exercises.

## Lab exercise 1: Implementing Data Deduplication

### Scenario

You decide to install the Data Deduplication role service by using Server Manager. You determine that drive **M** is heavily used, and you suspect that it contains duplicate files in some folders. You decide to enable and configure the Data Deduplication role to reduce the consumed space on this volume.

The main tasks for this exercise are:

1. Install the Data Deduplication feature on **SEA-SVR3**.
2. Enable and configure Data Deduplication on drive **M** on **SEA-SVR3**.
3. Test Data Deduplication by adding files and observing deduplication.

### Task 1: Install the Data Deduplication role service

1. On **SEA-ADM1**, in Server Manager, add the **Data Deduplication** role to **SEA-SVR3** (under File and Storage Services, and then under File and iSCSI Services).
1. Share the (**SEA-ADM1**) **C:\Labfiles** folder adding the **Users** group with **Read** access.
1. On **SEA-SVR3**, in Windows PowerShell, create a virtual disk on **SEA-SVR3** from disk 1, and label it drive **M**. Use the following commands to complete this:

   ```
   Get-Disk

   Initialize-Disk -Number 1

   New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter M

   Format-Volume -DriveLetter M -FileSystem ReFS

   ```

1. Exit Windows PowerShell and map drive **X** to **\\\\SEA-ADM1\\Labfiles** (NET USE), and then in the drive **X:**, browse to **cd Mod04**, and then get a directory listing.
1. In the **Windows PowerShell command** window, enter **M:**
1. Enter **MD Data**
1. Copy **x:\mod04\createlabfiles.cmd M:**.
1. Enter the **CreateLabFiles.cmd**.
1. Enter **cd data**, then enter **dir**.
1. Notice that **M:\Data** has free space.  Make note of the amount in bytes.

### Task 2: Enable and configure Data Deduplication

1. Return to **SEA-ADM1**.
1. In Server Manager, select **File and Storage Services**, and then on **SEA-SVR3**, select **Disks**.
2. Select the **1** disk, and then select the **M** volume.
3. Enable Data Deduplication, and then select the **General purpose file server** setting.
4. Configure the following settings:

   - Deduplicate files older than (in days): **0**
   - Enable throughput optimization: Selected

### Task 3: Test Data Deduplication

1. On **SEA-ADM1**, open **WAC**.
2. Connect to **SEA-SVR3**, and then open the **PowerShell** node.
3. Execute the following command to start Data Deduplication process, and then select Enter:

   ```powershell
   Start-DedupJob m: -Type Optimization –Memory 50
   ```

4. Switch to **SEA-SVR3**. In the Command Prompt window, enter **Dir**. Observe the Bytes free size on property values for the Data Directory.
5. Wait for 5 to 10 minutes to allow the deduplication job to run.
6. Switch back to the Windows PowerShell window on **SEA-ADM1**.
7. To verify the Data Deduplication status, run the following commands, selecting Enter at the end of each line:

   ```powershell
   Get-DedupStatus –Volume M: | fl

   Get-DedupVolume –Volume M: |fl

   Get-DedupMetadata –Volume M: |fl

   ```

8. In Server Manager, select **File and Storage Services**, select Disk **1**, and then select Volume **M** (You might have to refresh).
9. Observe the values for **Deduplication Rate** and **Deduplication Savings**.
10. Close all open windows except **Server Manager**.

> When you have finished the exercise, revert the VMs to their initial state.

## Lab exercise 2: Configuring iSCSI storage

### Scenario

Executives at Contoso are exploring the option of using iSCSI to decrease the cost and complexity of configuring centralized storage. To test this, you must install and configure the iSCSI targets, and configure the iSCSI initiators to provide access to the targets.

The main tasks for this exercise are:

1. Install iSCSI and configure targets on **SEA-SVR3**.
2. Connect to and configure iSCSI targets from **SEA-DC1** (initiator).
3. Verify iSCSI disk presence by copying and moving files.

### Task 1: Install iSCSI and configure targets

1. On **SEA-ADM1**, open a Windows PowerShell window.
2. Enter the following command, and then select Enter:

   ```powershell
   Invoke-Command -ComputerName SEA-SVR3 -ScriptBlock {Install-WindowsFeature –Name FS-iSCSITarget-Server –IncludeManagementTools}
   ```
3. On **SEA-ADM1**, open a remote Windows PowerShell session to **SEA-SVR3** as **Contoso\Administrator**.

4. Use Windows PowerShell to initialize, create, and format a volume as Resilient File System (ReFS) on the two offline disks (disks 2 and 3) on **SEA-SVR3** (where X refers to the drive number). Use the following three commands, selecting Enter after each line:

   ```powershell
   Initialize-Disk -Number <X>
   New-Partition -DiskNumber <X> -UseMaximumSize -AssignDriveLetter
   ```

   Note the drive letter it assigns because you'll be using it in the next command.

   ```powershell
   Format-Volume -DriveLetter <X> -FileSystem ReFS
   ```

5. Use Windows PowerShell to create an inbound and outbound Firewall exception for port 3260. Use the following commands, selecting Enter at the end of each line:

    ```powershell
    New-NetFirewallRule -DisplayName "iSCSITargetIn" -Profile "Any" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3260
    ```

   ```powershell
   New-NetFirewallRule -DisplayName "iSCSITargetOut" -Profile "Any" -Direction Outbound -Action Allow -Protocol TCP -LocalPort 3260

   ```

> **Note:** Word wrap is used to display the previous command. Don't use word wrap when entering the command in Windows PowerShell.

6. Close the remote session but keep Windows PowerShell open.

### Task 2: Connect to and configure iSCSI targets

1. On **SEA-ADM1**, in the **Server Manager** window, in **File and Storage Services**, under **Disks**, select the **SEA-DC1** server. Note that it only contains the boot and system volume drive C.
2. In Server Manager, in **File and Storage Services**, under **iSCSI**, select the **SEA-SVR3** server.
3. Create a new iSCSI virtual disk with the following settings:

   - Storage Location: **E:**
   - Name: **iSCSIDisk1**
   - Disk size: **5 GB**, **Dynamically Expanding**
   - iSCSI target: **New**
   - Target name: **iSCSIFarm**
   - Access servers: **SEA-DC1** (Browse and check names)

4. Create a second iSCSI virtual disk with the following settings:

   - Storage Location: **F:**
   - Name: **iSCSIDisk2**
   - Disk size: **5 GB**, **Dynamically Expanding**
   - iSCSI target: **iSCSIFarm**

5. On **SEA-DC1**, open Windows PowerShell, enter the following commands, selecting Enter at the end of each line:

   ```powershell
   Start-Service msiscsi

   iscsicpl
   ```

> **Note:** The **iscsicpl** command will open an **iSCSI Initiator Properties** dialog box.

6. Connect to the following iSCSI target:

   - Name: **SEA-SVR3**
   - Target name: **iqn.1991-05.com.microsoft:SEA-SVR3-fileserver-target**

### Task 3: Verify iSCSI disk presence

1. In Server Manager, on **SEA-ADM1**, in the **tree** pane, select **File and Storage Services**, and then select **Disks**.
2. Notice the two new 5-gigabyte (GB) disks on the **SEA-DC1** server that are offline. Notice that the bus entry is **iSCSI**. (If you're in the **File and Storage Services** section of **Server Manager**, you might need to select the refresh button to open the two new disks.)

> **Note:** When you have finished the exercise, revert the VMs to their initial state.

## Lab exercise 3: Configuring redundant Storage Spaces

### Scenario

To meet some requirements for high availability, you decided to evaluate redundancy options in Storage Spaces. Additionally, you want to test the provisioning of new disks to the storage pool.

The main tasks for this exercise are:

1. Create a storage pool by using the iSCSI disks attached to **SEA-SVR3**.
2. Create a three-way mirrored disk on **SEA-SVR3**.
3. Copy a file to the volume on the three-way mirror, and verify it's present in File Explorer.
4. Disconnect the disk and verify file availability.
5. Add a new disk to storage pool.

> **Note:** In **Windows Server 2019**, you can't disconnect a disk in a storage pool. You can only remove it. You also can't remove a disk from a three-way mirror without adding a new disk first.

### Task 1: Create a storage pool by using the iSCSI disks attached to the server

1. On **SEA-ADM1**, open Server Manager.
2. In Server Manager, in **File and Storage Services**, select **Disks**.
1. Set the disks 1-4 for **SEA-SVR3** to **Online**.
3. In Server Manager, on **SEA-SVR3**, create a new storage pool named **SP1**.
1. Use three of the four available disks to make up the pool.

### Task 2: Create a three-way mirrored disk

1. In Server Manager, in **Storage Pools**, in **SP1**, create a new virtual disk named **Three-Mirror** that uses a mirror storage layout, and thin provisioning. Use **25 GB** for the size.
2. Create a new volume from **Three**-**Mirror** named **TestData**.
1. Format it as ReFS, and assign it drive letter **T**.
3. Close Server Manager.

### Task 3: Copy a file to the volume, and verify it's present in File Explorer

1. Switch to **SEA-SVR3**.
2. In Windows PowerShell, enter the following command, and then select Enter:

   ```
   netsh advfirewall firewall set rule group=&quot;File and Printer Sharing&quot; new enable=Yes
   ```

3. Switch back to **SEA-ADM1**. In the **File Explorer** window, in the Address bar, enter **\\sea-svr3\t$.**
4. Create a new folder named **Test Data**, and then create a new document named **Document1.txt** in that folder.

### Task 4: Disconnect the disk and verify file availability

1. In Server Manager, on **SEA-ADM1**, add a new physical disk to storage pool **SP1**. Ensure the disk uses automatic allocation.
2. Remove the top disk in the **PHYSICAL DISKS** pane from the storage pool.
3. Return to **Document1.txt**, add some text, and then save it.

### Task 5: Add a new disk to the storage pool

1. In Server Manager, re-scan the **SP1** storage pool.
2. Add the disk you removed earlier, ensuring it's allocated automatically.
3. Open **Document1.txt**, add some more text, and then save it.
4. Switch back to **SEA-SVR3**.
1. On drive **T**, in the new **Test Data** folder, open **Document1.txt**.
5. Close all open windows.

> When you have finished the exercise, revert the VMs to their initial state.

## Lab exercise 4: Implementing Storage Spaces Direct

### Scenario

You want to test whether using local storage as highly available storage is a viable solution for your organization. Previously, your organization has only used storage area networks (SANs) for storing VMs. The new features in Windows Server 2019 make it possible to use only local storage, so you want to implement Storage Spaces Direct as a test implementation.

The main tasks for this exercise are:

1. Install the Storage Spaces Direct Failover Clustering features.
2. Create and validate the failover cluster.
3. Enable Storage Spaces Direct.
4. Create the storage pool, a virtual disk, and a share.
5. Verify that Storage Spaces Direct functions properly.

### Task 1: Install the Storage Spaces Direct Failover Clustering features

1. On **SEA-ADM1**, open Server Manager.
2. Ensure all servers refer to **Manageability** of **Online-Performance counters not started**.
3. In **Server Manager**, in the navigation pane, select **File and Storage Services**, and then select **Disks**.
4. In the **Disks** pane, scroll until you find **SEA-SVR3*, disks 1 through 4, and note that they are set to **Unknown**.
5. Right-click or access the context menu for each offline disk, select **Bring Online**, and then in the **Bring Disk Online** window, select **Yes**.
6. Verify that all disks are online for **SEA-SVR1** and **SEA-SVR2**.
7. Open Windows PowerShell ISE and load the **C:\Labfiles\Mod04\Implement-StorageSpacesDirect.ps1** script.

   >**Note:** This script is divided into numbered steps. There are eight steps, and each step has a number of commands. Run the commands by highlighting each and selecting **F8**, one after the other in accordance with the following instructions. Ensure each step finishes, that is, goes from Stop operation (a red square) to Run selection (a green arrow) in the menu bar, before starting the next step.

8. Run the commands in Step 1. This command installs the Failover Clustering role service on **SEA-SVR1**, **SEA-SVR2** and **SEA-SVR3**. The second command restarts the three servers, which is required to complete the install, and the third command installs the Failover Cluster Manager console on **SEA-ADM1**.

   > **Note:** When you start the second command to restart the servers, you can run the third command to install the console without waiting for the second command's restarts to finish.

### Task 2: Create and validate a cluster

1. On **SEA-ADM1**, start the **Failover Cluster Manager** tool.
2. In **Windows PowerShell ISE**, run the step 2 command, which will take approximately 5 minutes to finish.
3. Ensure the output only includes nothing greater than warnings.
4. In **Windows PowerShell ISE**, run the step 3 command.
5. When the command completes, return to **Failover Cluster Manager**, and add the cluster named **```S2DCluster.Contoso.com```**.

### Task 3: Enable Storage Spaces Direct

1. In **Windows PowerShell ISE**, run the step 4 command, which will take approximately 5 minutes to finish.
2. Run the step 5 command, which creates the **S2DStoragePool** storage pool.
3. Return to the **Failover Cluster Manager**, and then observe the **Cluster Pool 1** object in **Pools**.
4. Return to **Windows PowerShell ISE**, and run the step 6 command, which creates the **CSV** file system.
5. Return to the **Failover Cluster Manager**, and then observe the **Cluster Virtual Disk (CSV)** object in **Disks**.

### Task 4: Create a storage pool, a virtual disk, and a share

1. In **Windows PowerShell ISE**, run the step 7 command, which creates the **S2D-SOFS** service role.
2. Return to the **Failover Cluster Manager**, and then observe the **S2D-SOFS** object in **Roles**.
3. Return to **Windows PowerShell ISE**, and run all three commands in Step 8 simultaneously to create the **VM01** share. To run them simultaneously, highlight all three and then select **F8**.
4. Return to the **Failover Cluster Manager**, and then observe the **VM01** object in **Shares**.

### Task 5: Verify Storage Spaces Direct functionality

1. On **SEA-ADM1**, in File Explorer, open **\\s2d-sofs\VM01** and create a folder named **VMFolder**.
2. In **Windows PowerShell ISE**, enter the following command, and then select Enter:

    ```Stop-Computer -ComputerName SEA-SVR3```

3. Return to Server Manager and confirm **SEA-SVR3** is included in the list of **All Servers**.
4. Return to the **Failover Cluster Manager**, and then observe the **Cluster Virtual Disk (CSV)** information in the **Disks** node. (Notice the **Health Status** is set to **Warning**, and the **Operational Status** is **Degraded**.)
5. On **SEA-ADM1**, open the **Windows Admin Console (WAC)**.
6. If required, sign in as **Contoso\Administrator** with a password of **Pa55w.rd**.
7. On the **All connections** page, select **+ Add**.
8. Scroll to find and select **Windows Server Cluster**, and then enter **```S2DCluster.Contoso.com```** as the cluster name.
9. Notice that the pass-through credentials will be denied; however, you can manually add the same account for the connection. Reenter the sign-in information **Contoso\Administrator** with a password of **Pa55w.rd** in the other credentials area.
1. Notice that the scroll to select completion feature won't work, so simply select Enter.
10. Don't add the other servers in the cluster as they are already registered in **WAC**.
11. Note the cluster has a critical error because **SEA-SVR3** is offline. Start **SEA-SVR3**.
12. After a few minutes, verify that the alert clears.
13. Close all windows and revert the VMs.

### Results

After completing this lab, you will have:

- Tested the implementation of Data Deduplication.
- Installed and configured iSCSI storage.
- Configured redundant Storage Spaces.
- Tested the implementation of Storage Spaces Direct.
