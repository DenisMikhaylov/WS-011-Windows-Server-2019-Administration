---
lab:
    title: 'Lab: Deploying and configuring Windows Server'
    type: 'Answer Key'
    module: 'Module 1: Windows Server administration'
---

# Lab: Deploying and configuring Windows Server

## Scenario

Contoso, Ltd. wants to implement several new servers in their environment, and they have decided to use Server Core. They also want to implement Windows Admin Center for remote management of both these servers and other servers in the organization.

## Objectives

- Deploy and configure Server Core
- Implement and configure Windows Admin Center

## Estimated time: 45 minutes

## Lab setup

VMs: **WS-011T00A-SEA-DC1-B**, **WS-011T00A-SEA-ADM1-B**, **WS-011T00A-SEA-SVR4**

Username: **Contoso\Administrator**

Password: **Pa55w.rd**

For this lab, you’ll use the available VM environment. Before you begin the lab, complete the following steps:

1. Start **WS-011T00A-SEA-DC1-B**.
1. Start **WS-011T00A-SEA-ADM1-B**.

## Exercise 1: Deploying and configuring Server Core

### Scenario

As a part of the deployment plan, you will implement Server Core and configure it for remote management. **WS-011T00A-SEA-SVR4-B** is pre-configured to turn on from the **Win2019_1809_Eval.iso** to install Windows Server.

The main tasks for this exercise are as follows:

1. Install Server Core.
1. Configure Server Core with sconfig and PowerShell.
1. Install Features on Demand on Server Core.

### Task 1: Install Server Core

1. Start **WS-011T00A-SEA-SVR4-B**. Windows will start loading the installation files.
1. At the **Windows Setup** page, note the **Language, Time and Keyboard** settings, and then select **Next**.
1. Select **Install now**.
1. On the **Select the operating system you want to install** page, ensure that **Windows Server 2019 Standard Evaluation** is selected, and then select **Next**.
1. Select the **I accept license terms** check box to accept the license terms, and then select **Next**.
1. On the **Which type of installation do you want?** page, select **Custom: Install Windows only (advanced)**.
1. On the **Where do you want to install Windows?** page, select **Next**. Installation will take a few minutes.
1. After installation completes, select Ctrl-Alt-Del. After reading the message about changing the password, select Enter.
1. In the **New password** and **Confirm password** fields, enter **Pa55w.rd**, and then select Enter twice to acknowledge the password has been changed.

### Task 2: Configure Server Core with sconfig and PowerShell

1. At the command prompt, enter **sconfig**, and then select Enter.
1. To access **Network Settings**, enter **8**, and then select Enter.
1. To change adapter index #1, enter **1**, and then select Enter.
1. To set the network adapter address, enter **1**, and then select Enter.
1. To set a static IP address, enter **S**, and then select Enter.
1. To set the IP address, enter **172.16.10.15**, and then select Enter.
1. To set the subnet mask, enter **255.255.0.0**, and then select Enter.
1. To set the Default Gateway and observe the resulting settings, enter **172.16.10.1**, and then select Enter.
1. To set the Domain Name System (DNS) server, enter **2**, and then select Enter.
1. Enter **172.16.10.10**, and then select Enter. Then, to dismiss the message box, select **OK**. To leave the alternate DNS server blank, select Enter.
1. To return to the main menu, enter **4**, and then select Enter.
1. To exit to Command Line, enter **15**, and then select Enter.
1. At the command prompt, enter **PowerShell**, and then select Enter.
1. At the PowerShell prompt, enter ```Rename-Computer -NewName SEA-SVR4 -restart -force```, and then select Enter.
1. On **SEA-SVR4**, select Ctrl+Alt+Del, enter the password **Pa55w.rd**, and then select Enter.
1. At the command prompt, enter **PowerShell**, and then select Enter.
1. At the PowerShell prompt, enter ```Add-Computer -DomainName Contoso.com -Credential Contoso\Administrator -restart -force```, and then select Enter. In the **Windows PowerShell credential request** window, enter **Pa55w.rd**, and then select **OK**.

### Task 3: Install Features on Demand on Server Core

1. Mount the **Win2019_FOD.iso** image file to drive D of SEA-SVR4.
1. On SEA-SVR4, select Ctrl+Alt+Del, enter the password **Pa55w.rd**, and then select Enter.
1. At the command prompt, enter **Explorer.exe**. Note that the command does not run and returns an error.
1. At the command prompt, enter **PowerShell**, and then select Enter.
1. At the PowerShell prompt, enter ```Add-Windowscapability -Online -Name Servercore.Appcompatibility~~~~0.0.1.0 -Source D:```.
1. After completion, to restart the server and then sign in with the password **Pa55w.rd** at the PowerShell prompt, enter **Restart-computer**.
1. At the command prompt, enter **Explorer.exe**. Note that File Explorer now opens successfully.

### Results

After completing this exercise, you will have installed Server Core, configured the networking settings, renamed the server, and joined the Contoso domain.

## Exercise 2: Implementing and using remote server administration

### Scenario

Now that you have deployed the Server Core servers, you need to implement Windows Admin Center for remote administration.

The main tasks for this exercise are as follows:

1. Install Windows Admin Center.
1. Add servers for remote administration.
1. Configure Windows Admin Center extensions.
1. Verify remote administration.
1. Administer servers with Remote PowerShell.

### Task 1: Install Windows Admin Center

1. Connect to **WS-011T00A-SEA-ADM1-B**.
1. Select Ctrl+Alt+Del and sign in as **Contoso\Administrator** with a password of **Pa55w.rd**.
1. From the taskbar, open File Explorer, and then browse to **C:\Labfiles\Mod01**.
1. Double-click or select **WindowsAdminCenter1910.2.msi**.
1. On the **Windows Admin Center Setup** page, to accept the terms, select the check box, and then select **Next**.
1. On the **Use Microsoft Update to help keep your computer secure and up-to-date** page, select **Next**.
1. On the **Install Windows Admin Center on Windows Server** page, select **Next**.
1. On the **Configure Gateway Endpoint** page, select **Next**, and then select **Install**.
1. Select **Finish**.

### Task 2: Add servers for remote administration

1. From the taskbar, open Microsoft Edge.
1. In the address bar, enter ```Https://Sea-Adm1```, and then select Enter. The console will open. Notice that the host server is listed by default.
1. In Windows Admin Center, select **Add**.
1. In the Add resources pane, in the Windows Server box, select **Add**
1. In the **Server name** box, enter **SEA-DC1**. In a few moments, the server will be found through DNS, and then select **Add**. .
1. Select **Add**.
1. Repeat the steps to add **SEA-SVR4**.

### Task 3: Configure Windows Admin Center extensions

1. In the upper right corner, select the **Settings** icon (the cog wheel).
1. In the left pane, select **Extensions**. Note that there are no available extensions.
1. In the **details** pane, select **Feeds**, and then select **Add**.
1. In the **Add package source** pane, in the **Extension feed** URL or path, enter **C:\Labfiles\Mod01**, and then select **Add**.
1. Select **Available extensions**. Now you can observe the extensions.
1. Select the **DNS (Preview)** extension, and then select **Install**. The extension will install and Windows Admin Center will refresh.
1. On the top menu, next to **Settings**, select the drop-down arrow, and then select **Server Manager**.
1. On the **Server connections** page, select the ```SEA-DC1.Contoso.com``` link.
1. To install the DNS PowerShell tools in the left pane, select **DNS**, and then select **Install**. The tools will take a few moments to install.
1. Select the ```Contoso.com``` zone and observe the console.

### Task 4: Verify remote administration

1. In Windows Admin Center, select the **Overview** icon in the left pane. Note that the **details** pane for Windows Admin Centers displays basic server information and performance monitoring, which is like **Task Manager**.
1. In the left pane, scroll down and observe the basic administration tools available. Select **Roles and Features** and note what is installed and what is available to install. Scroll down and check the box beside **Telnet Client**, and then select **Install**, which will be at the top of the list.
1. To install the Telnet Client, select **Yes**. In a few moments, you will get a message that the Telnet Client installed successfully.
1. In the **details** pane, select **Remote Desktop**, select **Go to settings**, select the **Allow remote connections to this computer** radio button, and then select **Save**.
1. Close the browser.

### Task 5: Administer servers with Remote PowerShell

1. Select **Start**, enter **PowerShell**, and then select Enter.
1. At the PowerShell prompt, enter ```Enter-PSSession -ComputerName SEA-DC1```, and then select Enter.
1. Enter ```Get-Service -Name AppIDSvc```, and then select Enter. Note that the service is currently stopped.
1. Enter ```Start-Service -Name AppIDSvc```, and then select Enter.
1. Enter ```Get-Service -Name AppIDSvc```, and then select Enter. Note that the service is currently running.

### Results

After completing this exercise, you will have installed Windows Admin Center and connected the server to manage. You performed management tasks of installing a feature and enabling Remote Desktop. Finally, you used Remote PowerShell to check the status of a service and start a service.
