# DP 050 – Migrating SQL Workloads to Azure

## Lab 4 – Migrate SQL Workloads to Azure SQL Database

**Estimated Time:** 60 minutes

**Pre-requisites:** There are no pre-requisite steps to perform in this lab.

**Lab files:** There are no lab files for this lab

## Lab Overview

In this lab you will perform a migration to Azure SQL Database. First, you will perform a schema migration and create the destination database as a pre-requisite, then you migrate from a database running in an on-premises SQL instance to Azure SQL Database. You will perform an online migration using Database Migration Service (DMS), to keep the data in synchronization between the source and the target databases until cut over to the new database.

## Lab Objectives

At the end of this lab, you will be able to:

- Create a database in Azure SQL Database using Azure Cloud Shell.
- Configure Azure Data Migration Service.
- Migrate a database schema to Azure SQL Database.
- Perform an online migration using Data Migration Service.

## Scenario

You are the senior database management lead at AdventureWorks and are preparing to run a data modernization project. You will prepare the necessary environment to migrate a set of databases to SQL Server in an Azure Virtual Machine (VM) and perform test migrations using Data Migration Assistant.

## Exercise 1: Create a database in Azure SQL Database using Azure Cloud Shell

In this exercise you’ll be using Azure Cloud Shell to:

- Create a new resource group.
- Create a new Azure SQL Database server instance.
- Configure the Azure SQL Database server firewall.
- Create a new general-purpose database.

**Estimated Time:** 20 minutes

There are many options in Azure to automate the installation, management, and deployment of services. One of the options could be installing the Azure CLI command line tool, a lightweight cross-platform command-line tool. Another option would be to automate and script using Azure Cloud Shell.

Azure Cloud Shell is an interactive shell environment hosted in Azure and managed through your browser. Cloud Shell lets you use either Bash or PowerShell to work with Azure services. You can use the Cloud Shell pre-installed commands to run the code in this article without having to install anything on your local environment.

### Task 1: Log on and configure Azure Cloud Shell

> [!NOTE]
> This task can be completed entirely in the Azure Cloud Shell.

1. Go to the [Azure Shell](https://shell.azure.com)
1. Log on to your Azure Subscription with the credentials used for this training.
1. Select **Bash** as the scripting environment.

### Task 2: Create a new storage account and share for Azure Cloud Shell

1. When prompted that you have no storage account created for Azure Cloud Shell, select **Show Advanced Settings**, enter these values,and then select **Create storage**:

    | Property | Value |
    | --- | --- |
    | Subscription | Select the Azure subscription used for this lab |
    | Cloud Shell Region | Select an available Cloud Shell region close to your location |
    | Resource group | Create a new resource group called **dp050lab4rg** |
    | Storage account | Create a new storage account called **dp050sa&lt;youridentifier&gt;** where **&lt;youridentifier&gt;** is a unique string |
    | File share | Create a new file share called **dp050share&lt;youridentifier&gt;** where **&lt;youridentifier&gt;** is a unique name |

1. When the storage account has been created, the Azure Cloud Shell opens.

### Task 3: Create the Azure SQL Database server instance and database

1. Select the **{}** icon to open the code editor.
1. Paste this script into the code editor:

    ```bash
    #bash script to create a new sql db server instance and sql db  
    
    #Disclaimer: this script is a sample script on how to create an Azure database but uses least restrictive firewall settings for lab purposes. Do not use this script  
    
    #defining a name for the resource group
    resourcegroup=dp-050-labresourcegroup
    
    #edit the variable below to provide a unique server name
    servername=dp-050-servername
    
    #edit the variable below to provide the location – azure locations can be listed by typing az account list-locations -o table in the shell command interface
    location=eastus
    
    adminuser=sqladmin
    password=Pa55w.rdPa55w.rd
    firewallrule=dp-050-access
    
    #edit the script to provide a unique database name
    labdatabase=dp-050-Adventureworksxxx
    
    #creates a resource group
    az group create --name $resourcegroup --location $location
    
    #creates a sql db servername
    az sql server create --name $servername --resource-group $resourcegroup --admin-user $adminuser --admin-password $password --location $location
    
    #shows current firewall list
    az sql server firewall-rule list --resource-group $resourcegroup --server $servername
    
    #creates a server-based firewall – note – you should restrict the start/end ip range based on your environment
    az sql server firewall-rule create --resource-group $resourcegroup --server $servername --name $firewallrule --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
    
    #creates a general-purpose SQL database
    az sql db create --name $labdatabase --resource-group $resourcegroup --server $servername -e GeneralPurpose
    ```

1. In the script, change the value `dp-050-servername` to a unique servername, such as `dp-050-serverxxx`. Don't use upper case in this name.
1. In the script, change the value `eastus` to an Azure location near you.
1. In the script, change the value `dp-050-Adventureworksxxx` to a unique database name.
1. To save the script, press <kbd>CTRL + S</kbd>, type **CreateSQLDBandServer.sh**, and then select **Save**.
1. To exit the code editor, press <kbd>CTRL + Q</kbd>.
1. To execute the script, type **sh CreateSQLDBandServer.sh** and then press <kbd>Enter</kbd>.

    When the script has completed successfully, you can validate that the resource group, server, and database have been created in your Azure account subscription.

## Exercise 2: Migrate a database to Azure SQL Database

In this exercise, you’ll migrate the database schema of a database in a SQL Server instance running on-premises and load the schema into the SQL Database created in the previous exercise. The source SQL Server instance for the database schema runs on the VM LON-DEV-01.

At the end of this lab, you will be able to:

- Create a new migration project using Data Migration Assistant.
- Migrate a database a schema.

**Estimated Time:** 20 minutes

> [!NOTE]
> This exercise is completed using Data Migration Assistant running on the LON-DEV-01 VM.

### Task 1: Create a Migration Project using Data Migration Assistant

1. Sign into the **LON-DEV-01** virtual machine running in the classroom environment. The username is **administrator**, and the password is **Pa55w.rd**.
1. To create a new data migration project, open **Microsoft Data Migration Assistant**, and then **+**.
1. In the **New** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Project type | Migration |
    | Project name | SQLSchemaMigration |
    | Source server type | SQL Server |
    | Target server type | Azure SQL Database |
    | Migration scope | Schema only |

### Task 2: Perform the schema migration

1. On the **Select source** page, under **Connect to source server**, enter these values, and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | localhost |
    | Authentication type | Windows Authentication |
    | Encrypt connection | No |
    | Trust server certificate | Yes |

1. In the list of databases, select **AdventureworksLT2008R2** and deselect the **Assess database before Migration** checkbox.
1. Select **Next**.
1. On the **Select target** page,  under **Connect to target server**, enter these values, and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | Type the **servername** of the server you created in the previous exercise. List the server by its **full qualified name**, for example: dp-050-servername.database.windows.net |
    | Authentication type | SQL Server Authentication |
    | Username | sqladmin |
    | Password | Pas55w.rdPa55w.rd |
    | Encrypt connection | No |
    | Trust server certificate | Yes |

1. In the list of databases, select the target database you created in the previous exercise, and then select **Next**
1. Review all the schema objects, and then select **Generate SQL script**

    The Data Migration Assistant window will look similar to:

    ![Data Migration Assistant Window](/images/dbmigrate.png)

1. Select **Deploy Schema**.
1. When the deployment is complete, close Data Migration Assistant.

## Exercise 3: Migrate on-premises databases to Azure SQL Database

In this exercise you’ll perform an online migration of a database using the Azure Data Migration Service.

At the end of this exercise, you will be able to:

- Configure the source instance for replication and make sure the pre-requisites for migration are met.
- Perform a live migration using Azure Data Migration Service.

**Estimated Time:** 20 minutes

### Task 1: Configure SQL Server as a replication distributor

You will complete this task using SQL Management Studio on the SQL 2008 R2 Virtual Machine but connected to the **SQL Server 2017 instance.**

> [!NOTE]
> Perform these tasks on LON-DEV-01 while connected to the SQL Server 2017 instance in Azure, to avoid having to configure VPN access between the hosted SQL 2008 R2 VM and Azure. In a non-lab environment, you would typically configure VPN or ExpressRoute.

1. In SQL Management Studio Object Explorer, connect to the SQL Server 2017 instance you created in Lab 3.

    If the SQL Server 2017 instance was not registered, select **Connect/Database Engine**, and then use these values:

    | Property | Value |
    | --- | --- |
    | Server name | The IP address or fully qualified domain name of your SQL 2017 VM, for example sql2017vmxxxx.centralus.cloudapp.azure.com |
    | Authentication | SQL Server Authentication |
    | Login | sqladmin |
    | Password | Pa55w.rdPa55w.rd |

1. In the **Object Explorer**, right-click **Replication**, and then select **Configure Distribution**.
1. In the **Configure Distribution Wizard**, accept the default settings on each of the Configure Distribution pages. If the wizard prompts you to **start the SQL Server Agent Automatically**, select **Yes**.
1. On the **Complete the wizard** page, select **Finish**
1. If the **SQL Server Agent Fails to start**, start the agent manually by right-clicking on **SQL Server Agent in SQL Management Studio | Object Explorer | SQL Server Agent** and Start the service
1. To set the database recovery model for the **AdventureworksLT2008R2** database to FULL, execute this query in a new query window:

    ```sql
    ALTER DATABASE AdventureworksLT2008R2 SET RECOVERY FULL WITH NO_WAIT
    ```

1. Perform a FULL Backup of the database by executing the following query  

    ```sql
    BACKUP DATABASE AdventureworksLT2008R2 TO DISK = 'd:\awlt2008r2backup.bak'
    ```

1. As the backup completes, close **SQL Management Studio**.

### Task 2: Perform an online migration using Azure Database Migration Service

In this task you will configure the Azure Database Migration Service to enable a live migration between the database running on a VM and Azure SQL Database.

> [!NOTE]
> Complete all these tasks in the Azure Portal.

1. In the Azure Portal, select **Create a resource**.
1. In the **Search the Marketplace** textbox, type **Azure Database Migration Service** and then press <kbd>Enter</kbd>.
1. Select **Create**.
1. In the **Create Migration Service** wizard, on the **Basics** page, enter these values, and then select **Next: Networking \>\>**:

    | Property | Value |
    | --- | --- |
    | Subscription | Select your subscription. |
    | Resource group | dp050lab4rg |
    | Migration service name | **dp050dmsxxx** where **xxx** is a unique value. |
    | Location | Select a location near you. |
    | Service mode | Azure |
    | Pricing tier | Premium |

1. On the **Networking** page, in the **Virtual network name** textbox, type **OnPremGateway**, and then select **Review + create**.
1. On **Review + create** page, select **Create**.

    > [!NOTE]
    > This deployment can take up to 10 minutes.

1. When the deployment is complete, select **Go to resource**.
1. Select **+ New Migration Project**.
1. In the **New migration project** page, enter these values, and then select **Create and run activity**:

    | Property | Value |
    | --- | --- |
    | Project name | DP050OnlineMigration |
    | Source server type | SQL Server |
    | Target server type | Azure SQL Database |
    | Choose type of activity | Online data migration |

1. In the **SQL Server to Azure SQL Database Online Migration Wizard**, on the **Select source** page, enter these values, and then select **Next: Select target \>\>**:

    | Property | Value |
    | --- | --- |
    | Source SQL Server instance name | Fully qualified domain name of your SQL 7 VM in Azure |
    | Authentication type | SQL Authentication |
    | User Name | sqladmin |
    | Password | Pa55w.rdPa55w.rd |
    | Encrypt connection | Deselected |
    | Trust server certificate | Selected |

1. On the **Select target** page, enter these values, and then select **Map to target databases \>\>**:

    | Property | Value |
    | --- | --- |
    | Target server name | Fully qualified domain of the Azure SQL Database server |
    | Authentication type | SQL Authentication |
    | User name | sqladmin |
    | Password | Pa55w.rdPa55w.rd |
    | Encrypt connection | Deselected |
    | Trust server certificate | Selected |

1. On the **Map to target databases** page, select the database for which you did the schema migration, select the target database, and then select **Configure migration settings \>\>**.
1. On the **Configure migration settings** page, examine the notes for each table, and then select **Summary \>\>**.
1. Name the **Activity name** textbox, type **dp050lab4activity**, and then select **Run Migration**.

    > [!NOTE]
    In a production environment, you would increase the database tier of the SQL database to perform a faster load of the data.

1. During the migration process, select **Refresh** occasionally and monitor the **STATUS** column, until it displays **Ready to cutover**.

    > [!NOTE]
    > During the migration, any changes made to data in the source database will be synchronized to the target database.

    If you have time, you can insert some records in the **ProductCategory** table in the source database using this SQL command. Ensure that you execute this command on the source VM:

    ```sql
    USE [AdventureWorksLT2008R2]
    GO
    
    INSERT INTO [Sales LT].[ProductCategory]
     ([ParentProductCategoryID]
     ,[Name]
     ,[ModifiedDate])
    VALUES
     (1, ' Myproduct', getdate())
    ```

1. When the data migration is complete, select the database and then select **Start Cutover**.
1. Check **Confirm**and then select **Apply**.

Results: You have now successfully completed an online migration to Azure SQL Database.