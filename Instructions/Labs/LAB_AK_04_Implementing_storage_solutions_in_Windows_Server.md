---
lab:
    title: 'Lab: Implementing storage solutions in Windows Server'
    type: 'Answer Key'
    module: 'Module 4: File servers and storage management in Windows Server'
---

# Lab answer key: Implementing storage solutions in Windows Server

> **Note**: Be sure to revert the virtual machines (VMs) between each exercise. Due to most of the VMs being Windows Server 2019 Server Core, the time it takes to revert and restart is faster than attempting to undo changes made to the storage environment in the exercises.

## Exercise 1: Implementing Data Deduplication

### Task 1: Install the Data Deduplication role service

1. On **SEA-ADM1**, select **Start**, and then select **Server Manager**.
2. In Server Manager, select **Manage**, and then select **Add Roles and Features**.
3. In the **Add Roles and Features Wizard**, select **Next** twice.
4. On the **Select destination server** page , in the **Server Pool** pane, select **```SEA-SVR3.Contoso.com```**, and then select **Next**.
5. On the **Select server roles** page, in the **Roles** pane, expand the **File and Storage Services** item, and then expand the **File and iSCSI Services** item, select the **Data Deduplication** item, and then select **Next**.
6. On the **Select features** page, select **Next**, and then in the **Confirm installation selections** page, select **Install**.
7. While the role service is installing, on the taskbar, select the **File Explorer** icon.
8. In **File Explorer**, expand drive **C**.
8. Right-click or access the context menu for the **Labfiles** directory, and then select **Give access to**. In the next context menu, select **Specific people…**.
1. In the **Type a name and then Click Add, or click the arrow to find someone** text box, type **Users** and click **Add**.
1. In the **Network access** window, select **Share**, and when the **Your folder is shared** section opens, select **Done**.
1. 2. In Server Manager, on the **Add Roles and Features Wizard installation succeeded** page, select **Close**.
1. Switch to **SEA-SVR3**.
1. In the **Command Prompt** window, enter **PowerShell**.
1. Note that the prompt changes with the **PS** cursor to let you know you are now in Windows PowerShell.
1. In the **Administrator: Windows PowerShell** window, enter the following commands, selecting Enter after each line.

     ```
     Get-Disk
     ```

     ```
     Initialize-Disk -Number 1
     ```

     ```
     New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter M
     ```

     ```
     Format-Volume -DriveLetter M -FileSystem ReFS
     ```

     ```
     Exit
     ```

17. In the **Command Prompt** window, enter the following command, selecting Enter after each line:

     ```
     Net use x: \\SEA-ADM1\Labfiles
     ```

     ```
     M:
     ```

     ```
     Md Data
     ```

     ```
     copy x:\mod04\createlabfiles.cmd M:
     ```

     ```
     CreateLabFiles.cmd
     ```

     ```
     Cd data
     ```

     ```
     dir
     ```

24. Make note of the free space in **M:\Data**.

### Task 2: Enable and configure Data Deduplication

1. Return to **SEA-ADM1**
2. In the Server Manager console tree, right-click or access the context menu for **File and Storage Services**, and then from the context menu select **Disks**
3. In the **Disks** pane, select **SEA-SVR3**, **Number** column **1**.
3. In the **Volumes** pane, right-click or access the context menu for the **M** volume, and then from the context menu, select **Configure Data Deduplication**.
4. In the **Volume (M:\) Deduplication Settings** wizard, select the Data Deduplication drop-down menu and change the selection to the **General purpose file server** setting.
5. Set **Deduplicate files older than (in days):** to **0**.
6. Select the **Set Deduplication Schedule** button.
7. In the **SEA-SVR3 Deduplication Schedule** window, select **Enable throughput optimization**, and then select **OK**.
8. In the **Volume (M:\) Deduplication Settings Wizard**, select **OK**.

### Task 3: Test Data Deduplication

1. On **SEA-ADM1**, on the taskbar, select **Microsoft Edge**.
2. In Microsoft Edge, select the **Windows Admin Center** (WAC) tab on the **Favorites** bar.
2. In the **Windows Security** pop-up window, in the **User name** text box, enter **Contoso\Administrator**, in the **Password** text box, enter **Pa55w.rd**, and then select **OK**.
3. On the **All connections** page, select **SEA-SVR3**.
4. On the **Specify your credentials** blade, select the **Use another account for this connection** radio button.
4. In the **Username** text box, enter **Contoso\Administrator**, and in the **Password** text box, enter **Pa55w.rd**, select the check box for **Use these credentials for all connections**, and then select **Continue**.
5. In the **SEA-SVR3 Tools** console, scroll to and select the **PowerShell** node.
6. In the **PowerShell** pane, enter **Pa55w.rd** at the **Password** prompt.
7. In the **PowerShell** pane, enter the following command, and then select Enter:

     ```
     Start-DedupJob m: -Type Optimization –Memory 50
     ```

8. Switch to **SEA-SVR3**.
8. In the **Command Prompt** window, enter **Dir**.
9. Observe the **Bytes free** size on property values for the **Data** Directory.
10. Wait for five to ten minutes to allow the deduplication job to run.
1. Repeat step 12.
1. Switch back to **SEA-ADM1**, and to the **WAC SEA-SVR3 PowerShell** pane.
1. To verify the Data Deduplication status, run the following commands, pressing Enter at the end of each line:

     ```
     Get-DedupStatus –Volume M: | fl
     ```

     ```
     Get-DedupVolume –Volume M: |fl
     ```

     ```
     Get-DedupMetadata –Volume M: |fl
     ```

13. In **Server Manager**, select **File and Storage Services**, select disk **1**, and then select Volume **M**.
14. Observe the values for **Deduplication Rate** and **Deduplication Savings**.

> **Note:** When you have finished the exercise, revert the VMs to their initial state.

## Exercise 2: Configuring iSCSI storage

### Task 1: Install iSCSI and configure targets

1. On **SEA-ADM1**, right-click or access the context menu for **Start**, and then select **Windows PowerShell (Admin)**.
2. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

     ```
     Invoke-Command -ComputerName SEA-SVR3 -ScriptBlock {Install-WindowsFeature –Name FS-iSCSITarget-Server –IncludeManagementTools}
     ```

3. Enter the following command, and then select Enter:

     ```
     Enter-PSSession -ComputerName SEA-SVR3 -Credential "Contoso\Administrator"
     ```

4. In the **Windows PowerShell credential request** pop-up window, in the **Password** text box, enter **Pa55w.rd**, and then select **OK**.
5. In the **Administrator: Windows PowerShell** window, enter the following commands, pressing Enter at the end of each line:

   > **Note**: In the following command, drive Y is placeholder text only. The drive letter to use will be returned to you in the results of the second command.

   ```
   Initialize-Disk -Number 2
   New-Partition -DiskNumber 2 -UseMaximumSize -AssignDriveLetter
   Format-Volume -DriveLetter _Y_ -FileSystem ReFS
   ```

6. Repeat step 5 for disk 3, replacing 2 with the number 3.
7. In the **Administrator: Windows PowerShell** window, enter the following commands, pressing Enter at the end of each line:

     ```
     New-NetFirewallRule -DisplayName "iSCSITargetIn" -Profile "Any" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3260
     ```

     ```
     New-NetFirewallRule -DisplayName "iSCSITargetOut" -Profile "Any" -Direction Outbound -Action Allow -Protocol TCP -LocalPort 3260
     ```

8. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:

     ```
     Exit-PSSEssion
     ```

### Task 2: Connect to and configure iSCSI targets

1. On **SEA-ADM1**, select **Start**, and then select **Server Manager**.
2. In Server Manager, in the console tree, select **File and Storage Services**, and then under **Disks** pane, select the **SEA-DC1** server. Note that the server only contains the boot and system volume drive C.
3. In the same pane, select the **SEA-SVR3** server. Note that disks **2**, **3**, and **4** are still offline.
1. Right-click or access the context menu for each disk, and in the context menu, select **Bring online**.
1. In the **Bring Disk Online** window, select **Yes**.
4. In the **Server Manager** window, in **File and Storage Services**, select **iSCSI**, select **Tasks**, and in the context menu, select **New iSCSI Virtual Disk**.
5. In the **New iSCSI Virtual Disk Wizard**, on the **Select iSCSI virtual disk location** page, under the **SEA-SVR3** server, select the **E:** volume, and then select **Next**.
6. In the Specify iSCSI virtual disk name page, in the **Name** text box, enter **iSCSIDisk1**, and then select **Next**.
7. On the **Specify iSCSI virtual disk size** page, in the **Size** text box, enter **5**. Leave all other settings as they are, and then select **Next**.
8. On the **Assign iSCSI target** page, ensure the **New iSCSI target** radio button is selected, and then select **Next**.
9. In the **Specify target name** page, in the **Name** field, enter **iSCSIFarm**, and then select **Next**.
10. In the **Specify access servers** page, select the **Add** button.
11. In the **Select a method to identify the initiator** window, select the **Browse** button.
12. In the **Select Computer** window, in the **Enter the object name to select** text box, enter **SEA-DC1**, select **Check Names**, and then select **OK**.
13. In the **Select a method to identify the initiator** window, select **OK**.
14. On the **Specify access servers** page, select **Next**.
15. On the **Enable Authentication** page, select **Next**.
16. On the **Confirm selections** page, select **Create**.
17. On the **View results** page, select **Close**.
18. Create the second iSCSI virtual disk (F:), by repeating steps 4 through 9 and then step 17, using the following settings:

     - Storage Location: **F:**
     - Name: **iSCSIDisk2**
     - Disk size: **5 GB**, **Dynamically Expanding**
     - iSCSI target: **iSCSIFarm**

19. On **SEA-DC1**, in the Command Prompt window, enter **PowerShell**.
20. In the **Windows PowerShell** window, enter the following commands, selecting **Enter** after each command:

     ```
     Start-Service msiscsi
     ```

     ```
     iscsicpl
     ```

    > **Note**: The **iscsicpl** command will open an **iSCSI Initiator Properties** dialog box.

21. In the **iSCSI Initiator Properties** dialog box, on the **Targets** tab, in the **Target** text box, enter **SEA-SVR3**, and then select **Quick Connect**.
22. In the **Quick Connect** dialog box, note that the **Discovered target name** is **iqn.1991-05.com.microsoft:sea-svr3-iscscifarm-target**, and then select **Done**.
23. In the **iSCSI Initiator Properties** dialog box, select **OK**.
1. Close the **iSCSI Initiator Properties** dialog box.

### Task 3: Verify iSCSI disk presence

1. Switch back to **SEA-ADM1**.
1. In **Server Manager**, select **File and Storage Services**, and then select **Disks**. In the **Tasks** drop-down list box, select **Refresh**.
2. Notice the two new **5 GB** disks on the **SEA-DC1** server that are offline. Notice that the bus entry is **iSCSI**.
3. Switch back to **SEA-DC1**.
1. In the **Windows PowerShell** window, enter the following command, and then select Enter:

     ```
     Get-Disk
     ```

   > **Note**: Both disks are present and healthy, but offline. To use them, you need to initialize and format them on **SEA-DC1**.

4. In the **Windows PowerShell** window, enter the following commands, selecting Enter after each command:

     ```
     Initialize-Disk -Number 1
     ```

     ```
     New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter E
     ```

     ```
     Format-Volume -DriveLetter E -FileSystem ReFS
     ```

5. Repeat step 6 above, using disk number **2** and disk letter **F**.
6. Return to **SEA-ADM1**.
1. In **server Manager**, refresh the page in the **Tasks** drop-down, and note that both the drives are now **Online**.

> **Note:** When you have finished the exercise, revert the VMs to their initial state.

## Exercise 3: Configuring redundant Storage Spaces

### Task 1: Create a storage pool by using the iSCSI disks attached to the server

1. On **SEA-ADM1**, select **Start**, and then select **Server Manager**.
2. In Server Manager, in the **navigation** pane, select **File and Storage Services**, and then select **Disks**.
1. In the **Disks** pane, scroll down, and note that the **SEA-SVR3** disks 1 through 4 are set to **Unknown**.
3. Right-click or access the context menu for each offline disk, select **Bring Online**, and then in the **Bring Disk Online** window, select **Yes**.
4. Verify that all disks are now online, and then in Server Manager, in the **navigation** pane, select **File and Storage Services**, and then select **Storage Pools**.
5. In Server Manager, in the **STORAGE POOLS** area, in the **TASKS** list, select **New Storage Pool**.
6. In the **New Storage Pool Wizard**, on the **Before you begin** page, select **Next**.
7. On the **Specify a storage pool name and subsystem** page, in the **Name** text box, enter **SP1**. In the **Description** text box, enter **Storage Pool 1**, and then select **Next**.
8. On the **Select physical disks for the storage pool** page, select the check box for the top three disks, and then select **Next**.
9. On the **Confirm selections** page, review the settings, and then select **Create**.
10. Select **Close**.

### Task 2: Create a three-way mirrored disk

1. In **Server Manager**, in **Storage Pools**, select **SP1**.
2. In the **VIRTUAL DISKS** area, select **TASKS**, and then select **New Virtual Disk**.
3. In the **Select the storage pool** dialog box, select **SP1**, and then select **OK**.
4. In the **New Virtual Disk Wizard**, on the **Before you begin** page, select **Next**.
5. On the **Specify the virtual disk name** page, in the **Name** text box, enter **Three-Mirror**, and then select **Next**.
6. On the **Specify enclosure resiliency** page, select **Next**.
7. On the **Select the storage layout** page, select **Mirror**, and then select **Next**.
8. On the **Specify the provisioning enter** page, select **Thin**, and then select **Next**.
9. On the **Specify the size of the virtual disk** page, in the **Specify size** text box, enter **25**, and then select **Next**.
10. On the **Confirm selections** page, review the settings, and then select **Create**.
11. On the **View results** page, clear the **Create a volume when this wizard closes** check box, and then select **Close**.
12. In **Server Manager**, in the **navigation** pane, select **Volumes**.
13. In the **VOLUMES** area, select **TASKS**, and then select **New Volume**.
14. In the **New Volume Wizard**, on the **Before you begin** page, select **Next**.
15. On the **Select the server and disk** page, select **SEA-SVR3**, select **Three-Mirror**, and then select **Next**.
16. On the **Specify the size of the volume** page, select **Next**.
17. On the **Assign to a drive letter or folder** page, select **Drive letter**, select **T**, and then select **Next**.
18. On the **Select file system settings** page, in the **File system** drop-down list, select **ReFS**. In the **Volume label** text box, enter **TestData**, and then select **Next**.
19. On the **Confirm selections** page, select **Create**.
20. On the **Completion** page, select **Close**.
21. Close **Server Manager**.

### Task 3: Copy a file to the volume, and verify visibility in File Explorer

1. Switch back to **SEA-SVR3**.
1. In the **Command Prompt** window, enter the following command, and then select Enter:

     ```
     netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
     ```

2. Return to **SEA-ADM1**.
1. In the taskbar, select the **File Explorer** icon.
3. In the **File Explorer** window, in the **Address bar**, enter **\\\\sea-svr3\t$**.
4. Right-click or access the context menu in the empty details pane, and then select **New Folder**. Name the folder **Test data**, and then select **Enter**.
5. Double-click **Test data**, or activate its context menu and select **Open**.
1. Right-click or access the context menu for the empty details pane, select **New**, and then **Text Document**. Name the new document **document1**, and then select **Enter**.

### Task 4: Disconnect the disk and verify file availability

1. On **SEA-ADM1**, in Server Manager, in **File and Storage Services**, select **Storage Pools**, and then select **SP1**.
1. In the **Physical Disks** pane, select the **TASKS** drop-down list, and then select **Add Physical Disk**.
3. In the **Add Physical Disk** dialog box, in the **Allocation** drop-down list, ensure **Automatic** is selected, select the check box corresponding to the disk, and then select **OK**.
4. In the **PHYSICAL DISKS** pane, right-click the top disk in the list, and then select **Remove disk**.
5. In the **Remove Physical Disk** window, select **Yes**.
6. Review the statement in the **Remove Physical Disk** window, and then select **OK**.
7. Return to **File Explorer**.
8. Open **Document1.txt**, add some text, and then save and close the file.

### Task 5: Add a new disk to storage pool

1. In Server Manager, on **SEA-ADM1**, in **File and Storage Services**, select **Storage Pools**.
1. In **Storage Pools**, in the **TASKS** drop-down list, select the **Rescan Storage** item.
2. In the **Rescan Storage** window, select **Yes**.
3. In the **Physical Disks** pane, select the **TASKS** drop-down list, and then select **Add Physical Disk**.
4. In the **Add Physical Disk** window, in the **Allocation** drop-down list, ensure **Automatic** is selected. Select the check box, and then select **OK**.
5. Return to File Explorer.
6. Open **Document1**, add some additional text, and then save and close the file.
7. Close all open windows.
8. Switch back to **SEA-SVR3**.
1. In the **Command Prompt** window, enter the following command, and then select Enter:

     ```
     T:

     Cd "test data"

     dir
     ```

9. Verify that **Document1.txt** is included in the returned results.
10. In the **Command Prompt** window, enter the following command, and then select Enter:

     ```
     .\Document1.txt
     ```

11. When the document opens in **Notepad**, verify that all the text additions you made earlier are present, and then close **Notepad**.

> **Note:** When you have finished the exercise, revert the VMs to their initial state.

## Exercise 4: Implementing Storage Spaces Direct

For Exercise 4, you will need to start the following VMs using the username **Contoso\Administrator**, and the password **Pa55w.rd**:

- WS-011T00A-SEA-DC1
- WS-011T00A-SEA-SVR1
- WS-011T00A-SEA-SVR2
- WS-011T00A-SEA-SVR3
- WS-011T00A-SEA-ADM1

### Task 1: Install the features

1. On **SEA-ADM1**, in Server Manager, in the console tree, select **All Servers**, and verify that **SEA-SVR1**, **SEA-SVR2**, and **SEA-SVR3** have a **Manageability** of **Online – Performance counters not started** before continuing.
2. In Server Manager, in the **navigation** pane, select **File and Storage Services**, and then select **Disks**.
1. In the **Disks** pane, scroll until you find **SEA-SVR3** disks 1 through 4, and note that they are set to **Unknown**.
3. Right-click or access the context menu for each offline disk, select **Bring Online**, and then in the **Bring Disk Online** window, select **Yes**.
4. Verify that all disks are online for **SEA-SVR1** and **SEA-SVR2**.
5. Select **Start**, and in the **Start** menu, select **Windows PowerShell ISE**.
6. When **Windows PowerShell ISE** completes loading, select **File**, select **Open**, and then navigate to **C:\Labfiles\Mod04**.
1. Select **Implement-StorageSpacesDirect.ps1**, and then select **Open**.

    > **Note**: The script is divided into numbered steps. There are eight steps, and each step has a number of commands. Run the commands by highlighting each command and pressing **F8**, one after the other in accordance with the following instructions. Ensure each step finishes, that is, goes from Stop operation (a red square) to a Run selection (green arrow) in the menu bar, before starting the next.

7. Select the line in step 1, that is, highlight the entire line, starting with the first **Invoke-Command**, and then select **F8**.
8. Wait until the installation finishes, and then verify that the output of the command includes four lines (one for each server) with **Success** as **True**.
9. Select the second line in step 1, starting with _second_ **Invoke-Command**, and then select **F8**.

      > **Note**: When you start the second command to restart the servers, you can run the third command to install the console without waiting for the second command's restarts to finish.

10. Select the third line in step 1, starting with **Install**, and then select **F8**.
1. Wait a few minutes while the servers restart and the **Failover Cluster Manager** tool is added to **SEA-ADM1**.  
1. Leave the **Windows PowerShell ISE** console open for the remainder of the exercise.

### Task 2: Create and validate a cluster

1. On **SEA-ADM1**, select the **Windows** key, and in the **Start** menu, select **Server Manager**.
2. In Server Manager, select **Tools**, and then select **Failover Cluster Manager**. (This is to confirm it is installed.) Leave the Server Manager console open.
3. In the **Administrator: Windows PowerShell ISE** window, select the line in step 2 starting with **Test-Cluster**, and then select **F8**.
1. Wait until the test finishes, which takes about 5 minutes.
4. Verify that the output of the command only includes warnings and that the last line is a validation report in HTML format.
5. In the **Administrator: Windows PowerShell ISE** window, select the line in step 3 starting with **New-Cluster**, and then select **F8**.
1. Wait until the installation finishes.
6. Verify that the output of the command only includes warnings, and that the last line has a **Name** column with the value **S2DCluster**.
7. Switch to the **Failover Cluster Manager** window, and in the **Management** pane, select **Connect to Cluster**, enter **```S2DCluster.Contoso.com```**, and then select **OK**.

### Task 3: Enable Storage Spaces Direct

1. In the **Administrator: Windows PowerShell ISE** window, select the line in step 4 starting with **Invoke-Command**, and then select **F8**.
1. Wait until the installation finishes.
2. If a **Confirm** dialog box opens, select **Yes**.
3. There should be no output from the command, and ignore any warning message that opens.
4. In the **Administrator: Windows PowerShell ISE** window, select the line in step 5 starting with **Invoke-Command**, and then select **F8**.
1. Wait until the installation finishes.
5. In the output of the command, verify that the **FriendlyName** attribute has a value of **S2DStoragePool**.
6. In the **Failover Cluster Manager** window, expand **```S2DCluster.Contoso.com```**, expand **Storage**, and then select **Pools**.
7. Verify the existence of **Cluster Pool 1**.
8. In the **Administrator: Windows PowerShell ISE** window, select the line in step 6 starting with **Invoke-Command**, and then select **F8**.
1. Wait until the installation finishes.
9. Verify that in the output of the command is the attribute **FileSystemLabel**, with a value of **CSV**.
10. In the **Failover Cluster Manager** window, select **Disks**.
11. Verify the existence of **Cluster Virtual Disk (CSV)**.

### Task 4: Create a storage pool, a virtual disk, and a share

1. In the **Administrator: Windows PowerShell ISE** window, select the line in step 7 starting with **Invoke-Command**, and then select **F8**.
1. Wait until the installation finishes.
2. Verify that in the output of the command is an attribute **FriendlyName**, with a value of **S2D-SOFS**. This validates that the command was successful.
3. In the **Failover Cluster Manager** window, select **Roles**.
4. Verify the existence of **S2D-SOFS**. This also verifies that the command was successful.
5. In the **Administrator: Windows PowerShell ISE** window, select the three lines in step 8, starting with **Invoke-Command**, and then select **F8**.
1. Wait until the installation finishes.
6. Verify that within the output of the command is an attribute **Path** with a value of **C:\ClusterStorage\CSV\VM01**. This validates that the command was successful.
7. In the **Failover Cluster Manager** window, select **S2D-SOFS**, and then select the **Shares** tab.
8. Verify the existence of **VM01**. This also verifies that the command was successful.

### Task 5: Verify Storage Spaces Direct functionality

1. On  **SEA-ADM1**, on the taskbar, select the **File Explorer** icon.
1. In  **File Explorer**, in the address bar, enter **\\\s2d-sofs\VM01**, and then select Enter.
2. Create a new folder named **VMFolder**, and then open it.
3. Switch to the **Administrator: Windows PowerShell ISE** window.
4. At the Windows PowerShell command prompt, enter the following command, and then select Enter:

     ```
     Stop-Computer -ComputerName SEA-SVR3
     ```

5. Switch to the **Server Manager** window, and then select **All Servers**.
6. In the **Servers** list, select **SEA-SVR3**.
7. Verify that **Manageability** changes to **Target computer not accessible**.
**Note**: You may have to refresh the Server Manager view.
1. Switch back to the **File Explorer** window.
1. Create a new text document in the **VMFolder**.
1. In **Failover Cluster Manager**, select **Disks**, and then select **Cluster Virtual Disk (CSV)**.
1. Verify that for the **Cluster Virtual Disk (CSV)**, the **Health Status** is **Warning**, and **Operational Status** is **Degraded**. (**Operational Status** might also display as **Incomplete**.)
1. On the taskbar, select the **Microsoft Edge** icon.
1. In Microsoft Edge, in the Favorites menu, select the **Windows Admin Center (WAC)** tab.
1. In the **Windows security** window, in the **Username** text box, enter **Contoso\Administrator**, in the **Password** text box, enter **Pa55w.rd**, and then select **OK**.
1. In the **All connections** page, select **+ Add**.
1. In the **Add resources** blade, scroll to the **Windows Server cluster** pane, and in the pane, select **Add**.
1. In the **Add cluster** blade **Cluster name** text box, enter **```S2DCluster.Contoso.com```**, and then select **Add**.

     > **Note**: Initially, the connection under the current user will be denied.

18. In the **Specify your credentials** window, select the **Use another account for this connection** radio button. In the **Username** text box, enter **Contoso\Administrator**, in the **Password** text box, enter **Pa55w.rd**, and then select Enter.
19. Clear the check box for **Also add servers in the cluster** (they are already included), and then select **Add**.
20. After the cluster is added to the **All connections** page, select **```S2DCluster.Contoso.com```**.
21. Verify that when the page loads, the **Dashboard** appears has an alert for **SEA-SVR3** being offline.
22. Start **WS-011T00A-SEA-SVR3**. (While **SEA-SVR3** should start quickly, it may take a few minutes for the alert to be removed.)

> When you have finished the exercise, revert the VMs to their initial state.
