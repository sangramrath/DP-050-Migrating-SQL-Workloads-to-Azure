# DP 050 - Migrating SQL Workloads to Azure
# Lab 1 - Azure Architecture Considerations

**Estimated Time**: 50 minutes

**Pre-requisites**: There are no pre-requisite steps to perform this lab.

**Lab files**: The files for this lab are in the _Allfiles\Labfiles\Starter\DP-150.1_ folder.

## Lab overview

The students will use the information gained in this module to demonstrate an understanding of data platform modernization, and why and how organizations should undertake a modernization project at a high level. They will learn how to determine the cost of moving to Azure. They will also gather information about the environment that they are working on. Students will finally determine a data platform modernization strategy to use for a given scenario.

## Lab objectives
  
After completing this lab, you will be able to:

- Describe data platform modernization.
- List the stages of migration.
- Describe data migration technologies.

## Scenario
  
You have recently been hired as a senior data engineer at AdventureWorks and are working with a consultant and architects to initiate a data platform modernization project that meets the organization's technical and business requirements. You will calculate the Total Cost of Ownership (TCO) of migrating to Azure. You will ask a junior data engineer to collect information about the SQL Server that will be migrated to Azure. Then you will choose the best Azure data platform technology to migrate to for a given scenario.

## Exercise 1: Understand data platform modernization

In this lab, you will calculate the costs for a migrating databases to Azure.

Estimated Time: 15 Minutes

The main tasks for this exercise are the following:

1. Open the Microsoft Total Cost of Ownership (TCO) Calculator
1. Define a server-based workload
1. Add storage
1. Add networking
1. Adjust the currency
1. Adjust the electricity costs
1. Adjust other assumptions
1. Compare the on-premises and Azure costs

### Open the Total Cost of Ownership (TCO) Calculator

1. Open your browser and navigate to [Azure TCO Calculator](https://azure.microsoft.com/pricing/tco/calculator/).
1. Under **Define your workloads**, delete any existing workloads in the **Servers** section.

### Enter the Database workload

1. Under **Databases**,  delete any existing workloads.
1. Select **+ Add database**.
1. In the **Name** textbox, type **Accounting**.
1. In the **Source** section, choose these values:

    | Property | Value |
    | --- | --- |
    | Database | Microsoft SQL Server |
    | License | Enterprise |
    | Environment | Physical Servers |
    | Operating system | Windows |
    | Operating System License | Datacenter |
    | Servers | 1 |
    | Procs per server | 1 |
    | Core(s) per proc | 4 |
    | RAM (GB) | 64 |
    | Optimize by | CPU |
    | SQL Server 2008/2008R2 | Slide to the right |

1. In the **Destination** section, choose these values:

    | Property | Value |
    | --- | --- |
    | Service | SQL Server VM |
    | Disk type | SSD |
    | IOPS | 5000 |
    | SQL Server storage | 32 GB |
    | SQL Server Backup | 32 GB |

    > [!NOTE]
    > SSDs are the recommended disk type for production servers in Azure

### Enter the Storage and Networking workloads

1. Under **Storage**, delete any existing workloads, and then select **+ Add storage**.
1. Enter these values:

    | Property | Value |
    | --- | --- |
    | Name | Accounting Local Disks |
    | Storage Type | Local Disk/SAN |
    | Disk Type | HDD |
    | Capacity | 3 TB |
    | Backup | 1 TB |
    | Archive | 0 TB |

1. Under **Networking**, in the **Outbound bandwidth** controls, select **1 GB**.
1. At the bottom of the page, select **Next**.

### Adjust assumptions

1. In the **Adjust assumptions** section, in the **Currency** list, select your preferred currency.
1. Under **Software Assurance coverage (provides Azure Hybrid Benefit)**, slide the **Windows Server Software Assurance coverage** slider to the right.
1. Slide the **SQL Server Software Assurance coverage** slider to the right.

    > [!NOTE]
    > You can use the links in the **Software Assurance** section to learn more about the assurance that is available. There are similar links in subsequent sections.

1. Under **Geo-redundant storage (GRS)**, ensure that the toggle beside **GRS replicates your data to a secondary region that is hundreds of miles away from the primary region** is turned off.
1. Under **Virtual Machine costs**, ensure that the toggle beside **Enable this for the Calculator to not recommend B-series virtual machines** is turned off.
1. Under **Electricity costs**, in the **Price per KW hour** textbox, enter a realistic value for your location.

    > [!NOTE]
    > You can find approximate electricity prices at [Global electricity prices](https://www.statista.com/statistics/263492/electricity-prices-in-selected-countries/). These prices are in USD ($). Convert them to an approximate value in your preferred currency.

1. Under **Storage costs**, keep app the default values.
1. Under **IT labor costs**, keep all the default values.
1. Under **Other assumption**, expand each section and examine the associated costs.
1. At the bottom of the page, select **Next**.

### Investigate the 5 year report

1. On the **View report** page, note that the **Timeframe** defaults to **5 years**.
1. Scroll down to display the report, and note the estimated costs for on-premises systems compared to Azure. Make a note of this information:

    - Where is the majority of the spend for on-premises workloads?
    - Where the largest cost saving if you decide to migrate to Azure?

1. Expand each section in turn and investigate the breakdown of costs.
1. Change the region based on your location, network latency, or data sovereignty requirements, and examine how the costs compare.

### Investigate the 3 year report

1. Scroll to the top of the page and then, in the **Timeframe** textbox, select **3 years**.
1. Scroll down the report and investigate the estimated breakdown of costs for on-premises systems and Azure. Make a note of this information:

    - Where is the majority of the spend for on-Premises?
    - Where the largest cost saving if you decide to migrate to Azure?

1. Expand each section in turn and investigate the breakdown of costs

After completing this exercise, you have used the Azure TCO calculator to identify cost differences between on-premises and Azure deployments for AdventureWorks Corporation's accounting server and its associated databases.

## Exercise 2: Understand the stages of migration

In this exercise, you will examine the specifications of your existing server.

Estimated Time: 15 Minutes

The main tasks for this exercise are:

1. Identify the amount of memory used by the existing server
1. Discover the type of CPU of the existing server
1. Determine the disk configuration of the existing server

### Determine the amount of memory that is being used on the server

1. Sign in to the **LON-DEV-01** virtual machine running in the classroom environment. The username is **administrator**, and the password is **Pa55w.rd**.
1. Use a method of your choice to determine the amount of memory being used by the server.

    > **Tip**: Use System Information, an application included in all modern versions of Windows, to obtain specifications about your server or desktop device. To open System Information, select Start, and then enter System Information into the search field.

1. Make a note of the value in Notepad.

### Determine the amount of CPU that is being used on the server

1. Use a method of your choice to determine how CPU resources are being used on the server.
1. Make a note of your observations in Notepad.

### Determine the disk configuration that is being used on the server

1. Use a method of your choice to determine the disk configuration that is configured on the server.
1. Make a note of the configuration in Notepad.
1. Save the Notepad into the _Allfiles\Labfiles\Starter\DP-150.1_ folder with the name of ServerSpecs.txt

After completing this exercise, you will have collected information on the hardware specifications of the server.

## Exercise 3: Data Migration Technologies

In this exercise, you will select a data platform technology to help a customer migrate from an on-premises SQL Server to Azure.

Estimated Time: 15 Minutes

The tasks for this exercise are as follows:

1. Identify the database technology required to facilitate a migration in scenario A.
1. Identify the database technology required to facilitate a migration in scenario B.

### Identify the database technology required to facilitate a migration in scenario A

1. Read the following scenario

      The customer has an application that uses many databases currently residing in an on-premises version of Microsoft SQL Server 2008. The total database footprint is large at 12 TB, and rapidly growing by several terabytes per year. The application also has tight integration with SQL Server Reporting Services for its reporting functionality. The existing SAN storage, which contains the databases, is almost at capacity, expensive to expand, and nearing the end of its life. The application is critical to the company, with a moderate transaction rate, and any downtime would have significant business impact. Small maintenance windows are available in which to make changes to maximize the availability of the application. The high growth rate has seen more and more time being spent by DBAs and sysadmins to keep everything running.

1. From the scenario, which database platform technology would be appropriate?

### Identify the database technology required to facilitate a migration in scenario B

1. Read the following scenario

      The customer keeps departmental data in databases on a quad-core SQL Server with 16 GB of memory. The server is used as a backend to simple data access for spreadsheets and an Access form. There are six databases in total that take up 350 MB of storage. The maximum number of concurrent connections to this server is twelve.

1. From the scenario, which database platform technology would be appropriate?

After completing this exercise, you will have identified the appropriate Azure database technologies for your migration project.

## Lab Review

After approximately 45 minutes, the instructor will bring a close to this lab. The class will discuss the findings of each group.