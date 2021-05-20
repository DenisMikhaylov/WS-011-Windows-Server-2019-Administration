---
lab:
    title: 'Lab: Configuring security in Windows Server'
    type: 'Answer Key'
    module: 'Module 8: Windows Server security'
---

# Lab answer key: Configuring security in Windows Server

## Exercise 1: Configuring Windows Defender Credential Guard

> **Note**: In the lab environment, Credential Guard will not run VMs because they don't meet the requirements. You can still create the GPO (Group Policy Objects) and run the tool.

### Task 1: Enable Windows Defender Credential Guard using Group Policy

1. Sign-in to **SEA-ADM1** as **Contoso\\Administrator** with the password **Pa55w.rd**.
2. Select **Start**, and then enter **Group Policy Management**.
3. Select **Group Policy Management**.
4. In the Group Policy Management Console, expand **Forest: ```Contoso.com```**, expand **Domains**, expand **```Contoso.com```**, right-click or access the context menu for the **IT** OU (Organizational Unit), and then select **Create a GPO in this domain, and Link it here**.
5. In the **New GPO** dialog box, in the **Name** text box, enter **CredentialGuard_GPO**, and then select **OK**.
6. In the **Group Policy Management** window, under **IT**, right-click or access the context menu for **CredentialGuard_GPO**, and then select **Edit**.
7. In the Group Policy Management Editor, navigate to **Computer Configuration\\Policies\\Administrative Templates\\System\\Device Guard**.
8. Select **Turn On Virtualization Based Security**, and then select the **policy setting** link.
9. Select **Enabled**.
10. In the **Select Platform Security Level** drop-down list, select **Secure Boot and DMA Protection**.
10. In the **Credential Guard Configuration** drop-down list, select **Enabled with UEFI lock**.
11. In the **Secure Launch Configuration** drop-down list, select **Enabled**, and then select **OK**.
13. Close the Group Policy Management Editor.
14. Close the Group Policy Management Console.

### Task 2: Enable Windows Defender Credential Guard using the Hypervisor-Protected Code Integrity and Windows Defender Credential Guard hardware readiness tool

1. On **SEA-ADM1**, select **Start**, and then enter **Powershell**.
2. Right-click or access the context menu for **Windows PowerShell**, and then select **Run as administrator**.
3. Enter the following command:

    ```powershell
    cd c:\\labfiles\\Mod08
    DG_Readiness_Tool.ps1 -Enable -AutoReboot
    
    ```

4. Your virtual machine will restart after the tool has completed running.
5. When the virtual machine restarts, reenter the credentials for **Contoso\\Administrator**.

## Exercise 2: Locating problematic accounts

### Task 1: Locate and reconfigure accounts with passwords that don’t expire

1. Sign in to **SEA-ADM1** as **Contoso\\Administrator** with the password **Pa55w.rd**.
2. Open Windows PowerShell.
3. Enter the following command:

    ```powershell
    Get-ADUser -Filter {Enabled -eq $true -and PasswordNeverExpires -eq $true}
    
    ```

4. Review the list of user accounts returned.
5. Enter the following command:

    ```powershell
    Get-ADUser -Filter {Enabled -eq $true -and PasswordNeverExpires -eq $true} | Set-ADUser -PasswordNeverExpires $false
    
    ```

6. Rerun the command from step 3 and notice that no users are returned.

### Task 2: Locate and disable accounts to which no sign-ins have occurred for at least 90 days

1. Enter the following commands:

    ```cmd
    $days = (Get-Date).Adddays(-90)
    Get-ADUser -Filter {LastLogonTimeStamp -lt $days -and enabled -eq $true} -Properties LastLogonTimeStamp
    
    ```

2. In the lab environment, no accounts will be returned.
3. Enter the following command:

    ```cmd
    Get-ADUser -Filter {LastLogonTimeStamp -lt $days -and enabled -eq $true} -Properties LastLogonTimeStamp | Disable-ADAccount
    
    ```

4. No results will be returned in the lab environment.

## Exercise 3: Implementing LAPS

### Task 1: Prepare OU and computer accounts for LAPS (Local Administrator Password Solution)

1. Sign in to **SEA-ADM1** as **Contoso\\Administrator** with the password **Pa55w.rd**.
2. Open Windows PowerShell.
3. Enter the following commands:

    ```powershell
    New-ADOrganizationalUnit -Name "Seattle_Servers"
    Get-ADComputer SEA-SVR1 | Move-ADObject –TargetPath "OU=Seattle_Servers,DC=Contoso,DC=com"
    
    ```

4. Enter the following command:

    ```powershell
    Msiexec /I C:\Labfiles\Mod08\LAPS.x64.msi
    
    ```

5. When the **Local Administrator Password Solution Setup Wizard** opens, select **Next**.
6. Select **I accept the terms in the License Agreement**, and then select **Next**.
7. Under **Custom Setup**, in the drop-down menu next to **Management Tools**, select **Entire feature will be installed on the local hard drive**.
8. Select **Next**, select **Install**, and then select **Finish**.

### Task 2: Prepare AD DS (Active Directory) for LAPS

1. In Windows PowerShell, enter the following commands:

    ```powershell
    Import-Module admpwd.ps
    Update-AdmPwdADSchema
    Set-AdmPwdComputerSelfPermission -Identity "Seattle_Servers"
    
    ```

2. Select **Start**, and then enter **Group Policy**.
3. Select **Group Policy Management**.
4. In the Group Policy Management Console, expand **Forest: ```Contoso.com```**, expand **Domains**, expand **```Contoso.com```**, right-click or access the context menu for the **Seattle_Servers** OU, and then select **Create a GPO in this domain, and Link it here**.
5. In the **New GPO** dialog box, in the **Name** text box, enter **LAPS_GPO**, and then select **OK**.
6. In the **Group Policy Management** window, under **Seattle_Servers**, right-click or access the context menu for **LAPS_GPO**, and then select **Edit**.
7. In the **Group Policy Management Editor** window, under **Computer Configuration**, expand the **Policies** node, expand the **Administrative Templates** node, and then select **LAPS**.
8. Select the **Enable local admin password management** policy, and then select the **policy settings** link.
9. In the **Enable local admin password management** window, select **Enabled**, and then select **OK**.
10. Select the **Password Settings** policy, and then select the **policy settings** link.
11. In the **Password Settings** policy dialog box, select **Enabled**, and then configure **Password Length** to **20**.
12. Verify that the **Password Age (Days)** is configured to **30**, and then select **OK**.
13. Close the Group Policy Management Editor.

### Task 3: Deploy LAPS client-side extension

1. Switch to **SEA-SVR1**, using **Contoso\\Administrator** with the password **Pa55w.rd**.

> **Note:** You will be prompted to change your password, due to the previous exercise. Use the new password in place of the documented password throughout the remainder of the lab.

2. Enter the following command:

    ```cmd
    Msiexec /I \\SEA-ADM1\c$\Labfiles\Mod08\LAPS.x64.msi
    
    ```

3. When the **Local Administrator Password Solution Setup Wizard** opens, select **Next**.
4. Select **I accept the terms in the License Agreement**, and then select **Next**.
5. Select **Next** again, and then select **Install**.
6. Select **Finish**.
7. Enter the following command:

    ```cmd
    gpupdate /force
    
    ```

### Task 4: Verify LAPS

1. Switch to **SEA-ADM1**.
2. Select **Start**, select **LAPS**, and then select **LAPS UI**.
3. In the **LAPS UI** dialog box, in the **ComputerName** text box, enter **SEA-SVR1**, and then select **Search**.
4. Review the **Password** and the **Password expires** values, and then select **Exit**.
5. In the Windows PowerShell window, enter the following command:

    ```powershell
    Get-ADComputer SEA-SVR1 -Properties ms-Mcs-AdmPwd
    
    ```

6. Review the password assigned to SEA-SVR1.
7. Close the gridview window.
