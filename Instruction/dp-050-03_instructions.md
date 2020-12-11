# DP 050 – Migrating SQL Workloads to Azure

## Lab 3 – Migrate SQL Workloads to SQL Server in Azure Virtual Machine

**Estimated Time:** 60 minutes

**Pre-requisites:** There are no pre-requisite steps to perform in this lab.

**Lab files:** There are no lab files for this lab.

## Lab Overview

The students will initially evaluate the migration process that they will use to migrate from an on-premises SQL Server 2008 R2 instance to an SQL Server 2017 running in a virtual machine. They will then perform a migration using Data Migration Assistant to move databases. Finally, they will assess a successful migration.

## Lab Objectives

After completing this lab, you will be able to:

- Create a new virtual machine on Azure that runs SQL Server 2017.
- Create an Azure Storage Account and a file share.
- Perform a migration of SQL Server 2008 R2 databases to SQL Server on an Azure VM.

## Scenario

You are the senior database management lead of AdventureWorks and are preparing to run a data modernization project. You will prepare the necessary environment to migrate a set of databases to SQL Server on an Azure virtual machine and perform test migrations using Data Migration Assistant.

## Exercise 1: Create a new virtual machine on Azure that runs SQL Server 2017

In this exercise, you will create a new virtual machine on Azure, using the Azure Portal.

**Estimated Time:** 20 Minutes

The task for this exercise is:

1. Create a new virtual machine in the Azure Portal

### Provision a SQL Server 2017 virtual machine

> [!NOTE]
> If you are running this lab in a hosted lab environment, perform these steps inside that environment.

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**.
1. In the Marketplace search box, type **SQL Server 2017 on Windows Server 2019** and then press Enter. Under **Showing All Results** select **SQL Server 2017 on Windows Server 2019**.
1. In the **Select a plan** dropdown list, select **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2019**, and then select **Create**.
1. In the **Create a virtual machine** wizard, on the **Basics** page, enter these values, and then select **Next: Disks \>**:

    | Property | Value |
    | --- | --- |
    | Subscription | Choose your subscription |
    | Resource group | Create a new resource group called **DP-050-Training** |
    | Virtual machine name | sql2017vm |
    | Region | Select a region close to you |
    | Availability options | No infrastructure redundancy required |
    | Image | Free SQL Server License: SQL Server 2017 Developer on Windows Server 2019 - Gen1 |
    | Azure Spot instance | No |
    | Size | Standard_D2_v2 |
    | Username | sqladmin |
    | Password | Pa55w.rdPa55w.rd |
    | Confirm Password | Pa55w.rdPa55w.rd |
    | Public inbound ports | Allow selected ports |
    | Select inbound ports | RDP (3389) |
    | Would you like to use an existing Windows license? | No |
    
1. On the **Disks** page, accept the default settings, and then select **Next: Networking \>**.
1. On the **Networking** page, accept the default settings, and then select **Next: Management \>**.
1. On the **Management** page, in the **Boot diagnostics** list, select **Disable**, and then select **Next: Advanced \>**.
1. On the **Advanced** page, accept the default settings, and then select **Next: SQL Server settings \>**.
1. On the **SQL Server Settings** page, enter these values, and then select **Review + create**:

    | Property | Value |
    | --- | --- |
    | SQL connectivity | Public (Internet) |
    | Port | 1433 |
    | SQL Authentication | Enable |
    | Login name | sqladmin |
    | Password | Pa55w.rdPa55w.rd |
    | Azure Key Vault integration | Disable |

1. On the **Review + create** page, select **Create**.

    > [!NOTE]
    > This step could take about 10 minutes to complete.

1. When the deployment is complete, select **Go to resource**.
1. Locate and record the **Public IP address** for your VM. You will need this address later.
1. Next to **DNS name**, select **Configure**.
1. In the **DNS name label (optional)** textbox, type a unique DNS name and record it.

    For example: sql2017vmxxxx.centralus.cloudapp.azure.com

1. Select **Save**.

Results: After completing this exercise, you have a SQL Server 2017 instance running in an Azure virtual machine.

## Exercise 2: Create an Azure storage account and file share

In this exercise, you will create a new storage account in Azure, by using the Azure Portal.

**Estimated Time:** 15 Minutes

The main tasks for this exercise are:

1. Create an Azure storage account.
1. Create a file share in the Azure storage account.

### Create an Azure storage account

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**.
1. In the **Search the Marketplace** textbox, type **Storage account**, and then press **Enter**.
1. Under **Showing All Results** select **Storage account**, and then select **Create**.
1. In the **Create storage account** wizard, on the **Basics** page, enter these values, and then select **Review + create**:

    | Property | Value |
    | --- | --- |
    | Subscription | Select your subscription |
    | Resource group | DP-050-Training |
    | Storage account name | **dp050storagexxxx** where xxxx is a random sequence of characters |
    | Location | Select the same location as you used for the virtual machine |
    | Performance | Standard |
    | Account kind | StorageV2 (general purpose v2) |
    | Replication | Locally-redundant storage (LRS) |

1. On the **Review + create** page, select **Create**.

    > [!NOTE]
    > This deployment could take a few minutes

1. Record the storage account name you used.
1. When the deployment is complete, select **Go to resource**
1. Under **Settings**, select **Access keys**.
1. On the **Access keys** page, select **Show keys**, and then under **key1**, record the contents of the **Key** textbox.

### Create a file share

1. In the storage account page, under **File service** in the left menu, select **File shares**.
1. On the **File shares** page, select **+ File share**
1. In the **New file share** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Name | backupshare |
    | Quota | 200 GiB |

Results: You have now successful created an Azure file share which will be used as a shared access location for SQL Server database backup files. In the next exercise you will configure the SQL instances to access the shared location.

## Exercise 3: Create a connection for the SQL Server instances to connect to the Azure file share

In this exercise, you will configure the SQL Server environment to access the Azure file share.

**Estimated Time:** 10 Minutes

The main tasks for this exercise are:

1. Register the file share through SQL Management Studio by mapping a network drive.
1. Connect to the file share.

### Register the server instances in SQL Management Studio

1. Sign into the **LON-DEV-01** virtual machine running in the classroom environment. The username is **administrator**, and the password is **Pa55w.rd**.
1. Start **SQL Management Studio**, and then connect to the local instance (LONDON).
1. In SQL Management Studio Object Explorer, select **Connect** and then select **Database Engine**.
1. In the **Connect to Server** dialog, enter these values and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | Enter the fully qualified domain name or IP address of your SQL 2017 VM in Azure. For example: **sql2017vmxxx.centralus.cloudapp.azure.com** |
    | Authentication | SQL Server |
    | Login | sqladmin |
    | Password | Pa55w.rdPa55w.rd |

### Connect the SQL instances to the file shares

> [!NOTE]
> In order for SQL Server to be able to connect to a drive letter residing on a file share, you have to map the network drive by running `xp_cmdshell` in SQL Server Management Studio, so that the SQL service account can access the share. Data Migration Assistant uses the SQL service account to backup the database. For security reasons, command line access should be limited to SQL Server service accounts. By default SQL command line is disabled.

1. In SQL Management Studio, in the **Object Explorer**, right-click the **LONDON** server and then select **New Query**.
1. Enter this Transact-SQL code:

    ```sql
    EXECUTE sp_configure 'show advanced options', 1;
    RECONFIGURE;
    EXECUTE sp_configure 'xp_cmdshell' 1;
    RECONFIGURE;
    GO
    EXECUTE xp_cmdshell 'net use U: \\<storageaccountname>.file.core.windows.net\backupshare /persistent:Yes /u:Azure\<storageaccountname> <storageaccountkey>';
    EXECUTE xp_cmdshell 'dir U:';
    ```

1. In the query text, replace `<storageaccountname>` with the name of the storage account you created earlier. The name must be entered in two places.
1. Replace `<storageaccountkey>` with the primary access key you recorded for the storage account.
1. Execute the query and validate that the network drive is accessible.
1. Save the query in the Labfiles folder as **MapNetworkDrive.sql**
1. Start a new query window and disable `xp_cmdshell` by running this query:

    ```sql
    EXECUTE sp_configure 'xp_cmdshell', 0;
    RECONFIGURE;
    ```

1. Close all the queries windows and don't save any files.
1. In the **Object Explorer**, right-click the SQL Server in Azure, and then select **Connect**.
1. In the **Connect to Server** dialog, in the **Password** textbox, type **Pa55w.rdPa55w.rd** and then select **Connect**.
1. On the **File** menu, select **Open/File** and then open the **MapNetworkDrive.sql** file that you saved above.
1. In the status bar at the bottom of the query window, check that you are connected to the Azure VM.
1. To map the U: drive on the Azure VM, execute the query and validate that the network drive is accessible.
1. Start a new query window and disable `xp_cmdshell` by running this query:

    ```sql
    EXECUTE sp_configure 'xp_cmdshell', 0;
    RECONFIGURE;
    ```

1. Close SQL Server Management Studio.

## Exercise 4: Perform a Database Migration using SQL Server Data Migration Assistant

In this exercise, you will migrate the data from an on-premises SQL Server to a VM in Azure.

**Estimated Time:** 10 Minutes

The main tasks for this exercise are:

1. Migrate databases by using Data Migration Assistant.
1. Validate a successful migration of the database.

### Migrate SQL Databases using Data Migration Assistant

1. In the **LON-DEV-01** virtual machine, open **Microsoft Data Migration Assistant** and then select **+**.
1. In the **New** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Project type | Migration |
    | Project name | Migration to Azure VM |
    | Source server type | SQL Server |
    | Target server type | SQL Server on Azure Virtual Machines |

1. On the **Specify source & target** page, under **Source server details**, enter these values:

    | Property | Value |
    | --- | --- |
    | Server name | localhost |
    | Authentication type | Windows Authentication |
    | Encrypt connection | No |
    | Trust server certificate | Yes |

1. Under **Target server details**, enter these values, and then select **Next**:

    | Property | Value |
    | --- | --- |
    | Server name | Enter the IP address or DNS name of the virtual machine in Azure |
    | Authentication type | SQL Server Authentication |
    | Username | sqladmin |
    | Password | Pa55w.rdPa55w.rd |
    | Encrypt connection | Yes |
    | Trust server certificate | Yes |

1. On the **Add databases** page, deselect all the databases except for **AdventureWorks** and **AdventureWorksLT2008TR2**.
1. In the **Shared location** textbox, type **U:\\** and then select **Next**.
1. Review the **Select logins** window. There are no logins to migrate. Select **StartMigration**

    > [!NOTE]
    > All databases will be backed up to the shared network drive in the Azure Storage file share.

1. Monitor the migration process.
1. When the migration is complete, close Data Migration Assistant.

### Validate a successful migration

1. In the **LON-DEV-01** virtual machine, open **SQL Management Studio**.
1. In the **Connect to Server** dialog, in the **Server name** list, choose the IP address or DNS name of the Azure VM.
1. In the **Password** textbox, type **Pa55w.rdPa55w.rd** and then select **Connect**.
1. In Object Explorer, expand the  expand the **Databases** list.
1. Verify that the **AdventureWorks** database has been successfully migrated.
1. On the **File** menu, select **New/Query with Current Connection**.
1. Type and execute this query to validate the database compatibility level of each of the databases.

    ```sql
    SELECT name, compatibility_level FROM sys.databases
    ```

1. Alter the database compatibility level for the **Adventureworks** database using this query:

    ```sql
    ALTER DATABASE AdventureWorks
    SET COMPATIBILITY_LEVEL = 110;
    GO
    ```

1. Backup the Adventureworks database using this query:

    ```sql
    BACKUP DATABASE Adventureworks  
    TO DISK = 'U:\Adventureworks'  
       WITH FORMAT,  
          MEDIANAME = 'Adventureworks',  
          NAME = 'Full Backup of Adventureworks';  
    ```

1. Upon successful completion of the backup close **SQL Management Studio.**

> [!IMPORTANT]
> Don't delete the SQL Server VM running in Azure at this end of this lab. You will use it as the source for migration to Azure Database in Lab 4.

Results: You have now completed the successful migration of SQL Server databases to SQL Server 2017 running in an Azure VM.