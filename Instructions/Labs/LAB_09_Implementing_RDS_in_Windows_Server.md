---
lab:
    title: 'Lab: Implementing RDS in Windows Server'
    module: 'Module 9: RDS in Windows Server'
---

# Lab: Implementing RDS in Windows Server

## Scenario

You have been asked to configure a basic Remote Desktop Services (RDS) environment as the starting point for the new infrastructure that will host the sales application. You would like to deploy RDS services, perform initial configuration, and demonstrate to the delivery team how to connect to an RDS deployment.

You are evaluating whether to use user profile disks for storing user profiles and making the disks available on all servers in the collection. A coworker reminded you that users often store unnecessary files in their profiles, and you need to explore how to exclude such data from the profile and set a limit on the profile size.

As the sales application will publish on the RD Web Access site, you also have to learn how to configure and access RemoteApp programs from the Remote Desktop Web Access (RD Web Access) portal.

You been tasked with creating a proof of concept (POC) for a virtual machine (VM)—based session deployment of Virtual Desktop Infrastructure (VDI). You will create a virtual desktop template on a preexisting Microsoft Hyper-V VM manually with a few optimizations.

## Objectives

After completing this lab, you’ll be able to:

- Implement RDS
- Configure session collection settings and use RDS
- Configure virtual desktop template

## Lab Setup

**Estimated Time:** 90 minutes

For this lab, you'll use the following VMs:

- **WS-011T00A-SEA-DC1**
- **WS-011T00A-SEA-RDS1**
- **WS-011T00A-SEA-CL1**

**User Name**: Contoso\Administrator

**Password**: Pa55w.rd

Sign in to **WS-011T00A-SEA-DC1** and **WS-011T00A-SEA-RDS1** by using the following credentials:

- User name: **Administrator**
- Password: **Pa55w.rd**
- Domain: **Contoso**

Sign in to **WS-011T00A-SEA-CL1** by using the following credentials:

- User name: **Jane**
- Password: **Pa55w.rd**
- Domain: **Contoso**

### Exercise 1: Implementing RDS

### Scenario<!-- A scenario tells a story to give context to the assignments. The following pgph is not a scenario. Can we rewrite this so it is a scenario? -->

In this exercise, you will learn how to install RDS using Windows PowerShell and Server Manager. You will create a session collection using Windows PowerShell, and then change various collection settings. You will configure User Profile Disk using both Windows PowerShell and graphical user interfaces (GUIs)<!-- Instead of saying GUIs, can we say what the program is? Is it Server Manager? -->, and connect to a Remote Desktop Session Host (RD Session Host) using the Remote Desktop Web (RD Web) portal<!-- I think this is supposed to be the "RD Web Access" portal. If you agree, please s/r.-->. You will conclude the exercise by verify that a User Profile Disk has been created for a user.

The main tasks for this exercise are as follows:

1. Install RDS.
2. Create a session collection.
3. Configure the session collection properties.
4. Connect to the session collection from the RD Web portal

#### Task 1: Install RDS<!-- There should always be at least one sentence between headings. Please add one here. Even something like, "You will install RDS using Server Manager, and then by using Windows PowerShell." -->

##### Install RDS using Server Manager

1. On **SEA-RDS1**, open **Server Manager**, and then select **Manage**.
1. Select **Add Roles and Features**, and in the **Add Roles and Features Wizard**, select **Next**.
1. On the **Select installation type** page, select **Remote Desktop Services installation**, and then select **Next**.
1. On the **Select deployment type** page, select **Next**.

> **NOTE**: Even though, we could have selected the **Quick Start** deployment option and have all three required RDS role services installed on **SEA-RDS1**, you selected the **Standard deployment** option to practice selecting different servers for the RDS role services. Furthermore, the **Quick Start** deployment option will create a collection named **QuickSessionCollection** and publish the following  RemoteApp Programs: **Calculator**, **Paint**, and **WordPad**.

4. On the **Select deployment scenario** page, select **Session-based desktop deployment**, and then select **Next**.
5. On the **Review role services** page, select **Next**.
6. On the **Specify RD Connection Broker server** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**, and then select **Next**.
7. On the **Specify RD Web Access server** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```** and then select **Next**.
8. On the **Specify RD Session Host servers** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**, and then select **Next**.
9. On the **Confirm selections** page, select **Cancel**.

##### Install RDS using Windows PowerShell

> **NOTE**: We will now do the actual installation of RDS using Windows PowerShell. The previous steps were included to demonstrate how to install RDS using Server Manager.

1. Switch to **SEA-DC1**.
1. In the **Administrator: C:\Windows\system32\cmd.exe** command prompt window, enter the following command, and then select Enter:<br>
`powershell`<br>
1. In the command prompt window, enter the following command, and then select Enter:<br>
`$SVR="SEA-RDS1.contoso.com"`
1. In the command prompt window, enter the following command, and then select Enter:<br>
`New-RDSessionDeployment -ConnectionBroker $SVR -WebAccessServer $SVR -SessionHost $SVR`
1. Wait for the installation to complete, which will take approximately 5 minutes, and then wait as **SEA-RDS1** restarts automatically.
1. Switch to **SEA-RDS1**, and sign in as **Contoso\Administrator** with the password **Pa55w.rd**.
1. Open **Server Manager**, and wait for it to refresh.
1. In **Server Manager**, select **Remote Desktop Services**.

#### Task 2: Create a session collection

##### Create and configure a session collection using Server Manager

> **NOTE**: RDS in Windows Server supports two types of Session Collections on a single RD Session Host: an RD Session Collection, or a RemoteApp Session Collection. You cannot run both session collection types on the same RD Session Host by default. Therefore, when you're doing this exercise, you will first create an RD Session Host collection and verify that it works and then create a RemoteApp Session collection and verify that as well.

1. On **SEA-RDS1**, on the **Remote Desktop Service Overview** page, select **Collections**.
2. Under **COLLECTIONS**, select **TASKS**, and then select **Create Session Collection**. You might need to scroll to access this option.
3. On the **Before you begin** page, select **Next**.
4. On the **Name the collection** page, in the **Name** field, enter **IT**, and then select **Next**.
5. On the **Specify RD Session Host servers** page, in the **Server Pool** section, select **```SEA-RDS1.Contoso.com```**, and then select **Next**.
6. On the **Specify user groups** page, remove **CONTOSO\Domain Users**, and then select **Add**. Add the **CONTOSO\IT** group and then select **OK**. Verify that **CONTOSO\IT** is listed under **User Groups**, and then select **Next**.
7. On the **Specify user profile disks** page, clear the **Enable user profile disks** check box, and then select **Next**.
8. On the **Confirm selections** page, select **Cancel**.
1. When prompted, select **Yes**.
1. Minimize **Server Manager**.

##### Create and configure a session collection using Windows PowerShell

> **NOTE**: We will now create and configure the session collection using Windows PowerShell. The previous steps were included to demonstrate how to create a session collection using Server Manager.

1. On **SEA-RDS1**, open Windows PowerShell.
1. At the command prompt, enter the following command, and then select Enter:<br>
`New-RDSessionCollection –CollectionName IT –SessionHost SEA-RDS1.Contoso.com –CollectionDescription “This Collection is for the IT department in Contoso” –ConnectionBroker SEA-RDS1.Contoso.com`
1. Wait for the command to complete, which will take approximately 1 minute.
1. Maximize **Server Manager**, and then select **Overview**.
1. Refresh **Server Manager** by selecting the **F5** key.
1. In **Server Manager**, select **Collections**, and verify that a collection named **IT** is in the details pane.

#### Task 3: Configure the Session Collection properties<!-- Same comment about sentences between headings. -->

##### Configure device redirection settings

1. On SEA-RDS1, select the **IT** collection. Next to **PROPERTIES**, select **TASKS**, and then select **Edit Properties**.
2. On the **Session Collection** page, select the various settings and notice how the collection is configured.
3. select **Client Settings**, and verify that **Audio and video playback** and **Audio recording** is enabled.
4. select **User Profile Disks**, and verify that **User Profiles Disks** is not enabled.
1. In the **IT Properties** dialog box, select **Cancel**.
1. Minimize **Server Manager**.
1. In the Windows PowerShell window, enter the following command, and then select Enter:<br>`Get-RDSessionCollectionConfiguration –CollectionName IT –Client | Format-List`
1. Examine the output and notice that next to **ClientDeviceRedirectionOptions**, the following entries are listed:
    - **AudioVideoPlayBack**
    - **AudioRecording**
    - **PlugAndPlayDevice**
    - **SmartCard**
    - **Clipboard**
    - **LPTPort**
    - **Drive**

1. In the WindowsPowerShell window, enter the following command, and then select Enter:<br>`Set-RDSessionCollectionConfiguration –CollectionName IT –ClientDeviceRedirectionOptions PlugAndPlayDevice, SmartCard,Clipboard,LPTPort,Drive`
1. In the WindowsPowerShell window, enter the following command, and then select Enter:<br>`Get-RDSessionCollectionConfiguration –CollectionName IT –Client | Format-List`
1. Examine the output and notice that next to **ClientDeviceRedirectionOptions**, only the following entries are listed now:
    - **PlugAndPlayDevice**
    - **SmartCard**
    - **Clipboard**
    - **LPTPort**
    - **Drive**

##### Configure User Profile Disks for IT collection

1. Switch to **SEA-DC1**, and in the command prompt window, enter the following commands, one line at a time, and then select Enter:<br>

- `New-Item C:\RDSUserProfiles -itemtype directory`<br>
- `New-SMBShare –Name “RDSUserProfiles” –Path “C:\RDSUserProfiles” –FullAccess "Contoso\SEA-RDS1$", "Contoso\administrator"`<br>
- `$acl = Get-Acl C:\RDSUserProfiles`<br>
- `$AccessRule = New-Object System.Security.AccessControl.FileSystemAccessRule("Contoso\SEA-RDS1$","FullControl","Allow")`
- `$acl.SetAccessRule($AccessRule)`
- `$acl | Set-Acl C:\RDSUserProfiles`

2. Verify that each command executes successfully.
3. Switch to **SEA-RDS1**, and select the **IT** collection.
1. Next to ****PROPERTIES**, select **TASKS**, and then select **Edit Properties**.
1. On the **Session Collection** page, select **User Profile Disks**, and then select **Enable user profile disks**.
1. In the **Location** field, enter **\\\SEA-DC1\RDSUserProfiles**. In the **Maximum size (in GB)**, enter **10**, and then select **OK**.

#### Task 4: Connect to the Session Collection from RD Web portal

1. On **SEA-CL1**, Open **Microsoft Edge**.
2. In Microsoft Edge, browse to **```https://SEA-RDS1.Contoso.com/rdweb```**.
3. On the **This site is not secure** page, select **Details**, and then select **Go on to the webpage**.

> **NOTE**: This page opens because RD Web is using a self-signed certificate that is not trusted by the client. In a real production deployment, you would use trusted certificates.

4. On the **RD Web Access** page, sign-in using **contoso\jane** as the user name, and password as **Password**. If prompted by **Microsoft Edge** to save the password, select **Never**.
5. On the **RD Web Access** page, under **Current folder: /**, select **IT**, and when prompted, select **Open**.
6. In the **Remote Desktop Connection** dialog box, select **Connect**.

> **NOTE**: This prompts the **Unknown publisher** pop up window because certificates for RDS have not yet been configured.

7. In the **Windows Security** dialog box, use **Pa55w.rd** as the password, and then select Enter.
8. After the connection completes, on **SEA-RDS1**, sign out of the session.
9. Sign out of the RD Web portal and close **Microsoft Edge**.

##### Verify User Profile Disk creation

1. Switch to **SEA-DC1**, and in the command prompt window, enter the following command, and then select Enter:<br>
`cd\`<br>
2. Enter the following command, and then select Enter:<br>
`cd RDSUserProfiles`<br>
3. Enter the following command, and then select Enter:<br>
`dir`<br>
4. Examine the contents of the **RDSUserProfiles** folder. Verify that there is a **.vhdx** file with an SID (a long string that starts with **S-1-5-21**) in its name.

### Exercise 2: Configuring RemoteApp collection settings

### Scenario

In this exercise, you will explore how to add RemoteApp Programs to RDS using both Server Manager and Windows PowerShell. You will then run a RemoteApp Program from the RD Web portal.

The main tasks for this exercise are as follows:

1. Create and configure a RemoteApp collection using Server Manager
2. Create and configure a RemoteApp program using Windows PowerShell.
3. Run RemoteApp from RD Web portal.

#### Task 1: Create and configure a RemoteApp collection using Server Manager

1. Switch to **SEA-RDS1**.
1. In **Server Manager**, next to **REMOTEAPP PROGRAMS**, select **TASKS**, and then select **Publish RemoteApp Programs**.
1. On the **Select RemoteApp programs** page, select **WordPad** from the list, and then select **Next**.
1. On the **Confirmation** page, select **Publish**, and then wait for the RemoteApp to be published.
1. Verify that **WordPad** is listed in the details pane under **RemoteApp Program**, and then select **Close**.

#### Task 2: Create and configure a RemoteApp program using Windows PowerShell

1. ON **SEA-RDS1**, right-click or access the context menu for **Start**, and then select **Windows PowerShell.**
1. At the Windows PowerShell command prompt, enter the following command, and then select Enter:<br>
`New-RDRemoteApp -Alias Paint -DisplayName Paint -FilePath "C:\Windows\system32\mspaint.exe" -ShowInWebAccess 1 -collectionname IT -ConnectionBroker SEA-RDS1.Contoso.com`
1. Enter the following command, and then select Enter:<br>
`Get-RDRemoteApp -CollectionName IT`
1. Examine the output of the command. Notice that you will get a list of all published RemoteApp Programs.
1. Maximize **Server Manager**, and then select **Overview**.
1. Refresh **Server Manager** by selecting **F5**.
1. In **Server Manager**, select the **IT** collection and verify that **Paint** is listed in the details pane under **REMOTEAPP PROGRAMS**.

#### Task 3: Run RemoteApp from RD Web portal

1. On **SEA-CL1**, open **Microsoft Edge**, and brwose to **```https://SEA-RDS1.Contoso.com/rdweb```**.
2. On the **This site is not secure page**, select **Details**, and then select **Go on to the webpage**.

> **NOTE**: This page opens because RD Web is using a self-signed certificate that is not trusted by the client. In a real production deployment, you would use trusted certificates.

3. On the **RD Web Access** page, sign-in as **contoso\jane** using **Pa55w.rd** as the password.
4. On the **RD Web Access** page, run **Paint**, and when prompted, select **Open**.
5. In the **Remote Desktop Connection** dialog box, select **Connect**.

> **NOTE**: The **Unknown publisher** pop-up window displays because you have not yet configured certificates for RDS.

6. In the **Windows Security** dialog box, use **Pa55w.rd** as the password.
7. Wait for the **Paint** RemoteApp program to start, and then test its functionality.
8. Close **Paint**.
9. Back in the RD Web portal**RD Web, sign out.
10. Close Microsoft Edge.

### Exercise 3: Configure a virtual desktop template

### Scenario

In this exercise, you will explore how to manually configure a virtual desktop template. The Hyper-V VM, you are using has already been created.

The main tasks for this exercise are as follows:

1. Verify the operating system (OS) version.
2. Disable unnecessary services.
3. Disable unnecessary scheduled tasks.
4. Prepare the virtual desktop template by using System Preparation Tool (Sysprep).

#### Task 1: Verify the OS version

1. On **SEA-CL1**, sign in as **.\Admin** with the password **Pa55w.rd**.
2. On **SEA-CL1**, and then open **About your pc**.
3. In the **Settings** app, on the **About screen**, verify the following information:

- The Windows operating system edition is Windows 10 Enterprise
- The System type is 64-bit OS

4. Close the **Settings** app.

#### Task 2: Disable unnecessary services

1. On **SEA-CL1**, and then open **Services**.
2. In the **Services** window, right-click or access the context menu for **Background Intelligent Transfer Service**.
3. In the **Background Intelligent Transfer Service Properties (Local Computer)** dialog box, on the **General** tab, select **Stop**.
4. In the **Startup type** box, select **Disabled**, and then select **OK**.
5. Repeat steps 2 through 4 for the following services:

- **Diagnostic Policy Service**
- **Shell Hardware Detection**
- **Volume Shadow Copy**
- **Windows Search**

5. Close the **Services** window.

#### Task 3: Disable unnecessary scheduled tasks

1. On **SEA-CL1**, and then open **Task Scheduler**.
2. In **Task Scheduler**, expand **Task Scheduler Library**, expand **Microsoft**, expand **Windows**, and then select **Defrag**.
3. Disable **ScheduledDefrag**, and then close the **Task Scheduler** window.

#### Task 4: Prepare the virtual desktop template by using Sysprep

1. On **SEA-CL1**, browse to **C:\Windows\System32\Sysprep**, and then run **sysprep.exe**.
2. In the **System Preparation tool 3.14** dialog box, in the **System Cleanup Action** box, select **Enter System Out-of-Box Experience (OOBE)**, and then select the **Generalize** check box.
4. In the **Shutdown Options** box, select **Shutdown**, and then select **OK**.
5. Wait while Sysprep completes and shuts down the VM.

After completing this exercise, you will have prepared a Hyper-V VM to be a virtual desktop template.