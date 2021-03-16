---
lab:
    title: 'Lab: Implementing and configuring virtualization in Windows Server'
    module: 'Module 5: Hyper-V virtualization and containers in Windows Server'
---

# Lab: Implementing and configuring virtualization in Windows Server

## Scenario

Contoso is a global engineering and manufacturing company with its head office in Seattle, USA. An IT office and data center are in Seattle to support the Seattle location and other locations. Contoso recently deployed a Windows Server 2019 server and client infrastructure.

Because of many physical servers being currently underutilized, the company plans to expand virtualization to optimize the environment. Because of this, you decide to perform a proof of concept to validate how Hyper-V can be used to manage a virtual machine environment. Also, the Contoso DevOps team wants to explore container technology to determine whether they can help reduce deployment times for new applications and to simplify moving applications to the cloud. You plan to work with the team to evaluate Windows Server containers and to consider providing Internet Information Services (Web services) in a container.

## Objectives

After completing this lab, you'll be able to:

- Create and configure VMs.
- Install and configure containers.

## Lab Setup

**Estimated Time:** 60 minutes

**Virtual Machines**: WS-011T00A-SEA-DC1, WS-011T00A-SEA-ADM1, and WS-011T00A-SEA-SVR1

**User Name**: Contoso\Administrator

**Password**: Pa55w.rd
Note that Internet access is required to successfully complete the second exercise in this lab.

## Lab Startup

1. Select  **SEA-DC1**.
1. Sign in by using the following credentials:
   - User name: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **Contoso**

1. Repeat these steps for **SEA-ADM1** and **SEA-SVR1**.

## Exercise 1: Creating and configuring VMs

### Exercise scenario

In this exercise, you will use Hyper-V Manager and Windows Admin Center to create and configure a virtual machine. You will start with creating a private virtual network switch. Next you decide to create a differencing drive of a base image that has already been prepared with the operating system to be installed on the VM. Finally, you will create a generation 1 VM that uses the differencing drive and private switch that you have prepared for the proof of concept.

The main tasks for this exercise are:

1. Create a Hyper-V virtual switch
1. Create a virtual hard disk
1. Create a virtual machine
1. Manage virtual machines using Windows Admin Center

### Task 1: Create a Hyper-V virtual switch

1. On SEA-ADM1, open **Server Manager**.
1. In Server Manager, select **All Servers**.
1. In the Servers list, select and hold (or right-click) or access the context menu **SEA-SVR1** and then select **Hyper-V Manager**.
1. Use the **Virtual Switch Manager** to create the following switch:
   - Name: **Contoso Private Switch**
   - Connection type: **Private network**

### Task 2: Create a virtual hard disk

1. On SEA-ADM1, in Hyper-V Manager, use the **New Virtual Hard Disk Wizard** to create a new virtual hard disk as follows:
   - Disk Format: **VHD**
   - Disk Type: **Differencing**
   - Name: **SEA-VM1**
   - Location: **C:\Base**
   - Parent Disk: **C:\Base\BaseImage.vhd**

### Task 3: Create a virtual machine

1. On SEA-ADM1, in Hyper-V Manager, create a new virtual machine as follows:
   - Name: **SEA-VM1**
   - Location: **C:\Base**
   - Generation: **Generation 1**
   - Memory: **4096**
   - Networking: **Contoso Private Switch**
   - Hard disk: **C:\Base\SEA-VM1.vhd**

1. Open the **Settings** for **SEA-VM1** and enable **Dynamic Memory** with a Maximum RAM value of **4096**.
1. Close Hyper-V Manager.

### Task 4: Manage Virtual Machines using Windows Admin Center

1. On SEA-ADM1, on the taskbar, select **Microsoft Edge**.
1. In Microsoft Edge, on the Favorites Bar, select **Windows Admin Center**.
1. In the Windows Security box, enter **Contoso\Administrator** with the password of **Pa55w.rd** and then select **OK**.
1. In the **All connections** list, select **SEA-SVR1**.

   > **Note**: You may need to select **Manage As** to then enter the credentials in the next step.

1. In the **Specify your credentials** page, select **Use another account for this connection**, and then enter **Contoso\Administrator** with the password of **Pa55w.rd**.
1. In the **Tools** list, select **Virtual Machines**. Review the Summary pane.
1. On SEA-VM1, create a new disk, 5 GB in size.

   > **Note**: The **Save Disk** Setting may be greyed out which is a known issue. A workaround would be to create the disk in Hyper-V if needed.

1. Start **SEA-VM1** and then display the statistics for the running VM.
1. Refresh the page and then shut down the VM.
1. In the **Tools** list, select **Virtual switches** and identify the existing switches.
1. Close all open windows on SEA-ADM1.

### Exercise 1 results

After this exercise, you should have used Hyper-V Manager and Windows Admin Center to create a virtual switch, create a virtual hard disk, and then create and manage a virtual machine.

## Exercise 2: Installing and configuring containers

### Exercise Scenario

In this exercise, you will use Docker to install and run Windows containers. You will also use Windows Admin Center to manage containers.

The main tasks for this exercise are:

1. Install Docker on Windows Server
1. Install and run a Windows container
1. Use Windows Admin Center to manage containers

### Task 1: Install Docker on Windows Server

1. On SEA-ADM1, open **Windows Admin Center** using the Contoso\Administrator credentials.
1. Connect to **SEA-SVR1** using the Contoso\Administrator credentials and then connect to the server using PowerShell.

   > **Note**: The Powershell connection in **WAC** may be slow due to nested virtualization used in the lab, so an alternate method is to use **Enter-PSSession -computername SEA-SVR1** from a Powershell window on **SEA-ADM1**.

1. At the PowerShell command prompt enter the following command and then select Enter:

   `Install-Module -Name DockerMsftProvider -Repository PSGallery -Force`

1. At the PowerShell command prompt enter the following command and then select Enter:

   `Install-Package -Name docker -ProviderName DockerMsftProvider`

1. After the installation is complete, restart the computer by using the following command:

   `Restart-Computer -Force`

### Task 2: Install and run a Windows container

1. After SEA-SVR1 restarts reconnect the PowerShell tool and provide the Contoso\Administrator credentials.
1. Verify the installed version of Docker by using the following command:

   `Get-Package -Name Docker -ProviderName DockerMsftProvider`

   > **Note**: You may need to run **Start-Service -name Docker** before running the next commands.

1. To verify whether any Docker images are currently pulled, use the following command:

   `Docker images`

1. To review docker base images from the online Microsoft repository, use the following command:

   `Docker search Microsoft`

   > **Note**: You may disregard any errors and continue with the next step. 

1. To download a server core image, with IIS, that matches the host operating system, run the following command:

   `docker pull mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019`

   > **Note**: This download may take more than 15 minutes to complete.

1. To verify the Docker image that is currently pulled, use the following command:

   `Docker images`

1. To run the container, enter the following command:

   `Docker run -d -p 80:80 --name ContosoSite mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 cmd`

   This command runs the IIS image as a background service (-d) and configures networking such that port 80 of the container host maps to port 80 of the container.

1. Enter the following command to retrieve the IP address information of the container host:

   `ipconfig`

   Note the IPv4 address of the Ethernet adapter named vEthernet (nat). This is the address of the new container. Make a note of the IPv4 address of the Ethernet adapter named **Ethernet**. This is the IP address of the Host (SEA-SVR1).

1. In Microsoft Edge, open another tab and then enter **```<http://172.16.10.12>```**. Observe the default IIS page.

1. In the remote PowerShell session, enter the following command:

   `docker ps`

   This command provides information on the container that is currently running on SEA-SVR1. Take note of the container ID as you will use it to stop the Container.

1. In the remote PowerShell session, enter the following command:

   `docker stop *<ContainerID>*`

1. Rerun the `docker ps` command to confirm that the container has stopped.

### Task 3: Use Windows Admin Center to manage containers

1. On SEA-ADM1, ensure that SEA-SVR1 is targeted in the Windows Admin Center and then select the **Containers** tool.
1. Browse through each of the **Summary**, **Containers**, **Images**, **Networks**, and **Volumes** tabs.

### Exercise 2 results

After this exercise, you should have installed Docker on Windows Server and installed and run a Windows container containing web services.
