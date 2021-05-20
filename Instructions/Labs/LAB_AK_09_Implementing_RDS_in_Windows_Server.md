---
lab:
    title: 'Lab: Implementing RDS in Windows Server'
    type: 'Answer Key'
    module: 'Module 9: RDS in Windows Server'
---

# Lab answer key: Implementing RDS in Windows Server

### Exercise 1: Implementing RDS

#### Task 1: Install RDS

##### Install RDS using Server Manager

1. On **SEA-RDS1**, select the **Start** button, and then select the **Server Manager** tile.
1. In **Server Manager**, select **Manage**, and then select **Add Roles and Features**.
1. In the **Add Roles and Features Wizard**, on the **Before you begin** page, select **Next**.
1. On the **Select installation type** page, select **Remote Desktop Services installation**, and then select **Next**.
1. On the **Select deployment type** page, verify that **Standard deployment** is selected, and then select **Next**.

    > **NOTE**: Even though, we could have selected the **Quick Start** deployment option and have all three required Remote Desktop Services (RDS) role services installed on **SEA-RDS1**, you selected the **Standard deployment** option to practice selecting different servers for the RDS role services. Furthermore, the **Quick Start** deployment option will create a collection named **QuickSessionCollection** and publish the following  RemoteApp Programs: **Calculator**, **Paint**, and **WordPad**.

1. On the **Select deployment scenario** page, select **Session-based desktop deployment**, and then select **Next**.
1. On the **Review role services** page, review the description of the role services, and then select **Next**.
1. On the **Specify RD Connection Broker server** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**. Add the computer to the **Selected** section by selecting the Right arrow, and then select **Next**.
1. On the **Specify RD Web Access server** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**. Add the computer to the **Selected** section by selecting the Right arrow, and then select **Next**.
1. On the **Specify RD Session Host servers** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**. Add the computers to the **Selected** section by selecting the Right arrow, and then select **Next**.
1. On the **Confirm selections** page, select **Cancel**

##### Install RDS using Windows PowerShell

> **NOTE**: We will now do the actual installation of RDS using Windows PowerShell. The previous steps were included to demonstrate how to install RDS using Server Manager.

1. Switch to **SEA-DC1**.
1. In the **Administrator: C:\Windows\system32\cmd.exe** command prompt window, enter the following command, and then select Enter:
    `powershell`
1. In the **Administrator: C:\Windows\system32\cmd.exe - powershell** window, enter the following command, and then select Enter:
    `$SVR="SEA-RDS1.contoso.com"`
1. In the **Administrator: C:\Windows\system32\cmd.exe - powershell** window, enter the following command, and then select Enter:
    `New-RDSessionDeployment -ConnectionBroker $SVR -WebAccessServer $SVR -SessionHost $SVR`
1. Wait for the installation to complete, which will take approximately 5 minutes, and then wait as **SEA-RDS1** restarts automatically.
1. Switch to **SEA-RDS1** and sign in as **Contoso\Administrator** with the password **Pa55w.rd**
1. Select the **Start** icon, and then select the **Server Manager** tile. 
1. Wait for **Server Manager** to refresh.
1. In **Server Manager**, in the navigation pane, select **Remote Desktop Services**. You might need to select **Remote Desktop Services** twice.

#### Task 2: Create a Session Collection

##### Create and configure a Session Collection using Server Manager

> **NOTE**: RDS in Windows Server supports two types of Session Collections on a single RD Session Host: an RD Session Collection, or a RemoteApp Session Collection. You cannot run both session collection types on the same RD Session Host by default. Therefore, when you're doing this exercise, you will first create an RD Session Host collection and verify that it works and then create a RemoteApp Session collection and verify that as well.

1. On **SEA-RDS1**, in **Server Manager**, on the **Remote Desktop Service Overview** page, select **Collections**.
2. In the details pane under **COLLECTIONS**, select **TASKS**, and then select **Create Session Collection**. You might need to scroll to the right to find this option.
3. In the **Create Collection Wizard**, on the **Before you begin** page,  select **Next**.
4. On the **Name the collection** page, in the **Name** field, enter **IT**, and then select **Next**.
5. On the **Specify RD Session Host servers** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**. Add the computers to the **Selected** section by selecting the Right arrow, and then select **Next**.
6. On the **Specify user groups** page, select **Remove** to remove **CONTOSO\Domain Users**, and then select **Add**.
1. In **the Enter the object names to select** field, enter **it**.
1. Select **Check Names**, and then select **OK**.
1. Verify that **CONTOSO\IT** is listed under **User Groups**, and then select **Next**.
1. On the **Specify user profile disks** page, clear the **Enable user profile disks** check box, and then select **Next**.
1. On the **Confirm selections** page, select **Cancel**, and when prompted, select **Yes**.
1. Minimize **Server Manager**.

##### Create and configure a session collection using Windows PowerShell

> **NOTE**: We will now create and configure the session collection using Windows PowerShell. The previous steps were included to demonstrate how to create a session collection using Server Manager.

1. ON **SEA-RDS1**, right-click or access the context menu for the **Start** button, and then select **Windows PowerShell (Admin)**.
2. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `New-RDSessionCollection –CollectionName IT –SessionHost SEA-RDS1.Contoso.com –CollectionDescription “This Collection is for the IT department in Contoso” –ConnectionBroker SEA-RDS1.Contoso.com`
3. Wait for the command to complete, which will take approximately 1 minute.
4. Maximize **Server Manager**, and then select **Overview**.
1. Refresh **Server Manager** by selecting the F5 key.
1. In **Server Manager**, in the navigation pane, select **Collections**, and then verify that a collection named **IT** is listed in the details pane.

#### Task 3: Configure the Session Collection properties

##### Configure device redirection settings

1. On SEA-RDS1, in the navigation pane, select the **IT** collection.
1. Next to ****PROPERTIES**, select **TASKS**., and then select **Edit Properties**.
1. On the **Session Collection** page, select the various settings and notice how the collection is configured.
1. Select **Client Settings**, and verify that both **Audio and video playback** and **Audio recording** is enabled.
1. Select **User Profile Disks**, and verify that **User Profiles Disks** is not enabled.
1. In the **IT Properties** dialog box, select **Cancel**.
1. Minimize **Server Manager**.
1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `Get-RDSessionCollectionConfiguration –CollectionName IT –Client | Format-List`
1. Examine the output and notice that next to **ClientDeviceRedirectionOptions**, the following entries are listed:
    - **AudioVideoPlayBack**
    - **AudioRecording**
    - **PlugAndPlayDevice**
    - **SmartCard**
    - **Clipboard**
    - **LPTPort**
    - **Drive**
1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `Set-RDSessionCollectionConfiguration –CollectionName IT –ClientDeviceRedirectionOptions PlugAndPlayDevice, SmartCard,Clipboard,LPTPort,Drive`
1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `Get-RDSessionCollectionConfiguration –CollectionName IT –Client | Format-List`
1. Examine the output, and notice that next to **ClientDeviceRedirectionOptions** only the following entries are listed now:
    - **PlugAndPlayDevice**
    - **SmartCard**
    - **Clipboard**
    - **LPTPort**
    - **Drive**

##### Configure User Profile Disks for IT collection

1. Switch to **SEA-DC1**.
1. In the **Administrator: C:\Windows\system32\cmd.exe - powershell** window, enter the following commands, one line at a time, and then select Enter:

    `New-Item C:\RDSUserProfiles -itemtype directory`
    `New-SMBShare –Name “RDSUserProfiles” –Path “C:\RDSUserProfiles” –FullAccess "Contoso\SEA-RDS1$", "Contoso\administrator"`
    `$acl = Get-Acl C:\RDSUserProfiles`
    `$AccessRule = New-Object System.Security.AccessControl.FileSystemAccessRule("Contoso\SEA-RDS1$","FullControl","Allow")`
    `$acl.SetAccessRule($AccessRule)`
    `$acl | Set-Acl C:\RDSUserProfiles`

1. Verify that each command executes successfully.
1. Switch to SEA-RDS1.
1. In the navigation pane, select the **IT** collection.
1. Next to ****PROPERTIES**, select **TASKS**, and then select **Edit Properties**.
1. On the **Session Collection** page, select **User Profile Disks**, and then select **Enable user profile disks**.
1. In the **Location** field, enter **\\\SEA-DC1\RDSUserProfiles**. In the **Maximum size (in GB)**, enter **10**, and then select **OK**.

#### Task 4: Connect to the Session Collection from RD Web portal

1. On **SEA-CL1**, on the taskbar, select the **Microsoft Edge** icon.
2. In **Microsoft Edge**, in the address bar, enter **```https://SEA-RDS1.Contoso.com/rdweb```**.
3. On the **This site is not secure** page, select **Details**, and then select **Go on to the webpage**.

> **NOTE**: This page opens because RD Web is using a self-signed certificate that is not trusted by the client. In a real production deployment, you would use trusted certificates.

4. On the **RD Web Access** page, in the **Domain\user name** field, enter **contoso\jane**. In the **Password** field, enter **Pa55w.rd**, and then select **Sign in**.
5. If prompted by **Microsoft Edge** to save the password, select **Never**.
6. On the **RD Web Access** page, under **Current folder: /**, select **IT**, and when prompted, select **Open**.
7. In the **Remote Desktop Connection** dialog box, select **Connect**.

> **NOTE**: This prompts the **Unknown publisher** pop up window because certificates for RDS have not yet been configured.

8. In the **Windows Security** dialog box, in the **Password** field, enter **Pa55w.rd**, then wait for the connection to complete.
9. In **SEA-RDS1**, right-click or access the context menu for **Start**, select **Shut down or sign out**. and then select **sign-out**.
10. Back on the **RD Web Access** page, select **Sign out**.
11. Close **Microsoft Edge**.

##### Verify User Profile Disk creation

1. Switch to **SEA-DC1**, and in the **Administrator: C:\Windows\system32\cmd.exe - powershell** window, enter the following command, and then select Enter:
    `cd\`
2. Enter the following command, and then select Enter:
    `cd RDSUserProfiles`
3. Enter the following command, and then select Enter:
    `dir`
4. Examine the contents of the **RDSUserProfiles** folder. Verify that there is a **.vhdx** file with an SID (a long string that starts with **S-1-5-21**) in its name.

### Exercise 2: Configuring RemoteApp collection settings

#### Task 1: Create and configure a RemoteApp collection using Server Manager

1. Switch to **SEA-RDS1**.
1. Maximize **Server Manager**, and in the details pane. next to **REMOTEAPP PROGRAMS**, select **TASKS**, then select **Publish RemoteApp Programs**.
1. On the **Select RemoteApp programs** page, scroll down in the list under **The RemoteApp programs are populated from ```SEA-RDS1.CONTOSO.COM```**. Select **WordPad** and select **Next**.
1. On the **Confirmation** page, select **Publish**, and wait for the RemoteApp to be published.
1. On the **Completion** page, verify that **WordPad** is listed in the details pane under **RemoteApp Program**, and then select **Close**.

#### Task 2: Create and configure a RemoteApp program using Windows PowerShell

1. ON **SEA-RDS1**, right-click or access the context menu for **Start**, and then select **Windows PowerShell (Admin)**.
2. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `New-RDRemoteApp -Alias Paint -DisplayName Paint -FilePath "C:\Windows\system32\mspaint.exe" -ShowInWebAccess 1 -collectionname IT -ConnectionBroker SEA-RDS1.Contoso.com`
3. When the command has completed, review the information about the published app, and then minimize the WindowsPowerShell window.
4. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter:
    `Get-RDRemoteApp -CollectionName IT`
4. Examine the output of the command. Notice that you will get a list of all published RemoteApp programs.
5. Maximize **Server Manager**, and then select **Overview**.
1. Refresh **Server Manager** by selecting F5.
1. In **Server Manager**, in the navigation pane, select the **IT** collection and verify that **Paint** is listed in the details pane under **REMOTEAPP PROGRAMS**.

#### Task 3: Run RemoteApp from RD Web portal

1. On **SEA-CL1**, on the taskbar, select the **Microsoft Edge** icon. In **Microsoft Edge**, in the address bar, enter **```https://SEA-RDS1.Contoso.com/rdweb```**, and then select Enter.
2. On the **This site is not secure page**, select **Details**, and then select **Go on to the webpage**.

> **NOTE**: This page opens because RD Web is using a self-signed certificate that is not trusted by the client. In a real production deployment, you would use trusted certificates.

3. On the **RD Web Access** page, in the **Domain\user name** field, enter **contoso\jane**. In the **Password** field, enter **Pa55w.rd**, and then select **Sign in**. If prompted by **Microsoft Edge** to save the password, select **Never**.
4. On the **RD Web Access** page, under **Current folder: /**, select **Paint**, and when prompted, select **Open**.
5. In the **Remote Desktop Connection** dialog box, select **Connect**.

> **NOTE**: The **Unknown publisher** pop-up window displays because you have not yet configured certificates for RDS.

6. In the **Windows Security** dialog box, in the **Password** field, enter **Pa55w.rd**.
7. Wait for the RemoteApp **Paint** program to start, and then test its functionality.
8. In **Paint**, select **File**, and then select **Exit** to close the application.
9. Back on the **RD Web Access** page, select **Sign out**.
10. Close Microsoft Edge.

### Exercise 3: Configure a virtual desktop template

#### Task 1: Verify the operating system (OS) version

1. On **SEA-CL1**, sign in as **.\Admin** with the password **Pa55w.rd**.
2. Select the Start button, select the **Settings** gear icon. In the **Windows Settings** screen select **System**, and then select **About**.
3. In the **About** screen, verify the following information:
    - The Windows operating system edition is Windows 10 Enterprise
    - The System type is 64-bit OS

4. Close the **Settings** app.

#### Task 2: Disable unnecessary services

1. On **SEA-CL1**, select the **Start** button, and enter **services**, and then select **Services**.
2. In the **Services** window, right-click or access the context menu for **Background Intelligent Transfer Service**.
3. In the **Background Intelligent Transfer Service Properties (Local Computer)** dialog box, on the **General** tab, select **Stop**.
4. In the **Startup type** box, select **Disabled**, and then select **OK**.
5. In the **Services** window, right-click or access the context menu for **Diagnostic Policy Service**.
6. In the **Diagnostic Policy Service Properties (Local Computer)** dialog box, on the **General tab**, select **Stop**.
7. In the **Startup type** box, select **Disabled**, and then select **OK**.
8. In the **Services** window, right-click or access the context menu for **Shell Hardware Detection**.
9. In the **Shell Hardware Detection Properties (Local Computer)** dialog box, on the **General** tab, select **Stop**.
10. In the **Startup type** box, select **Disabled**, and then select **OK**.
11. In the **Services** window, right-click or access the context menu for **Volume Shadow Copy**.
12. In the **Volume Shadow Copy Properties (Local Computer** dialog box, on the **General** tab, select **Stop**.
13. In the **Startup type** box, select **Disabled**, and then select **OK**.
14. In the **Services window**, right-click or access the context menu for **Windows Search**.
15. In the **Windows Search Properties (Local Computer)** dialog box, on the **General** tab, select **Stop**.
16. In the **Startup type** box, select **Disabled**, and then select **OK**.
17. In the **Services** window, select **File**, and then select **Exit**.

#### Task 3: Disable unnecessary scheduled tasks

1. On **SEA-CL1**, select the **Start** button, enter **sch**, and then select **Task Scheduler**.
2. In the **Task Scheduler** window, expand **Task Scheduler Library**, expand **Microsoft**, expand **Windows**, and then select **Defrag**.
3. Right-click or access the context menu for **ScheduledDefrag**, and then select **Disable**.
4. In the **Task Scheduler** window, select **File**, and then select **Exit**.

#### Task 4: Prepare the virtual desktop template by using Sysprep

1. On **SEA-CL1**, open **File Explorer**. Browse to **C:\Windows\System32\Sysprep**, right-click or access the context menu for **sysprep.exe**, and then select **Open**.
2. In the **System Preparation tool 3.14** dialog box, in the **System Cleanup Action** box, select **Enter System Out-of-Box Experience (OOBE)**.
3. Select the **Generalize** check box.
4. In the **Shutdown Options** box, select **Shutdown**, and then select **OK**.
5. Wait while the System Preparation Tool (Sysprep) completes and shuts down the VM.

Results: After completing this exercise, you will have prepared a Hyper-V VM to be a virtual desktop template.
