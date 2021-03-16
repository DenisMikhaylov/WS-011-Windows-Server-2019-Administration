---
lab:
    title: 'Lab: Implementing and configuring virtualization in Windows Server'
    type: 'Answer Key'
    module: 'Module 5: Hyper-V virtualization and containers in Windows Server'
---

# Lab answer key: Implementing and configuring virtualization in Windows Server

### Exercise 1: Creating and configuring VMs

#### Task 1: Create a Hyper-V virtual switch

1. On SEA-ADM1, select **Start** and then select **Server Manager**.
1. In Server Manager, select **All Servers**.
1. In the Servers list, select and hold (or right-click) or access the context menu **SEA-SVR1** and then select **Hyper-V Manager**.
1. In Hyper-V Manager, ensure that **```SEA-SVR1.Contoso.com```** is selected.
1. In the Actions pane, select **Virtual Switch Manager**.
1. In the **Virtual Switch Manager**, in the **Create virtual switch** pane, select **Private** and then select **Create Virtual Switch**.
1. In the **Virtual Switch Properties** box, enter the following details and then select **OK**:
   - Name: **Contoso Private Switch**
   - Connection type: **Private network**

#### Task 2: Create a virtual hard disk

1. On SEA-ADM1, in Hyper-V Manager, select **New** and then select **Hard Disk**. The **New Virtual Hard Disk Wizard** starts.
1. On the **Before you Begin page**, select **Next**.
1. On the **Choose Disk Format** page, select **VHD** and then select **Next**.
1. On the **Choose Disk Type** page, select **Differencing** and then select **Next**.
1. On the **Specify Name and Location** page enter the following and then select **Next**:
   - Name: **SEA-VM1**
   - Location: **C:\Base**
1. On the **Configure Disk** page, in the **Location** box, enter **C:\Base\BaseImage.vhd** and then select **Next**.
1. On the **Summary** page, select **Finish**.

#### Task 3: Create a virtual machine

1. On SEA-ADM1, in Hyper-V Manager, select **New** and then select **Virtual Machine**. The **New Virtual Machine Wizard** starts.
1. On the **Before you Begin page**, select **Next**.
1. On the **Specify Name and Location** page, enter **SEA-VM1** and then select the check box next to **Store the virtual machine in a different location**.
1. In the **Location** box, enter **C:\Base** and then select **Next**.
1. On the **Specify Generation** page, select **Generation 1** and then select **Next**.
1. On the **Assign Memory** page, enter **4096** and then select **Next**:
1. On the **Configure Networking** page, select the Connection drop-down menu, select **Contoso Private Switch** and then select **Next**.
1. On the **Connect Virtual Hard Disk** page, select **Use an existing virtual hard disk**, and then select **Browse**.
1. Browse to **C:\Base**, select **SEA-VM1.vhd**, select **Open** and then select **Next**.
1. On the **Summary** page, select **Finish**. Notice that SEA-VM1 displays in the Virtual Machines list.
1. Select **SEA-VM1** and then in the Actions pane, under SEA-VM1, select **Settings**.
1. In the **Hardware** list, select **Memory**.
1. In the **Dynamic Memory** section, select the check box next to **Enable Dynamic Memory**.
1. Next to **Maximum RAM**, enter **4096** and then select **OK**.
1. Close Hyper-V Manager.

### Task 4: Manage Virtual Machines using Windows Admin Center

1. On SEA-ADM1, on the taskbar, select **Microsoft Edge**.
1. In Microsoft Edge, on the Favorites Bar, select **Windows Admin Center**.
1. In the Windows Security box, enter **Contoso\Administrator** with the password of **Pa55w.rd** and then select **OK**.
1. In the **All connections** list, select **SEA-SVR1**.

   > **Note**: You may need to select **Manage As** to then enter the credentials in the next step.

1. In the **Specify your credentials** page, select **Use another account for this connection**, and then enter **Contoso\Administrator** with the password of **Pa55w.rd**.
1. In the **Tools** list, select **Virtual Machines**. Review the Summary pane.
1. Select the **Inventory** tab. Notice the two virtual machines.
1. Select **SEA-VM1**. Review the **Properties** pane.
1. Select **Settings** and then select **Disks**.
1. Select **Add disk**.
1. Select **Create an empty virtual hard disk** and then in the Size box enter **5** GB.
1. Select **Save disks settings** and then select **Close**.

   > **Note**: The **Save Disk** Setting may be greyed out which is a known issue. A workaround would be to create the disk in Hyper-V if needed.

1. On the **Properties** page, select **Start** to start **SEA-VM1**.
1. Scroll down and display the statistics for the running VM.
1. Refresh the page and then select **Shut down**. Select **Yes** to confirm.
1. In the **Tools** list, select **Virtual switches**. Notice the two switches that have been configured.
1. Close all open windows on SEA-ADM1.

### Exercise 1 results

After this exercise, you should have used Hyper-V Manager and Windows Admin Center to create a virtual switch, create a virtual hard disk, and then create and manage a virtual machine.

### Exercise 2: Installing and configuring containers

#### Task 1: Install Docker on Windows Server

1. On SEA-ADM1, from the taskbar, open **Microsoft Edge**.
1. On the Favorites bar, select **Windows Admin Center**.
1. In the Windows Security box, enter the following credentials:

   - User name: **Contoso\Administrator**
   - Password: **Pa55w.rd**
1. On the **All connections** page, select **SEA-SVR1**.
1. On the **Specify your credentials** page, select **Use another account for this connection**. Provide the **Contoso\Administrator** credentials, and then select **Continue**.
1. In the **Tools** list, select **PowerShell**. Provide the **Contoso\Administrator** credentials, and then select **Enter**. You are now connected to SEA-SVR1 using a Remote PowerShell connection.

   > **Note**: The Powershell connection in **WAC** may be slow due to nested virtualization used in the lab, so an alternate method is to use **Enter-PSSession -computername SEA-SVR1** from a Powershell window on **SEA-ADM1**.

1. At the PowerShell command prompt enter the following command and then select Enter:

   `Install-Module -Name DockerMsftProvider -Repository PSGallery -Force`

1. At the NuGet Provider prompt, enter **Y** for yes and then select Enter.
1. At the PowerShell command prompt enter the following command and then select Enter:

   `Install-Package -Name docker -ProviderName DockerMsftProvider`

1. At the confirmation prompt, enter **A** for Yes to All and then select Enter.
1. After the installation is complete, restart the computer by using the following command:

   `Restart-Computer -Force`

#### Task 2: Install and run a Windows container

1. After SEA-SVR1 restarts reconnect the PowerShell tool and provide the Contoso\Administrator credentials.
1. Verify the installed version of Docker by using the following command:

   `Get-Package -Name Docker -ProviderName DockerMsftProvider`

   > **Note**: You may need to run **Start-Service -name Docker** before running the next commands.

1. To verify whether any Docker images are currently pulled, use the following command:

   `Docker images`

   Notice that there are no images in the local repository store.
1. To review docker base images from the online Microsoft repository, use the following command:

   `Docker search Microsoft`

   Notice the variety of base images each representing various runtime scenarios.

   > **Note**: You may disregard any errors and continue with the next step.

1. To download a server core image, with IIS, that matches the host operating system, run the following command:

   `docker pull mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019`

   > **Note**: This download may take more than 15 minutes to complete.

1. To confirm the Docker image that is currently pulled, use the following command:

   `Docker images`

   Notice that there is now an image listed in the local repository store.

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

   `docker stop <ContainerID>`

1. Rerun the `docker ps` command to confirm that the container has stopped.

#### Task 3: Use Windows Admin Center to manage containers

1. On SEA-ADM1, ensure that SEA-SVR1 is targeted in the Windows Admin Center and then select the **Containers** tool.
1. Browse through each of the **Summary**, **Containers**, **Images**, **Networks**, and **Volumes** tabs.

### Exercise 2 results

After this exercise, you should have installed Docker on Windows Server and installed and run a Windows container containing web services.
