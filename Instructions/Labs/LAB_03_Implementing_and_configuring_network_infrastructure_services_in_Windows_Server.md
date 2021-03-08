---
lab:
    title: 'Lab: Implementing and configuring network infrastructure services in Windows Server'
    module: 'Module 3: Network Infrastructure services in Windows Server'
---

# Lab: Implementing and configuring network infrastructure services in Windows Server

## Scenario

Contoso, Ltd. is a large organization with complex requirements for network services. To help meet these requirements, you will deploy and configure DHCP so that it is highly available to ensure service availability. You will also set up DNS so that Trey Research, a department within Contoso, can have its own DNS server in the testing area. Finally, you will provide remote access to Windows Admin Center and secure it with Web Application Proxy.

## Objectives

After completing this lab, you'll be able to:

- Deploy and configure DHCP
- Deploy and configure DNS

## Estimated time: 30 minutes

## Lab Setup

Virtual machines:

- **SEA-DC1**
- **SEA-ADM1**
- **SEA-SVR1**
- **SEA-CL1**

User name: **Contoso\Administrator**

Password: **Pa55w.rd**

For this lab, you'll use the available virtual machine environment. Before you begin the lab, complete the following steps:
1. Open **SEA-DC1** and sign in as **Contoso\Administrator** with the password **Pa55w.rd**.
1. Repeat step 1 for **SEA-ADM1**, **SEA-SVR1**, and **SEA-CL1**.

## Exercise 1: Deploying and configuring DHCP

### Scenario

The Trey Research subdivision of Contoso, Ltd. has a separate office with only about 50 users. They have been manually configuring IP addresses on all of their computers and want to begin using DHCP instead. You will install DHCP on **SEA-SVR1** with a scope for the Trey Research site. Additionally, you will configure DHCP Failover by using the new DHCP server for high availability with **SEA-DC1**.

The main tasks for this exercise are as follows:

1. Install the DHCP role.
1. Authorize the DHCP server.
1. Create a scope.
1. Configure DHCP Failover.
1. Verify DHCP functionality.

### Task 1: Install the DHCP role

1. On **SEA-ADM1**, open **Microsoft Edge**, and then sign in to **Windows Admin Center**.
1. In **Windows Admin Center**, connect to **SEA-SVR1**.
1. From **Roles & features**, install the DHCP role.
1. From **DHCP**, install the **DHCP PowerShell** tools. If **DHCP** is not available in the **Tools pane** for **SEA-SVR1**, close **Microsoft Edge** and sign in to **Windows Admin Center** again.

### Task 2: Authorize the DHCP server

1. On **SEA-ADM1**, open **Server Manager**.
1. In **Server Manager**, open **Notifications**, open **Complete DHCP configuration**, and then complete the **DHCP Post-Install Configuration Wizard** by using the default options.

### Task 3: Create a scope

1. On **SEA-ADM1**, in **Windows Admin Center**, while connected to **SEA-SVR1**, use **DHCP** to create a new scope with the following options:
    - Protocol: **IPv4**
    - Name: **ContosoClients**
    - Starting IP address: **10.100.150.50**
    - Ending IP address: **10.100.150.254**
    - DHCP client subnet mask: **255.255.255.0**
    - Router: **10.100.150.1**
    - Lease duration: **4 days**
1. In **Server Manager**, open the **DHCP management console**.
1. In the **DHCP management console**, add all authorized servers.
1. On the DHCP server **172.16.10.12**, in the **ContosoClients** scope, add the scope option **006 DNS Servers** with the value **172.16.10.10**.

### Task 4: Configure DHCP Failover

1. On **SEA-ADM1**, in the **DHCP management console**, from the **IPv4** node, configure failover with **SEA-DC1** by using the following information for the failover relationship:
    - Relationship Name: **SEA-SVR1 to SEA-DC1**
    - Maximum Client Lead Time: **1 hour**
    - Mode: **Hot standby**
    - Role of Partner Server: **Standby**
    - Addresses reserved for standby server: **5%**
    - State Switchover Interval: **Disabled**
    - Enable Message Authentication: **Enabled**
    - Shared Secret: **DHCP-Failover**
1. Verify that **SEA-SVR1** only has one scope.
1. Verify that **SEA-DC1** has two scopes.
1. Under **SEA-DC1**, for the **Contoso** scope, configure failover with **172.16.10.12**, and reuse the existing failover relationship.
1. Verify that both scopes now appear on **SEA-SVR1**.

### Task 5: Verify DHCP functionality

1. On **SEA-CL1**, configure the network connection to obtain an IP address and DNS server addresses automatically.
1. Examine the configuration status of the network connection to verify that the DHCP lease was obtained from **SEA-SVR2 (172.16.10.12)**.
1. Disable the Ethernet network connection.
1. On **SEA-ADM1**, in the **DHCP management console**, verify that both DHCP servers list the lease for **SEA-CL1** in the **Contoso** scope.
1. Stop the **DHCP** service on **SEA-SVR2 (172.16.10.12)**.
1. On **SEA-CL1**, enable the Ethernet network connection, and then verify that the same DHCP lease is obtained from **SEA-DC1 (172.16.10.10)**.

## Exercise 2: Deploying and configuring DNS

### Scenario

The staff who work at the Trey Research location within Contoso need to have their own DNS server to create records in their test environment. However, their test environment still needs to be able to resolve internet DNS names and resource records for Contoso. To meet these needs, you are configuring forwarding to your internet service provider (ISP) and creating a conditional forwarder for ```contoso.com``` to **SEA-DC1**. There is also a test application that needs a different IP address resolution based on user location. You are using DNS policies to configure ```testapp.treyresearch.net``` to resolve differently for users at the head office.

The main tasks for this exercise are as follows:

1. Install the DNS role.
1. Create a DNS zone.
1. Configure forwarding.
1. Configure conditional forwarding.
1. Configure DNS policies.
1. Verify DNS policy functionality.

### Task 1: Install the DNS role

1. On **SEA-ADM1**, open **Microsoft Edge** and sign in to **Windows Admin Center**.
1. In **Windows Admin Center**, connect to **SEA-SVR1**.
1. From **Roles & features**, install the DNS role.
1. From **DNS**, install the **DNS PowerShell** tools. If **DNS** is not available in the **Tools pane** for **SEA-SVR1**, close **Microsoft Edge** and sign in to **Windows Admin Center** again.

### Task 2: Create a DNS zone

1. On **SEA-ADM1**, in Windows Admin Center, create a new DNS zone with the following settings:
    - Zone type: **Primary**
    - Zone name: **```TreyResearch.net```**
    - Zone file: **Create a new file**
    - Zone file name: **```TreyResearch.net.dns```**
    - Dynamic update: **Do not allow dynamic update**
1. Create a new DNS record in the **```TreyResearch.net```** zone with the following settings:
    - DNS record type: **Host (A)**
    - Record name: **TestApp**
    - IP address: **172.30.99.234**
    - Time to live: **600**
1. At a Windows PowerShell prompt, run the following command to verify that the new record resolves properly:

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```

### Task 3: Configure forwarding

1. On **SEA-ADM1**, use **Server Manager** to open the **DNS Manager console**.
1. In **DNS Manager**, connect to **SEA-SVR1**.
1. In the properties of **SEA-SVR1**, on the **Forwarders** tab, configure **131.107.0.100** as a forwarder.

### Task 4: Configure conditional forwarding

1. On **SEA-ADM1**, in **DNS Manager** for **SEA-SVR1**, create a new conditional forwarder for **```Contoso.com```** that directs requests to **172.16.10.10**.
1. Open a Windows PowerShell prompt and run the following command to verify that the conditional forwarder is working:

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name sea-dc1.contoso.com
    ```

### Task 5: Configure DNS policies

1. On **SEA-ADM1**, in **Windows Admin Center**, while connected to **SEA-SVR1**, use **PowerShell** to sign in remotely.
1. At the **Windows PowerShell** prompt, run the following command to create a head office subnet:

    ```powershell
    Add-DnsServerClientSubnet -Name "HeadOfficeSubnet" -IPv4Subnet "172.16.10.0/24"
    ```

1. Run the following command to create a zone scope for head office:

    ```powershell
    Add-DnsServerZoneScope -ZoneName "TreyResearch.net" -Name "HeadOfficeScope"
    ```

1. Run the following command to create a new resource record for the head office scope:

    ```powershell
    Add-DnsServerResourceRecord -ZoneName "TreyResearch.net" -A -Name "testapp" -IPv4Address "172.30.99.100" -ZoneScope "HeadOfficeScope"
    ```

1. Run the following command to create a new policy that links the head office subnet and the zone scope:

    ```powershell
    Add-DnsServerQueryResolutionPolicy -Name "HeadOfficePolicy" -Action ALLOW -ClientSubnet "eq,HeadOfficeSubnet" -ZoneScope "HeadOfficeScope,1" -ZoneName "TreyResearch.net"
    ```

### Task 6: Verify DNS policy functionality

1. On **SEA-CL1**, open a Windows PowerShell prompt, enter **ipconfig**, and then select Enter to verify that **SEA-CL1** is on the **HeadOffice subnet (172.16.10.0)**.
1. At the Windows PowerShell prompt, run the following command to test the DNS policy:

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```

1. Verify that **testapp** resolved to **172.30.99.100** as configured in **HeadOfficePolicy**.
1. Update **SEA-CL1** to use the following IPv4 configuration:
    - IP Address: **172.16.11.100**
    - Subnet mask: **255.255.0.0**
    - Default gateway: **172.16.10.1**
    - Preferred DNS server: **172.16.10.10**
1. At the Windows PowerShell prompt, run the following command to test the DNS policy:

    ```powershell
    Resolve-DnsName -Server sea-svr1.contoso.com -Name testapp.treyresearch.net
    ```

1. Verify that **testapp** resolved to **172.30.99.234**.

> **Note:** When the client is on the HeadOffice subnet (172.16.10.0/24), the record ```testapp.treyresearch.net``` resolves to 172.30.99.100. When the client is moved off of the HeadOffice subnet, ```testapp.treyresearch.net``` resolves to 172.30.99.234.
