---
lab:
    title: 'Lab: Deploying and configuring Windows Server'
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

For this lab, you’ll use the available virtual machines, **WS-011T00A-SEA-DC1-B** and **WS-011T00A-SEA-ADM1-B**.

## Exercise 1: Deploying and configuring Server Core

### Scenario

As part of a deployment plan, you will implement Server Core and configure it for remote management. **WS-011T00A-SEA-SVR4-B** is pre-configured to open from the Win2019_1809_Eval.iso to install Windows Server.

The main tasks for this exercise are as follows:

1. Install Server Core.
1. Configure Server Core with sconfig and PowerShell.
1. Install Features on Demand on Server Core.

### Task 1: Install Server Core

1. Start **WS-011T00A-SEA-SVR4-B**. Windows will start loading the installation files.
1. Install Windows Server 2019 Standard Evaluation.
1. Accept the license.
1. Perform a custom install.
1. Accept the default install location.
1. Set the Administrator password to **Pa55w.rd**.

### Task 2: Configure Server Core with sconfig and PowerShell

1. At the command prompt, open the sconfig tool.
1. Access the Network Settings.
1. Modify adapter index #1.
1. Modify the following settings:

   - IP: **172.16.10.15**

   - Subnet mask: **255.255.0.0**

   - Default Gateway: **172.16.10.1**
1. Set the DNS server to be **172.16.10.10**. Leave the alternate DNS server blank.
1. Return to the main menu and exit to command line.
1. Open PowerShell.
1. Run the ```Rename-Computer -NewName SEA-SVR4 -restart -force``` cmdlet.
1. Sign in and open PowerShell
1. Run the ```Add-Computer -DomainName Contoso.com -Credential Contoso\Administrator -restart -force``` cmdlet. Enter **Pa55w.rd** when prompted for credentials.

### Task 3: Install Features on Demand on Server Core

1. Mount the **Win2019_FOD.iso** image file to **drive D** of **SEA-SVR4**.
1. Sign in as **Contoso\Administrator**.
1. At the command prompt, run **Explorer.exe**. Note that command fails and returns an error.
1. Open PowerShell.
1. At the PowerShell prompt, run ```Add-Windowscapability -Online -Name Servercore.Appcompatibility~~~~0.0.1.0 -Source D:```.
1. Run **Restart-computer**, and then sign in as **Administrator**.
1. Run **Explorer.exe**. Note that File Explorer now opens successfully.

### Results

After completing this exercise, you will have installed Server Core, configured the networking settings, renamed the server, and joined the Contoso domain. You will have also installed Features on Demand.

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
1. Sign in as **Contoso\Administrator**.
1. Open File Explorer and browse to **C:\Labfiles\Mod01**.

1. Double-click or select **WindowsAdminCenter1910.2.msi**, and then select Enter. Install  Windows Admen Center by accepting all the defaults.

### Task 2: Add servers for remote administration

1. Open Microsoft Edge and go to ```Https://Sea-Adm1```.
1. In Windows Admin Center, add **SEA-DC1** and **SEA-SVR4**.

### Task 3: Configure Windows Admin Center extensions

1. Open **Settings**.
1. Select **Extensions**, and then select **Feeds**.
1. Add the package source **C:\Labfiles\Mod01**.
1. Select **Available Extensions**. Now you can observe the extensions.
1. Install the **DNS (Preview)** extension.
1. Switch to the Server Manager module.
1. Select **DNS** and install the DNS PowerShell tools. The tools will take a few moments to install.
1. Open the ```Contoso.com``` zone and observe the console.

### Task 4: Verify remote administration

1. Select **Overview**. Note that the **details** pane contains basic server information performance monitoring much like **Task Manager**. In the left pane, observe the basic administration tools available.
1. Select **Roles and Features** and note what is installed and what is available to install.
1. Install the **Telnet Client**.
1. Open **Settings** and enable **Remote Desktop**.
1. Close the browser.

### Task 5: Administer servers with Remote PowerShell

1. Open PowerShell.
1. Run the ```Enter-PSSession -ComputerName SEA-DC1``` cmdlet.
1. Run the ```Get-Service -Name AppIDSvc``` cmdlet. Note that the service is currently stopped.
1. Run the ```Start-Service -Name AppIDSvc``` cmdlet.
1. Run the ```Get-Service -Name AppIDSvc``` cmdlet. The service is running now.

### Results

After completing this exercise, you will have installed Windows Admin Center and connected the server to manage. You performed management tasks of installing a feature and enabling Remote Desktop. Finally, you used Remote PowerShell to check the status of a service and start a service.
