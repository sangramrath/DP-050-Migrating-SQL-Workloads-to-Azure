# DP 050 - Migrating SQL workloads to Azure

# Lab 2 - Choose the right tools for data migration

**Estimated Time**: 25 minutes

**Pre-requisites**: There are no pre-requisite steps to perform this lab.

**Lab files**: There are no lab files for this lab

## Lab overview

The students will use the Data Migration Assistant in the prescribed data platform modernization stages to investigate their environment in an automated way. They will also identify any premigration compatibility issues and define a plan on how to address those issues before performing a migration of an on-premises server. Finally, they will assess how a workload will perform on a target version of Azure SQL Database.

## Lab objectives
  
After completing this lab, you will be able to:

1. Identify migration candidates using Data Migration Assistant

## Scenario
  
You are the senior data engineer of AdventureWorks and are preparing for a data modernization project. You will begin by performing an inventory of the SQL Servers that exist in your environment using a tool. You will then perform an assessment of the server to identify any issues that need to be resolved prior to any modernization work that will be undertaken. One of your on-premises SQL Servers is used to process sales for the business, and you want to ensure that your new Azure services can support your workloads after they are migrated.

## Exercise 1: Identify migration candidates using Data Migration Assistant
  
In this exercise, you will use the Data Migration Assistant to look for SQL Server feature parity and compatibility issues between SQL Server and Azure SQL Database.

Estimated Time: 20 Minutes
  
The main tasks for this exercise are:

1. Install the Data Migration Assistant (DMA) on Windows.
1. Use the Data Migration Assistant.

### Install the Data Migration Assistant (DMA) on Windows

> [!NOTE]
> If Data Migration Assistant is already installed, you can skip this task.

1. Sign in to the **LON-DEV-01** virtual machine running in the classroom environment. The username is **administrator**, and the password is **Pa55w.rd**.
1. Open up your web browser, and go to the download page for [Microsoft Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)
1. Confirm that your environment supports the software by checking the requirements list.
1. To download **DataMigrationAssistant.msi**, select **Download**, and then select **Run**.
1. On the **Welcome** screen, select **Next**.
1. Select **I accept the terms in the License Agreement**, and then select **Next**.
1. Read the **Privacy Statement**, and then select **Install**.
1. If prompted, allow UAC control for this application.
1. To complete install, select **Finish**.

### Use the Data Migration Assistant to prepare for migration from SQL Server to Azure SQL Database

1. Select the **Start** button, type **Data Migration**, and then select **Microsoft Data Migration Assistant**.
1. To create a new project, on the left, select **+**.
1. On the **New** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Project type | Assessment |
    | Project name | Migration Assessment SQL DB |
    | Assessment type | Database Engine |
    | Source server type | SQL Server |
    | Target server type | Azure SQL Database |

1. On the **Options** page, leave the default values, and then select **Next**.
1. On the **Connect to a server** page, enter these values, and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | localhost |
    | Authentication type | Windows Authentication |
    | Encrypt connection | Deselected |
    | Trust server certificate | Selected |

1. On the **Add sources** page, select the **AdventureWorks** database, and then select **Add**.
1. On the **Select sources** page, select **Start Assessment**.
1. Review the SQL Server feature parity and compatibility issues found. Note how many feature parity issue there are.

### Use the Data Migration Assistant to prepare for migration from SQL Server to Azure SQL Database Managed Instance

1. To create a new project, on the left, select **+**.
1. On the **New** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Project type | Assessment |
    | Project name | Migration Assessment SQL DB MI |
    | Assessment type | Database Engine |
    | Source server type | SQL Server |
    | Target server type | Azure SQL Database Managed Instance |

1. On the **Options** page, leave the default values, and then select **Next**.
1. On the **Connect to a server** page, enter these values, and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | localhost |
    | Authentication type | Windows Authentication |
    | Encrypt connection | Deselected |
    | Trust server certificate | Selected |

1. On the **Add sources** page, select the **AdventureWorks** database, and then select **Add**.
1. On the **Select sources** page, select **Start Assessment**.
1. Review the SQL Server feature parity and compatibility issues found. Note how many feature parity issue there are.

### Use the Data Migration Assistant to prepare for migration from SQL Server to SQL Server on an Azure Virtual Machine

1. To create a new project, on the left, select **+**.
1. On the **New** page, enter these values, and then select **Create**:

    | Property | Value |
    | --- | --- |
    | Project type | Assessment |
    | Project name | Migration Assessment Azure VM |
    | Assessment type | Database Engine |
    | Source server type | SQL Server |
    | Target server type | SQL Server on Azure Virtual Machines |

1. On the **Options** page, leave the default values, and then select **Next**.
1. On the **Connect to a server** page, enter these values, and then select **Connect**:

    | Property | Value |
    | --- | --- |
    | Server name | localhost |
    | Authentication type | Windows Authentication |
    | Encrypt connection | Deselected |
    | Trust server certificate | Selected |

1. On the **Add sources** page, select the **AdventureWorks** database, and then select **Add**.
1. On the **Select sources** page, select **Start Assessment**.
1. Review the SQL Server feature parity and compatibility issues found. Note how many feature parity issue there are.

After completing this exercise, you have collected information about the SQL Server feature parity and compatibility issues found between the instance of SQL Server on premises and various SQL Server hosting options in Azure.

## Lab Review

After approximately 20 minutes, the instructor will bring a close to this lab. The class will discuss the findings of each group.