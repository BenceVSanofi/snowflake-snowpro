<!-- omit in toc -->
# Snowflake SnowPro Core Certification

This document contains notes and study materials to help you prepare for the **Snowflake SnowPro Core Certification Exam**. It covers key concepts, features, and architecture of Snowflake, as well as practical topics like query optimization, data loading, and performance tuning. The content is designed to serve as a reference for understanding Snowflake’s core functionality.

Snowflake is a cloud-native **data platform** offered as a service (SaaS). It provides key services like **storage**, **compute**, and **management** and runs on the three major cloud providers: AWS, GCP, and Azure. Snowflake’s unique architecture allows it to handle diverse workloads such as data warehousing, data lakes, data engineering, and data science.

<!-- omit in toc -->
## Table of Contents

- [1. Introduction](#1-introduction)
- [2. Features and Architecture](#2-features-and-architecture)
- [3. High-Level Overview](#3-high-level-overview)
- [4. Multi-Cluster Shared Data Architecture](#4-multi-cluster-shared-data-architecture)
  - [4.1. Storage Layer](#41-storage-layer)
  - [4.2. Compute Layer (Query Processing)](#42-compute-layer-query-processing)
  - [4.3. Services Layer](#43-services-layer)
- [5. Editions and Features](#5-editions-and-features)
- [6. Object Model](#6-object-model)
  - [6.1. Organization](#61-organization)
  - [6.2. Account](#62-account)
  - [6.3. Database and Schema](#63-database-and-schema)
- [7. Table and View Types](#7-table-and-view-types)
  - [7.1. Table Types](#71-table-types)
  - [7.2. View Types](#72-view-types)
- [8. User-Defined Functions (UDFs)](#8-user-defined-functions-udfs)
  - [8.1. UDFs in Other Languages](#81-udfs-in-other-languages)
  - [8.2. External Functions](#82-external-functions)
- [9. Stored Procedures](#9-stored-procedures)
  - [9.1. Stored Procedures in Snowflake](#91-stored-procedures-in-snowflake)
  - [9.2. UDF vs Stored Procedure](#92-udf-vs-stored-procedure)
- [10. Sequences](#10-sequences)
- [11. Tasks and Streams](#11-tasks-and-streams)
  - [11.1. Tasks](#111-tasks)
  - [11.2. Streams](#112-streams)
  - [11.3. Using Tasks with Streams](#113-using-tasks-with-streams)
- [12. Billing Overview](#12-billing-overview)
  - [12.1. Compute Billing (Billed in Snowflake Credits)](#121-compute-billing-billed-in-snowflake-credits)
  - [12.2. Data Storage and Transfer Billing (Billed by the Cloud Provider)](#122-data-storage-and-transfer-billing-billed-by-the-cloud-provider)
  - [12.3. Key Differences: Cloud Services vs. Serverless Services](#123-key-differences-cloud-services-vs-serverless-services)
- [13. Connectivity](#13-connectivity)
  - [13.1. SnowSQL](#131-snowsql)
  - [13.2. Connectors and Drivers](#132-connectors-and-drivers)
  - [13.3. Snowflake Scripting](#133-snowflake-scripting)
  - [13.4. Snowpark](#134-snowpark)
- [14. \[SKIP\] Account Access and Security](#14-skip-account-access-and-security)
- [15. Performance Concepts: Virtual Warehouses](#15-performance-concepts-virtual-warehouses)
  - [15.1. Overview](#151-overview)
  - [15.2. Warehouse State and Properties](#152-warehouse-state-and-properties)
    - [15.2.1. Managing Warehouse States](#1521-managing-warehouse-states)
    - [15.2.2. Summary of Key Warehouse Parameters](#1522-summary-of-key-warehouse-parameters)
  - [15.3. Warehouse Size and Billing](#153-warehouse-size-and-billing)
  - [15.4. Resource Monitors](#154-resource-monitors)
    - [15.4.1. Best Practices for Warehouse Size and Resource Monitors](#1541-best-practices-for-warehouse-size-and-resource-monitors)
  - [15.5. Scaling Up and Out: Multi-Cluster Warehouses](#155-scaling-up-and-out-multi-cluster-warehouses)
    - [15.5.1. Scaling Up a Virtual Warehouse](#1551-scaling-up-a-virtual-warehouse)
    - [15.5.2. Scaling Out: Multi-Cluster Warehouses](#1552-scaling-out-multi-cluster-warehouses)
    - [15.5.3. Scaling Policies](#1553-scaling-policies)
    - [15.5.4. Credit Consumption in Multi-Cluster Warehouses](#1554-credit-consumption-in-multi-cluster-warehouses)
    - [15.5.5. Concurrency Behavior Properties](#1555-concurrency-behavior-properties)
    - [15.5.6. Examples of Multi-Cluster Warehouse Configuration](#1556-examples-of-multi-cluster-warehouse-configuration)
  - [15.6. Query Acceleration Service](#156-query-acceleration-service)
    - [15.6.1. Query Acceleration Eligibility](#1561-query-acceleration-eligibility)
    - [15.6.2. Verifying Query Acceleration Eligibility](#1562-verifying-query-acceleration-eligibility)
    - [15.6.3. Example Usage](#1563-example-usage)
- [16. Performance Concepts: Query Optimization](#16-performance-concepts-query-optimization)
  - [16.1. Analytics Tools](#161-analytics-tools)
  - [16.2. SQL Tuning](#162-sql-tuning)
  - [16.3. Caching](#163-caching)
  - [16.4. Materialized Views](#164-materialized-views)
  - [16.5. Clustering and Pruning](#165-clustering-and-pruning)
  - [16.6. Search Optimization Service](#166-search-optimization-service)
  - [16.7. Summary of Optimization Techniques](#167-summary-of-optimization-techniques)

## 1. Introduction

This document contains notes and study material for the **SnowPro Core Certification** course available on [Udemy](https://www.udemy.com/share/107srK3@7oBXnZzILRddmCPrD6_gfahyAsy1Vckxeh8lCT-OxVIOmA4F1RrOlQ40YMwFxm9LiA==/). The official Snowflake documentation is available at [Snowflake Docs](https://docs.snowflake.com/en/).

These notes were prepared as part of my study for the *24C21 Snowflake Performance Automation and Tuning* course, available at [Snowflake Performance Automation and Tuning](https://www.snowflake.com/wp-content/uploads/2022/06/Performance-Automation-and-Tuning-3-Day.pdf).

## 2. Features and Architecture

Snowflake (SF) is a SaaS platform that provides services such as storage, compute, and management across AWS, GCP, and Azure. **25% to 30%** of the certification exam will be on this topic. It consists of:

- High-level overview
- Multi-cluster shared data architecture
- Storage, compute, and services overview
- Editions and features
- Object model
- Account billing
- Connectivity

## 3. High-Level Overview

Snowflake is a cloud-native data platform offered as a service. Calling Snowflake a data platform (rather than a data warehouse) points out that Snowflake has features and capabilities beyond those of a traditional data warehouse. It supports additional workloads like data science and features like native processing of semi-structured data similar to a data lake. Here are some features:

1. **Data Warehouse**
   - Structured and relational data
   - ANSI standard SQL
   - ACID-compliant transactions
   - Data stored in databases, schemas, tables, and columns
2. **Data Lake**
   - Scalable storage and compute
   - Schema does not need to be defined upfront
   - Native processing of semi-structured data formats
3. **Data Engineering**
   - `COPY INTO` and Snowpipe
   - Separate compute clusters
   - Tasks and streams
   - All data encrypted at rest and in transit
4. **Data Science**
   - Removes data management roadblocks with centralized storage
   - Partner ecosystem includes data science tooling:
     - Amazon SageMaker
     - DataRobot
     - Dataiku
5. **Data Sharing**
   - Secure data sharing
   - Data marketplace
   - Data exchange
   - BI with the Snowflake partner ecosystem tools
6. **Data Applications**
   - Connectors and drivers
   - UDFs and stored procedures
   - External UDFs
   - Preview features such as Snowpark

Snowflake is a cloud-native solution, i.e., Snowflake's software is purpose-built for the cloud. The query engine, the storage file format, the metadata store, and the architecture generally were designed with cloud infrastructure in mind. All Snowflake infrastructure runs on the cloud in either AWS, GCP, or Azure. Snowflake makes use of the cloud's elasticity, scalability, high availability, cost-efficiency, and durability.

As a SaaS platform, Snowflake requires no management of hardware. Users do not perform any manual updates; instead, Snowflake applies transparent updates and patches with no visible downtime. You pay based on usage in a subscription payment model. Access is done via the web UI, and there are connectors and drivers for programmatic access. Optimizations are performed automatically as part of the loading process by Snowflake.

## 4. Multi-Cluster Shared Data Architecture

There are basically two ways to arrange hardware and networks into distributed computing architectures:

1. **Shared-disk**: First move away from single-node architecture. It works by scaling out nodes in a compute cluster but keeping storage in a single location. All data is accessible to all cluster nodes. Any machine can read or write data.
   - **Advantages**:
     - Relatively simple to manage
     - Single source of truth
   - **Disadvantages**:
     - Single point of failure
     - Bandwidth and network latency
     - Limited scalability

2. **Shared-nothing**: High performance. Hadoop and Spark use this approach. Nodes do not share any hardware. Storage and compute are located on separate machines networked together. Nodes in the cluster contain a subset of data locally instead of sharing one central data repository.
   - **Advantages**:
     - Co-locating compute and storage avoids networking latency issues
     - Generally cheaper to build and maintain
     - Improved scaling over shared-disk architecture
   - **Disadvantages**:
     - Scaling still limited because shuffling data between nodes is expensive
     - Storage and compute are tightly coupled
     - Tendency to overprovision

Snowflake uses a **multi-cluster shared data architecture** with three layers. It's a service-oriented architecture (each layer is a separate physical service that Snowflake manages, communicating over a network via RESTful interfaces) consisting of three physically separated but logically integrated layers:

1. **Cloud Services Layer** (coordination layer)
    - Authentication and access control
    - Infrastructure management, query optimizer, transaction manager, and security
    - Metadata management
2. **Query Processing Layer**
    - N virtual warehouses (separate compute clusters) to process queries. Users issue SQL commands to create virtual warehouses that Snowflake provisions and manages. These warehouses have access to the data storage layer and can cache data in the local storage layer.
3. **(Centralized/Shared) Data Storage Layer**
    - N databases
    - Cloud-agnostic storage layer

This architecture stems from the fact that Snowflake was purpose-built for the cloud. The advantages are:

- Decouples storage, compute, and management services
- Three infinitely scalable layers
- Workload isolation with virtual warehouses (e.g., analytics and pipeline jobs don’t have to contend for resources)

### 4.1. Storage Layer

- The storage layer is **persistent and infinitely scalable cloud storage**, residing in cloud providers' blob storage services such as AWS S3. Snowflake users, by proxy, benefit from the availability and durability guarantees of the cloud providers' blob storage. Data loaded into Snowflake is organized into databases and schemas and is accessible primarily as tables. All table data is stored in the centralized storage layer, acting as a single source of truth.
- Both structured and semi-structured data files can be loaded and stored in Snowflake.
- When data files are loaded or rows inserted into a table, Snowflake reorganizes the data into its **proprietary compressed, columnar table file format**. Since data is columnar:
  - Only the columns needed for a query are read, reducing the amount of data read from disk (**read optimization**).
  - This columnar structure also improves **data compression**, as columns are more likely to have similar values.
- Data is also **encrypted** at rest and in transit.
- The data that is loaded or inserted is partitioned into what Snowflake calls **micro-partitions**. This allows Snowflake to ignore partitions that are not needed for a query (**partition pruning**).
- **Storage is billed** by how much data is stored, based on a flat rate per TB calculated monthly.
- **Data is not directly accessible** in the underlying blob storage, only via SQL commands.

### 4.2. Compute Layer (Query Processing)

This layer performs the processing tasks on the data gathered from the storage layer to answer user queries. It consists of Snowflake-managed compute clusters called **virtual warehouses**. A virtual warehouse is a Snowflake object you create via a SQL command.

A virtual warehouse is a named abstraction for a cluster of cloud-based compute instances that Snowflake provisions and manages.

```sql
CREATE WAREHOUSE MY_WH WAREHOUSE_SIZE=LARGE;
```

Behind the scenes, running this command will create a cluster of compute instances in the cloud provider's infrastructure, composed of EC2 instances (or equivalents) arranged together as a distributed compute cluster. These instances run the software of the execution engine that Snowflake designed for the cloud. This engine carries out the computational tasks of the query plan generated by the services layer. Note that the user does not have to manage the cluster—Snowflake handles this for you, i.e., users interact with the abstracted warehouse object.

The underlying nodes of a virtual warehouse cooperate in a similar way to a shared-nothing compute cluster, making use of **local caching**.

Virtual warehouses are **ephemeral** and **highly flexible**:

- Virtual warehouses can be created or removed instantly by the user.
- Virtual warehouses can be paused or resumed. When paused, the cluster is not running, and you are not billed for it. When resumed, the cluster is started again. This is useful when variable usage patterns are expected.
- An unlimited number of virtual warehouses can be created, each with its own configuration. This allows for scaling out compute resources horizontally (for concurrent queries) or vertically (for complex queries). This also means that each warehouse is isolated from the others, so they do not compete for resources, e.g., you can create one warehouse for data loading and another for analytics.
- Virtual warehouses come in multiple "t-shirt" sizes indicating their relative compute power (from X-Small to 6X-Large). The size of the warehouse determines the number of nodes in the cluster and the amount of memory and CPU available to the cluster. The size of the warehouse also determines the cost of the warehouse.
- All running virtual warehouses have consistent access to the same data in the storage layer. Note that Snowflake uses strict ACID compliance to ensure data consistency across all virtual warehouses (this is managed by the **transaction manager** service in the services layer).

### 4.3. Services Layer

The services layer is a collection of **highly available and scalable services** that coordinate activities such as authentication, query optimization, and overall system management across all Snowflake accounts. This **global multi-tenant layer** is responsible for managing the virtual warehouses and the storage layer. It enables Snowflake to avoid creating a separate instance for each account (resulting in **economies of scale**) and facilitates **secure data sharing** between accounts.

Similar to the underlying virtual warehouse resources, the services layer also runs on cloud compute instances. Note that users have **no direct control** over the services layer—it is fully managed by Snowflake.

Services managed by this layer include:

- **Authentication and access control**
- **Infrastructure management**
- **Transaction management** to ensure ACID compliance
- **Metadata management** to store information about databases, tables, and users
- **Query parsing and optimization**
- **Security**

## 5. Editions and Features

Snowflake offers four **editions**, each tailored to specific needs and budgets. These editions differ in their features, capabilities, and pricing models:

1. **Standard**
   - ANSI standard SQL
   - ACID-compliant transactions
   - Continuous data protection (e.g., time travel and network policies)
2. **Enterprise**
   - Includes all features of the Standard edition
   - Designed for enterprise needs (e.g., multi-cluster warehouses and database failover capabilities)
3. **Business Critical**
   - Includes all features of the Enterprise edition
   - Adds enhanced security and higher levels of data protection to support organizations with very sensitive data
4. **Virtual Private Snowflake (VPS)**
   - Includes all features of the Business Critical edition
   - Provides the highest level of security for governmental bodies and financial institutions by operating in a dedicated environment

## 6. Object Model

The core objects in Snowflake are structured in a logical hierarchy with a **1:N relationship**. Every object in Snowflake is **securable**, meaning privileges (e.g., read, write) can be granted to roles, which are then assigned to users. This determines what users can do within Snowflake. Additionally, every object can be interacted with via SQL.

- **Organization**: A logical grouping of accounts.
- **Account**: A logical grouping of services (UI, compute, storage) accessible via the URL or the account object itself, which can be used to change account properties. Accounts are assigned **account-level objects**:
  - Network policy
  - User
  - Role
  - Database (see below)
  - Warehouse
  - Share
  - Resource monitor
- **Databases**: Logical grouping of schemas. Databases are assigned **database-level objects**:
  - Schema (see below)
- **Schemas**: Logical grouping of tables, views, and other objects. Schemas are assigned **schema-level objects**:
  - Stage
  - Pipe
  - Procedure
  - Function
  - Table: Logical representation of the stored data.
  - View
  - Task
  - Stream

### 6.1. Organization

Organizations are used to:

- Manage one or more Snowflake accounts.
- Set up and administer Snowflake features that make use of multiple accounts, e.g., database replication and failover.
- Monitor usage across accounts.

To set up an organization, you must contact Snowflake support, provide the organization name, and nominate an account. The `ORGADMIN` role is assigned to that account. The `ORGADMIN` is responsible for managing the lifecycle of accounts and can:

- Create accounts, view organization details, and list regions.
- Enable cross-account features, e.g., set up database replication for an account.
- Monitor account usage by querying the `ORGANIZATION_USAGE` schema in the Snowflake database.

### 6.2. Account

An **account** is the administrative unit for a collection of storage, compute, and cloud services, deployed and managed entirely on a selected cloud platform. Each account has the following characteristics:

- **Cloud Platform**: Each account is hosted on a single cloud provider (AWS, GCP, or Azure).
- **Region**: Each account is provisioned in a single geographic region (consider regulatory restrictions when moving data between regions).
- **Edition**: Each account is created with a single Snowflake edition (can be updated later).
- **System Role**: An account is created with the system-defined role `ACCOUNTADMIN`, which should be used sparingly. Enforce the principle of least privilege to maintain security.

The **account identifier** is a concatenation of the account locator, region ID, and cloud provider. Alternatively, you can use the concatenation of the organization name and account name.

### 6.3. Database and Schema

Both databases and schemas must follow naming conventions: they must start with an alphabetic character and cannot contain spaces or special characters unless enclosed in double quotes (in which case they become case-sensitive).

**Databases** are the first logical container for the data in Snowflake. They group schemas, which, in turn, group tables and views. Each database must have a unique identifier within an account.

- **Creating a Database**:
  - A database can be created from scratch, cloned from an existing database, created with a retention policy, or created as a replica or from a shared database.

   ```sql
   -- Create a new database
   create database my_database;

   -- Clone an existing database
   create database my_db_clone
   clone my_database;

   -- Create a database with a data retention policy
   CREATE DATABASE MYDB1 DATA_RETENTION_TIME_IN_DAYS = 10;

   -- Create a database as a replica from another account
   CREATE DATABASE MYDB1 AS REPLICA OF MYORG.ACCOUNT1.MYDB1;

   -- Create a database from a shared database in another account
   CREATE DATABASE SHARED_DB FROM SHARE UTT783.SHARE;
   ```

**Schemas** are logical containers for tables, views, and other objects. They must have a unique identifier within a database. Like databases, schemas must start with an alphabetic character and cannot contain spaces or special characters unless enclosed in double quotes.

- **Creating a Schema**:
  - Schemas can also be cloned from existing schemas.

   ```sql
   -- Create a new schema
   create schema my_schema;

   -- Clone an existing schema
   create schema my_schema_clone 
   clone my_schema;
   ```

The combination of a **database** and **schema** name (e.g., `my_database.my_schema`) forms a **namespace** in Snowflake. This namespace simplifies querying tables and objects within the schema. Alternatively, you can use the `USE` command to set the default database and schema for the session.

## 7. Table and View Types

Tables are a logical abstraction over the data in the storage area. They describe the structure of your data to enable querying. Snowflake offers four types of tables, each with its own data retention requirements:

### 7.1. Table Types

- **Permanent**
  - **Default table type**.
  - Exists until explicitly dropped.
  - **Time-travel**: Up to 90 days (e.g., undropping a table).
  - **Fail-safe**: 7 days (Snowflake can restore deleted data during this period).
  
- **Temporary**
  - Used for **transitory data**, e.g., a step in a complex ETL process or storing the result of a query for downstream processing.
  - Cannot be converted to another type of table.
  - Persists only for the **duration of the session**.
  - **Time-travel**: 1 day.
  - **Fail-safe**: None.

- **Transient**
  - Exists until explicitly dropped.
  - Designed for use cases where fail-safe is not required.
  - **Time-travel**: 1 day.
  - **Fail-safe**: None.

- **External**
  - Enables querying of data outside Snowflake, e.g., an Azure container, by overlaying a structure on this external data.
  - **Read-only** table.
  - **Time-travel**: Not available.
  - **Fail-safe**: Not available.

### 7.2. View Types

Views are **logical representations** of the data in tables. They do not store the data itself but instead store query definitions. Snowflake supports three types of views:

- **Standard View**
  - A logical object that stores a **SELECT query definition** (not the data itself).
  - Does not contribute to storage costs.
  - If the source table is dropped, querying the view returns an error.
  - Often used to restrict or customize the contents of a table for users.
  
  ```sql
  -- Create a standard view
  CREATE VIEW MY_VIEW AS
  SELECT COL1, COL2 FROM MY_TABLE;
  ```

- **Materialized View**
  - Stores the **results of a query definition** and refreshes periodically.
  - Known as a **pre-computed dataset** in Snowflake documentation.
  - Incurs compute and storage costs:
    - Compute: As a **serverless feature**, it refreshes in the background (outside of a user’s virtual warehouse).
    - Storage: Costs are incurred for storing the materialized data.
  - Typically used to **boost performance** when querying large or external tables.
  
   ```sql
   -- Create a materialized view
  CREATE MATERIALIZED VIEW MY_VIEW AS
  SELECT COL1, COL2 FROM MY_TABLE;
   ```

- **Secure View**
  - Both standard and materialized views can be secure.
  - The **underlying query definition** is only visible to authorized users, e.g., by calling `GET DDL` or `DESCRIBE` on the view.
  - Some query optimizations are bypassed to improve security.
  
   ```sql
   -- Create a secure view
  CREATE SECURE VIEW MY_VIEW AS
  SELECT COL1, COL2 FROM MY_TABLE;
   ```

## 8. User-Defined Functions (UDFs)

User-Defined Functions (UDFs) are **schema-level objects** that allow users to write custom functions in the following languages:

- SQL
- JavaScript
- Python
- Java

UDFs accept **zero or more parameters** and can return either **scalar** or **tabular results** (via User-Defined Table Functions or UDTFs). UDFs can be called directly as part of a SQL statement. Additionally, UDFs support **overloading**, meaning you can define multiple UDFs with the same name but different parameter lists.

```sql
create function area_of_circle(radius float)
returns float
as $$
   pi() * radius * radius
$$;

select area_of_circle(col)
from my_table;
```

UDFs are distinct from stored procedures in that UDFs can be used directly within SQL queries, whereas stored procedures **cannot** be used as part of a SQL statement.

### 8.1. UDFs in Other Languages

Snowflake supports writing **JavaScript UDFs**, offering additional flexibility by enabling the use of high-level programming features. Key points to note about JavaScript UDFs:

- **Language Parameter**: Specify JavaScript as the language when creating the UDF.
- **Recursion**: JavaScript UDFs can refer to themselves recursively.
- **Type Mapping**: Snowflake data types are automatically mapped to JavaScript data types.

```sql
create function js_factorial(D double)
returns double
language javascript
as $$
   if (D <= 0) {
      return 1;
   } else {
      var result = 1;
      for (var i = 2; i <= D; i++) {
         result *= i;
      }
      return result;
   }
$$;
```

In addition to SQL and JavaScript, you can write UDFs in **Python** and **Java**. These languages offer more flexibility for complex logic and integrations.

### 8.2. External Functions

Snowflake also supports **external functions**, which allow you to call code maintained and executed **outside of Snowflake**. This is a powerful feature that addresses some limitations of internal UDFs.

- External functions require the creation of an **API integration object** to define the external API endpoint.
- They are slower than internal UDFs but much more flexible.
- Currently, external functions are limited to **scalar functions** and cannot return tabular results.
- External functions are **not sharable**, less secure, and may incur **egress charges**.
- External functions are slower than internal UDFs.
- They offer flexibility by enabling the integration of external services.
- They are **less secure** and are subject to egress charges.

```sql
create or replace external function calc_sentiment(string_col varchar)  -- Function name and input parameter
returns variant  -- Return type
api_integration = aws_api_integration  -- Integration object
as 'https://ttu.execute-api.eu-west-2.amazonaws.com/'  -- URL Proxy Service
;

-- API Integration Object:
CREATE OR REPLACE API INTEGRATION aws_api_integration
API_PROVIDER = 'aws_api_gateway'
API_AWS_ROLE_ARN = 'arn:aws:iam::123456789012:role/my_cloud_account_role'
API_ALLOWED_PREFIXES = ('https://ttu.execute-api.eu-west-2.amazonaws.com/')
ENABLED = TRUE;
```

## 9. Stored Procedures

In Relational Database Management Systems (RDBMS), stored procedures are **named collections of SQL statements** that often contain procedural logic. They are generally used for administrative tasks. For example:

```sql
-- Create a stored procedure to delete old records from multiple tables:
CREATE PROCEDURE clear_emp_tables() 
AS 
BEGIN
   DELETE FROM EMP01 WHERE EMP_DATE < DATEADD(MONTH, -1, CURRENT_DATE);
   DELETE FROM EMP02 WHERE EMP_DATE < DATEADD(MONTH, -1, CURRENT_DATE);
   DELETE FROM EMP03 WHERE EMP_DATE < DATEADD(MONTH, -1, CURRENT_DATE);
   DELETE FROM EMP04 WHERE EMP_DATE < DATEADD(MONTH, -1, CURRENT_DATE);
   DELETE FROM EMP05 WHERE EMP_DATE < DATEADD(MONTH, -1, CURRENT_DATE);
END;

-- Execute the stored procedure:
CALL clear_emp_tables();
```

### 9.1. Stored Procedures in Snowflake

Stored procedures in Snowflake are **database objects** that allow you to perform complex operations, such as administrative tasks, data manipulation, and automation of workflows. Snowflake supports creating stored procedures using the following methods:

- **JavaScript**: Write the procedural logic using JavaScript.
- **Snowflake Scripting**: Extend SQL with procedural logic (e.g., loops, conditions).
- **Snowpark**: Use Python, Java, or Scala to write stored procedures.

Snowflake enables you to write stored procedures using **JavaScript**, which runs within Snowflake's procedural environment. Below is an example:

```sql
CREATE OR REPLACE PROCEDURE example_stored_procedure(param1 STRING) -- Identifier
RETURNS STRING -- Return type
LANGUAGE JAVASCRIPT -- Language option: JAVASCRIPT
EXECUTE AS OWNER -- Authorization context: OWNER or CALLER
AS
$$
   var tableName = param1;
   var sql_command = "SELECT * FROM " + tableName; // Example query
   var result = snowflake.execute({sqlText: sql_command}); // Execute the query
   return "Query executed successfully on table: " + tableName;
$$;

-- Execute the stored procedure:
CALL example_stored_procedure('MY_TABLE');
```

1. **Return Values**:
   - Stored procedures in Snowflake **can return values** or perform actions without returning a result.
   - In this example, the procedure returns a confirmation string.

2. **Execution Context**:
   - `EXECUTE AS OWNER`: The procedure runs with the privileges of the procedure owner.
   - `EXECUTE AS CALLER`: The procedure runs with the privileges of the caller.

### 9.2. UDF vs Stored Procedure

The key difference between User-Defined Functions (UDFs) and Stored Procedures is in their purpose:

- **Stored Procedures**:
  - Perform **actions** rather than just returning values.
  - Used for tasks such as:
    - Administrative operations.
    - Automating data manipulation.
    - Managing workflows.
  - Example use case: Deleting old records from multiple tables, as shown above.

- **User-Defined Functions (UDFs)**:
  - **Calculate values** and return results to the user.
  - Used in **SQL queries** to compute or transform data.
  - Example use case: Calculating the area of a circle from a table of radii.

In summary:

- Use **stored procedures** when you need to **perform actions** such as database updates or automation tasks.
- Use **UDFs** when you need to **return a value** that can be used in SQL queries.

## 10. Sequences

A **sequence** is a schema-level object used to generate **globally unique sequential numbers**. Sequences are commonly used for generating surrogate keys or other identifiers. However, note that sequences are **not guaranteed to be gapless** due to events like rollbacks or task failures.

```sql
-- Create a sequence
CREATE SEQUENCE MY_SEQUENCE
START = 1
INCREMENT = 1;

SELECT MY_SEQUENCE.NEXTVAL; -- Output: 1
SELECT MY_SEQUENCE.NEXTVAL; -- Output: 2
```

## 11. Tasks and Streams

### 11.1. Tasks

A **task** is an object used to schedule the execution of a **SQL command** or a **stored procedure**. Tasks are often used to automate data processing workflows. To create a task, you need the `ACCOUNTADMIN` role or the `CREATE TASK` privilege. Here's an example:

```sql
-- Create a task
CREATE TASK T1 -- Task name
WAREHOUSE = MYWH -- Warehouse to use (can also use the serverless model)
SCHEDULE = '30 MINUTE' -- Triggering mechanism
AS
COPY INTO MY_TABLE -- SQL command to execute
FROM $MY_STAGE;

-- Start the task:
ALTER TASK T1 RESUME;

-- Stop the task:
ALTER TASK T1 SUSPEND;
```

1. **Execution Privileges**:
   - To execute a task, you need the global privilege `EXECUTE TASK` as well as ownership or `OPERATE` privilege on the task object.

2. **Task Dependencies (DAGs)**:
   - Tasks can be **chained together** into a Directed Acyclic Graph (DAG).
   - All tasks in a DAG must:
     - Have the same owner.
     - Be stored in the same database and schema.

```sql
-- Create a DAG of tasks
CREATE TASK T2
WAREHOUSE = MYWH
AFTER T1 -- Chain the task to the previous task
AS
COPY INTO MY_TABLE FROM $MY_STAGE;
```

### 11.2. Streams

A **stream** is a schema-level object that **captures changes** (DML events) to a table. Streams are used to **view and track changes** like inserts, updates, and deletes on a source table.

```sql
-- Create a stream
CREATE STREAM MY_STREAM ON TABLE MY_TABLE;

-- Query the stream
SELECT * FROM MY_STREAM;
```

The query result contains the changes made to the source table, along with additional metadata.

### 11.3. Using Tasks with Streams

A common ETL pattern involves using a **stream** to capture changes to a table and then using a **task** to process these changes. This approach enables the creation of a **continuous ETL pipeline**.

```sql
-- Create a task to process the changes
CREATE TASK PROCESS_CHANGES
WAREHOUSE = MYWH
SCHEDULE = '1 MINUTE'
WHEN 
SYSTEM$STREAM_HAS_DATA('MY_STREAM') -- Check if the stream has data
AS 
INSERT INTO MY_TABLE2(COL1, COL2)
SELECT COL1, COL2 FROM MY_STREAM
WHERE METADATA$ACTION = 'INSERT';
```

1. **Stream Data**:
   - The result of querying a stream includes DML changes made to the source table, along with metadata such as the type of action (`INSERT`, `UPDATE`, `DELETE`).

2. **System Function**:
   - `SYSTEM$STREAM_HAS_DATA`: A boolean function that checks whether the stream contains new data to process.

3. **Use Case**:
   - This combination of tasks and streams is ideal for implementing **incremental ETL pipelines**, where changes to a source table are tracked and processed continuously.

## 12. Billing Overview

In Snowflake, there are two types of pricing models:

1. **On-demand pricing**: You pay for what you use, calculated based on actual consumption.
2. **Capacity pricing**: You pay for usage upfront, often at a discounted rate compared to on-demand pricing.

The account is billed in the following main areas.

### 12.1. Compute Billing (Billed in Snowflake Credits)

Compute billing covers services that utilize **Snowflake compute resources**. These costs are calculated in **Snowflake credits** and are categorized into the following:

- **Virtual Warehouse Services**:
  - Charges for compute resources used by virtual warehouses.
  - Includes activities like query execution, data loading/unloading, and other operations performed by user-provisioned virtual warehouses.
- **Cloud Services**:
  - Charges for **always-on services** provided by Snowflake's cloud infrastructure.
  - Includes metadata management, query optimization, authentication, and other system activities running in the **Cloud Services Layer**.
  - Costs are predictable and typically a small percentage of the overall compute usage.
- **Serverless Services**:
  - Charges for **on-demand services** provided by Snowflake that do not require user-provisioned virtual warehouses.
  - Examples include:
    - Snowpipe for continuous data loading.
    - Tasks or procedures that execute without a pre-defined warehouse.
    - Materialized view maintenance.
  - Unlike cloud services, these are billed based on the actual resources consumed for the operation.

### 12.2. Data Storage and Transfer Billing (Billed by the Cloud Provider)

Storage and transfer costs are typically billed directly by the cloud provider (AWS, GCP, or Azure) and depend on the following:

- **Storage Services**:
  - Charges for data stored in Snowflake's centralized storage layer.
  - Includes structured and semi-structured data stored in tables, as well as file storage in Snowflake stages.
  - Costs are billed based on the amount of data stored and the duration of storage (per TB per month).

- **Data Transfer Services**:
  - Charges for the movement of data **into or out of Snowflake**.
  - Includes:
    - **Ingress** (data loaded into Snowflake).
    - **Egress** (data exported out of Snowflake or shared with other accounts or regions).
    - Charges vary based on the volume of data transferred and the geographic regions involved.

### 12.3. Key Differences: Cloud Services vs. Serverless Services

| **Aspect**                | **Cloud Services**                                                                                 | **Serverless Services**                                                    |
| ------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Definition**            | Always-on services like metadata management, query optimization, and authentication.               | On-demand compute services that Snowflake provisions dynamically.          |
| **Billing**               | Charged as part of overall compute usage, typically a fixed percentage of virtual warehouse usage. | Charged based on the actual resources consumed for the specific operation. |
| **Examples**              | Metadata queries, query parsing, user authentication, transaction coordination.                    | Snowpipe, serverless tasks, materialized view refreshes.                   |
| **Resource Provisioning** | Runs on Snowflake's **Cloud Services Layer**.                                                      | Dynamically provisions resources on demand.                                |
| **Usage Model**           | Passive and background services.                                                                   | Active and tied to specific serverless operations.                         |

## 13. Connectivity

### 13.1. SnowSQL

SnowSQL is Snowflake’s command-line client that allows users to interact with Snowflake via SQL. Using SnowSQL, users can:

- Execute **DML Statements** (e.g., `INSERT`, `UPDATE`, `DELETE`, `MERGE`)
- Execute **DDL Statements** (e.g., `CREATE`, `ALTER`)
- Execute **DQL Statements** (e.g., `SELECT`)
- Perform **Account Management** (e.g., setting account parameters)
- Load and unload data to and from Snowflake

### 13.2. Connectors and Drivers

Snowflake provides a variety of connectors and drivers to integrate with external tools and platforms. These are classified into five main categories:

1. **Business Intelligence (BI) Tools**:
   - Tools for visualizing and analyzing data.
   - Examples: Tableau, Power BI.

2. **Data Integration Tools**:
   - Tools for moving data between systems.
   - Examples: dbt, Informatica.

3. **Data Governance and Security Tools**:
   - Tools for managing data access, security, and governance.
   - Examples: Collibra, Datadog, Vault.

4. **Machine Learning and Data Science Tools**:
   - Tools for analyzing and modeling data.
   - Examples: DataRobot, Dataiku, Amazon SageMaker.

5. **SQL Development Tools and Management**:
   - Tools for writing and executing SQL queries.
   - Examples: SQLDBM, SeekWell, Agile Data Engine.

### 13.3. Snowflake Scripting

**Snowflake Scripting** is an extension of Snowflake SQL that introduces **procedural logic** to write stored procedures or execute procedural code outside of a stored procedure. This enables users to create more complex workflows and operations within Snowflake. Below is an overview of its features and syntax.

A Snowflake Scripting block is structured as follows:

```sql
DECLARE
   -- Variable declarations, cursor declarations, etc.
BEGIN
   -- Snowflake Scripting and SQL statements
EXCEPTION
   -- Statements for handling exceptions
END;
```

Some key features of Snowflake Scripting include:

1. **Variables**:
   - You can declare variables and assign values within procedural code.

   ```sql
   CREATE PROCEDURE pythagoras()
   RETURNS FLOAT
   LANGUAGE SQL
   AS
   DECLARE
      leg_a NUMBER(38, 2);
      leg_b NUMBER(38, 2);
      hypotenuse NUMBER(38, 5);
   BEGIN
      leg_a := 3;
      leg_b := 4;
      hypotenuse := SQRT(SQUARE(leg_a) + SQUARE(leg_b));
      RETURN hypotenuse;
   END;
   ```

2. **Control Structures**:
   - Use `IF`, `ELSE`, and `CASE` for conditional logic.

   ```sql
   BEGIN
      LET count := 4;
      IF (count % 2 = 0) THEN
         RETURN 'even value';
      ELSE
         RETURN 'odd value';
      END IF;
   END;
   ```

3. **Loops**:
   - Support for looping constructs like `WHILE` and `FOR`.

   ```sql
   DECLARE
      total INTEGER DEFAULT 0;
      max_num INTEGER DEFAULT 10;
   BEGIN
      FOR i IN 1..max_num DO
         total := total + i;
      END FOR;
      RETURN total;
   END;
   ```

4. **Cursors**:
   - Use cursors to iterate over result sets.

   ```sql
   DECLARE
      total_amount FLOAT;
      c1 CURSOR FOR SELECT amount FROM transactions;
   BEGIN
      total_amount := 0.0;
      FOR record IN c1 DO
         total_amount := total_amount + record.amount;
      END FOR;
      RETURN total_amount;
   END;
   ```

5. **Result Sets**:
   - Return multiple rows from a procedure using result sets.

   ```sql
   DECLARE
      res RESULTSET;
   BEGIN
      res := (SELECT amount FROM transactions);
      RETURN TABLE(res);
   END;
   ```

### 13.4. Snowpark

Snowpark is an API that allows users to interact with Snowflake **outside of the Snowflake interface**, enabling code to be written in **Scala**, **Java**, or **Python**. Snowpark provides methods for working with data, such as:

- `select`
- `join`
- `group_by`
- `distinct`
- `drop`
- `union`
- `sort`

Snowpark uses the **DataFrame abstraction**, similar to APIs like PySpark or Pandas, making it easier to manipulate large datasets. Function calls in Snowpark are **lazily evaluated** and leverage a **pushdown computation model**. This means that instead of pulling data into the client environment for computation, the computation is **pushed down** to the data source and performed using Snowflake’s compute resources.

This contrasts with the **Snowflake Connector for Spark**, which pulls the data into the Spark cluster for computation, resulting in higher data transfer costs and potentially slower performance compared to Snowpark. The key features of Snowpark include:

1. **Pushdown Computation**:
   - All transformations (e.g., `select`, `filter`, `join`) are sent to Snowflake for execution using Snowflake's compute engine.
   - Reduces data transfer and improves performance.

2. **Lazy Evaluation**:
   - Operations on DataFrames are **not executed immediately**. Execution only occurs when an action like `collect()` or `write()` is called.
   - This ensures efficient query planning and execution.

3. **DataFrame API**:
   - Provides an easy-to-use, Pandas-like abstraction for interacting with Snowflake tables.

4. **Support for Multiple Languages**:
   - Code can be written in **Python**, **Scala**, or **Java**, enabling flexibility for developers with diverse skill sets.

Below is an example of using the **Snowpark Python API** to interact with Snowflake and perform operations on data.

```python
from snowflake.snowpark.session import Session
from snowflake.snowpark.functions import col, lit, avg

# Define the Snowflake connection parameters
connection_parameters = {
    "account": "your_account_name",
    "user": "your_username",
    "password": "your_password",
    "role": "your_role",
    "warehouse": "your_warehouse",
    "database": "your_database",
    "schema": "your_schema"
}

# Create a Snowpark session
session = Session.builder.configs(connection_parameters).create()

# Load a table into a Snowpark DataFrame
df = session.table("my_table")

# Perform transformations
filtered_df = df.filter(col("amount") > 100)  # Filter rows
aggregated_df = filtered_df.group_by("category").agg(avg("amount").alias("avg_amount"))  # Aggregate data
result_df = aggregated_df.sort(col("avg_amount").desc())  # Sort results

# Show the results
result_df.show()

# Write the transformed data back to another table
result_df.write.save_as_table("my_table_transformed", mode="overwrite")

# Close the session
session.close()
```

The key benefits of using Snowpark include:

- **Performance**: With computation pushed to Snowflake, it minimizes data transfer and leverages Snowflake’s scalable compute power.
- **Ease of Use**: The DataFrame API provides a familiar and intuitive interface for data manipulation.
- **Flexibility**: Supports multiple languages (Python, Java, Scala) to cater to developers' preferences.
- **Cost Efficiency**: Reduces the need for additional infrastructure like Spark clusters.

## 14. [SKIP] Account Access and Security

We skip this section as it is not relevant to the performance aspects of Snowflake.

## 15. Performance Concepts: Virtual Warehouses

In this section, we will cover the following topics:

- Virtual Warehouse Introduction
- Warehouse Configuration & Billing
- Warehouse State & Properties
- Resource Monitors
- Multi-cluster Warehouses

### 15.1. Overview

A **Virtual Warehouse** is a named abstraction for a **Massively Parallel Processing (MPP)** compute cluster. These clusters are composed of worker nodes running on the chosen cloud platform.

Virtual Warehouses are responsible for executing:

- **DQL operations**: Queries like `SELECT`
- **DML operations**: Modifications like `UPDATE`, `DELETE`
- **Data loading operations**: Commands like `COPY INTO`

As a user, you interact with the named warehouse object, **not the underlying compute resources**.

Key features of Virtual Warehouses:

1. You can spin up and shut down a virtually unlimited number of warehouses without resource contention.
2. Warehouse configuration (e.g., size, scaling policy) can be changed **on-the-fly**.
3. Virtual Warehouses include **local SSD storage**, referred to as the **warehouse cache**, which stores raw data retrieved from the storage layer. This can make subsequent queries faster by avoiding redundant reads from storage.

Virtual Warehouses can be created via the Snowflake UI or SQL commands. Below are examples of Virtual Warehouse operations with comments to clarify their purpose:

```sql
-- Drop an existing warehouse (if it exists):
DROP WAREHOUSE MY_WAREHOUSE;

-- Create a new warehouse named 'MY_MED_WH' with size MEDIUM:
CREATE WAREHOUSE MY_MED_WH
WAREHOUSE_SIZE = 'MEDIUM';

-- Suspend a warehouse to stop compute usage (no credits consumed when suspended):
ALTER WAREHOUSE MY_WH SUSPEND;

-- Modify the size of an existing warehouse to MEDIUM:
ALTER WAREHOUSE MY_WH_2 SET
WAREHOUSE_SIZE = 'MEDIUM';

-- Create a multi-cluster warehouse with 1 to 3 clusters and STANDARD scaling policy:
CREATE WAREHOUSE MY_WH_3
MIN_CLUSTER_COUNT = 1
MAX_CLUSTER_COUNT = 3
SCALING_POLICY = 'STANDARD';
```

### 15.2. Warehouse State and Properties

Virtual Warehouses in Snowflake can exist in the following states:

1. **Started**:  
   - The warehouse is running and can execute queries.  
   - **Credits are consumed** while the warehouse is in this state.  

2. **Suspended**:  
   - The warehouse is stopped and does **not consume credits**.  

3. **Resizing**:  
   - The warehouse is actively changing its size.  
   - Resizing can occur while the warehouse is running.  

#### 15.2.1. Managing Warehouse States

You can manually **suspend** or **resume** a warehouse using the `ALTER WAREHOUSE` command.

```sql
-- By default, a warehouse is started when created
CREATE WAREHOUSE my_wh WITH WAREHOUSE_SIZE = 'SMALL';

-- Suspend the warehouse
ALTER WAREHOUSE my_wh SUSPEND;

-- Resume the warehouse
ALTER WAREHOUSE my_wh RESUME;
```

By default, a warehouse is **started** upon creation. To create a warehouse that starts in a suspended state, use the `INITIALLY_SUSPENDED` parameter.

```sql
-- Create a warehouse that is initially suspended
CREATE WAREHOUSE my_wh INITIALLY_SUSPENDED = TRUE;
```

Creating a warehouse in a suspended state is useful when you need to perform preparatory tasks (e.g., creating tables) before starting the warehouse. Table creation does **not require** a running warehouse.

To automatically suspend a warehouse after a period of inactivity, use the `AUTO_SUSPEND` parameter.

```sql
-- Specify the number of seconds of inactivity before automatic suspension
-- Minimum value: 60 seconds, Default value: 600 seconds
ALTER WAREHOUSE my_wh SET AUTO_SUSPEND = 600;
```

- Suspending a warehouse **clears the cache**, so the next query will have to fetch data from the storage layer, which may result in longer query times.  
- Frequently suspending and resuming warehouses can result in **provisioning delays** (usually a few seconds) and may lead to unnecessary costs since **warehouses are billed for a minimum of 60 seconds** per usage cycle.

To automatically resume a warehouse when a query is submitted, use the `AUTO_RESUME` parameter.

```sql
-- Enable automatic resume when a query is submitted
-- Default value: TRUE
ALTER WAREHOUSE my_wh SET AUTO_RESUME = TRUE;
```

#### 15.2.2. Summary of Key Warehouse Parameters

| **Parameter**         | **Description**                                                                         | **Default Value** |
| --------------------- | --------------------------------------------------------------------------------------- | ----------------- |
| `AUTO_SUSPEND`        | Automatically suspends a warehouse after a specified period of inactivity (in seconds). | 600 seconds       |
| `AUTO_RESUME`         | Automatically resumes a warehouse when a query is submitted.                            | TRUE              |
| `INITIALLY_SUSPENDED` | Specifies whether a warehouse should start in a suspended state.                        | FALSE             |

### 15.3. Warehouse Size and Billing

When creating a Virtual Warehouse, you can choose from a range of sizes, each with different compute capacities. The size of the warehouse affects the **compute power** available for query execution and the **cost** associated with running the warehouse.

- Virtual Warehouses can be created in **10 t-shirt sizes** ranging from `X-SMALL` to `6X-LARGE`.  
- The **compute power approximately doubles** with each size increase.  
- Larger Virtual Warehouses typically offer **better query performance**, especially for workloads with high concurrency or complex queries.  
- **Choosing the right size** is typically determined by testing a representative query or workload.  
- Data loading operations (e.g., `COPY INTO`) do not generally benefit from larger Virtual Warehouses.  
- Increasing the size of a Virtual Warehouse does **not guarantee improved data loading performance**.  
- Virtual Warehouses are **billed based on their size** and **time spent in the `STARTED` state**:
  - **Billing starts** when a warehouse is started, even if no queries are being executed.  
  - The **first 60 seconds** are always billed, followed by per-second billing.  
- **Cost doubles** with each increase in warehouse size.  

### 15.4. Resource Monitors

Resource Monitors are objects used to set credit usage limits on Virtual Warehouses or accounts. They can be applied at the account level or to specific warehouses. Limits can be set for a specified interval (e.g., daily, weekly, or monthly) or a specific date range. When a credit usage limit is reached, actions can be triggered, such as notifying users, suspending the warehouse, or immediately suspending the warehouse.

- **Resource Monitors** are objects used to set **credit usage limits** on Virtual Warehouses or accounts.  
- They can be applied at the **account level** or to **specific warehouses**.  
- Limits can be set for a specified **interval** (e.g., daily, weekly, or monthly) or a specific **date range**.  
- When a credit usage limit is reached, **actions** can be triggered, such as:
  - **Notify** users about credit consumption.
  - **Suspend** the warehouse.
  - **Suspend immediately** to stop the warehouse instantly.
- **Only account administrators** can create and manage Resource Monitors.  
- Resource Monitors are a **proactive tool** to manage and control credit usage, avoiding unplanned costs.

The following SQL commands demonstrate how to create a Resource Monitor, set thresholds, and assign it to an account.

```sql
-- Create a Resource Monitor for credit usage control
CREATE RESOURCE MONITOR ANALYSIS_RM
WITH CREDIT_QUOTA = 100  -- Set credit limit to 100 credits
FREQUENCY = MONTHLY      -- Monitor usage monthly
START_TIMESTAMP = '2023-01-04 00:00 GMT'  -- Start monitoring from this date
TRIGGERS
  ON 50 PERCENT DO NOTIFY                  -- Notify at 50% of credit usage
  ON 75 PERCENT DO NOTIFY                  -- Notify at 75% of credit usage
  ON 95 PERCENT DO SUSPEND                 -- Suspend warehouse at 95% usage
  ON 100 PERCENT DO SUSPEND_IMMEDIATE;     -- Immediately suspend at 100% usage

-- Assign the Resource Monitor to the account
ALTER ACCOUNT SET RESOURCE_MONITOR = ANALYSIS_RM;
```

#### 15.4.1. Best Practices for Warehouse Size and Resource Monitors

1. **Warehouse Size**:
   - Use larger warehouses only for workloads that require high concurrency or complex queries.
   - Avoid oversizing warehouses for simple tasks like data loading.

2. **Billing Optimization**:
   - Configure **AUTO_SUSPEND** to minimize idle time and avoid unnecessary costs.
   - Monitor warehouse usage to identify underutilized warehouses.

3. **Resource Monitors**:
   - Use Resource Monitors to set credit limits and enforce cost controls.
   - Configure appropriate triggers to notify users and prevent unexpected charges.

### 15.5. Scaling Up and Out: Multi-Cluster Warehouses

#### 15.5.1. Scaling Up a Virtual Warehouse

- **Scaling up** a Virtual Warehouse involves resizing it to a larger size, which can improve **query performance**.
- Virtual Warehouses can be manually resized through the **Snowflake UI** or **SQL commands**.
- Resizing a **running warehouse**:
  - Does **not impact running queries**.
  - Additional compute resources are used for **queued** and **new queries**.
- **Decreasing the size** of a running warehouse:
  - Removes compute resources.
  - **Clears the warehouse cache**, which may result in longer query times for subsequent queries.

#### 15.5.2. Scaling Out: Multi-Cluster Warehouses

A **multi-cluster warehouse** is a group of Virtual Warehouses that can automatically **scale in and out** based on query demand and concurrency levels.

The key parameters for configuring a multi-cluster warehouse are:

1. **MIN_CLUSTER_COUNT**:
   - Specifies the **minimum number of warehouses** in the multi-cluster group.

2. **MAX_CLUSTER_COUNT**:
   - Specifies the **maximum number of warehouses** in the multi-cluster group.

- **MAXIMIZED Mode**:
  - If `MIN_CLUSTER_COUNT` and `MAX_CLUSTER_COUNT` are set to the same value, the multi-cluster warehouse will run **at full capacity**, with the specified number of clusters running continuously.
  
- **AUTO-SCALE Mode**:
  - If `MIN_CLUSTER_COUNT` and `MAX_CLUSTER_COUNT` are set to different values, the multi-cluster warehouse will **scale in and out** dynamically based on demand.

#### 15.5.3. Scaling Policies

Multi-cluster warehouses operate under one of two scaling policies:

1. **Standard Policy**:
   - Aims to **minimize query queuing** by scaling out quickly.
   - Behavior:
     - When a query is queued, a **new warehouse is added immediately**.
     - Every minute, a background process checks if the load on the least busy warehouse can be redistributed.
     - If this condition is met for **2 consecutive minutes**, the least busy warehouse is marked for **shutdown**.

2. **Economy Policy**:
   - Aims to **conserve credits** by running warehouses fully loaded for longer periods.
   - Behavior:
     - When a query is queued, the system estimates whether there is enough query load to keep a new warehouse **busy for 6 minutes**.
     - Every minute, a background process checks if the load on the least busy warehouse can be redistributed.
     - If this condition is met for **6 consecutive minutes**, the least busy warehouse is marked for **shutdown**.

#### 15.5.4. Credit Consumption in Multi-Cluster Warehouses

- The **total credit cost** of a multi-cluster warehouse is the sum of all the individual running warehouses in the group.
- The **maximum credits consumed** by a multi-cluster warehouse are calculated as:
  - `(Number of warehouses) × (Hourly credit rate of warehouse size)`.
- Since multi-cluster warehouses dynamically scale based on demand, the actual credit usage is usually **a fraction of the maximum consumption**.

#### 15.5.5. Concurrency Behavior Properties

Multi-cluster warehouses include parameters to manage query concurrency and timeouts:

1. **MAX_CONCURRENCY_LEVEL**:
   - Specifies the **number of concurrent SQL statements** that can be executed against a warehouse before:
     - Queries are queued.
     - Additional compute power is provided.

2. **STATEMENT_QUEUED_TIMEOUT_IN_SECONDS**:
   - Specifies the **time (in seconds)** a SQL statement can remain queued on a warehouse before it is aborted.

3. **STATEMENT_TIMEOUT_IN_SECONDS**:
   - Specifies the **time (in seconds)** after which a running SQL statement is **aborted** on a warehouse.

#### 15.5.6. Examples of Multi-Cluster Warehouse Configuration

Below are SQL examples of configuring a multi-cluster warehouse with comments explaining the parameters:

```sql
-- Create a multi-cluster warehouse with a minimum of 1 cluster and a maximum of 3 clusters
CREATE WAREHOUSE MY_MULTI_WH
WAREHOUSE_SIZE = 'LARGE'            -- Set the size of each individual warehouse
MIN_CLUSTER_COUNT = 1               -- Minimum number of clusters in the group
MAX_CLUSTER_COUNT = 3               -- Maximum number of clusters in the group
SCALING_POLICY = 'STANDARD';        -- Use the standard scaling policy to minimize queuing

-- Adjust concurrency settings for the warehouse
ALTER WAREHOUSE MY_MULTI_WH
SET
   MAX_CONCURRENCY_LEVEL = 10,      -- Allow up to 10 concurrent SQL statements
   STATEMENT_QUEUED_TIMEOUT_IN_SECONDS = 30, -- Abort queries queued for more than 30 seconds
   STATEMENT_TIMEOUT_IN_SECONDS = 300; -- Abort running queries after 300 seconds (5 minutes)

-- Change the scaling policy to ECONOMY to conserve credits
ALTER WAREHOUSE MY_MULTI_WH
SET SCALING_POLICY = 'ECONOMY';

-- Suspend the warehouse to stop compute usage
ALTER WAREHOUSE MY_MULTI_WH SUSPEND;

-- Resume the warehouse to start it ag ain
ALTER WAREHOUSE MY_MULTI_WH RESUME;
```

### 15.6. Query Acceleration Service

The **Query Acceleration Service** is a Snowflake feature designed to **dynamically add serverless compute power** to a Virtual Warehouse when a **complex query** requires additional resources. This service improves query performance by **offloading parts of the query plan** that can be executed in parallel to **serverless compute nodes** provisioned on-demand.

- Snowflake **analyzes the query plan** and identifies fragments of the query that:
  1. **Can be run in parallel**.
  2. **Involve large numbers of partitions to be scanned**.
- These query fragments are then offloaded to dynamically provisioned **serverless compute resources**, reducing the load on the main warehouse and accelerating the query execution.

#### 15.6.1. Query Acceleration Eligibility

For a query to be accelerated using the Query Acceleration Service, it must meet the following conditions:

1. **Parallelizable Query Plan**:
   - At least part of the query plan must be able to run in parallel.
2. **Partition Scanning**:
   - The query must involve **sufficient partitions** to justify the use of additional compute resources.

#### 15.6.2. Verifying Query Acceleration Eligibility

Snowflake provides the following tools to verify whether a query is eligible for acceleration:

1. **`QUERY_ACCELERATION_ELIGIBLE` View**:
   - This system view provides details about whether a query is eligible for query acceleration.

2. **`SYSTEM$ESTIMATE_QUERY_ACCELERATION` Function**:
   - This function estimates the level of query acceleration needed for a given query. It evaluates:
     - The number of partitions being scanned.
     - Whether the query plan can be parallelized.

#### 15.6.3. Example Usage

Here are examples of how to use Snowflake's Query Acceleration tools:

```sql
-- Check if a specific query is eligible for acceleration using the system view
SELECT QUERY_ID, QUERY_TEXT, ELIGIBLE
FROM QUERY_ACCELERATION_ELIGIBLE
WHERE QUERY_ID = '<your_query_id>';

-- Estimate the acceleration level for a specific query
SELECT SYSTEM$ESTIMATE_QUERY_ACCELERATION('<your_query_id>') AS acceleration_estimate;
```

## 16. Performance Concepts: Query Optimization

This section focuses on query optimization techniques and features in Snowflake to improve query performance and execution efficiency. Below are the key concepts and tools related to query optimization:

### 16.1. Analytics Tools

Snowflake provides **analytics tools** to analyze query performance and identify optimization opportunities. These tools include:

- **Query Profiler**:
  - A visual tool available in the Snowflake Web UI that allows users to view query execution plans and analyze performance bottlenecks.
  - Use the **execution graph** to understand how each step in the query plan contributes to overall execution time.

- **Query History**:
  - View detailed query metadata, such as execution time, resource usage, and query status, using the `QUERY_HISTORY` view or the Web UI.

- **ACCOUNT_USAGE Schema**:
  - Monitor and analyze resource usage, query patterns, and performance metrics across your Snowflake account.

### 16.2. SQL Tuning

SQL tuning involves rewriting queries to improve performance and reduce resource usage. Key practices include:

1. **Avoid SELECT \***:
   - Specify the required columns instead of using `SELECT *` to reduce the amount of data scanned.

2. **Leverage Filters and Joins**:
   - Use `WHERE` and `JOIN` clauses to limit the amount of data retrieved and processed.

3. **Avoid Inefficient Operations**:
   - Avoid nested queries or unoptimized joins when simpler alternatives are available.

4. **Use Temporary Tables**:
   - For complex queries, break them into smaller steps using **temporary tables** or **common table expressions (CTEs)**.

### 16.3. Caching

Caching in Snowflake helps improve query performance by reducing the need to reprocess or fetch data from the storage layer. Snowflake uses the following types of caching:

1. **Result Cache**:
   - Stores the results of a query for **up to 24 hours**.
   - Subsequent identical queries (with the same context) can reuse the cached results, avoiding re-execution.
   - **Key Notes**:
     - Works across all Virtual Warehouses for the same user/account.
     - Results are invalidated if the underlying data changes.

2. **Warehouse Cache**:
   - Stores **raw data** retrieved from the storage layer on the **local SSDs** of the Virtual Warehouse.
   - **Improves performance** for subsequent queries accessing the same data, as the data is read from the local cache instead of the storage layer.
   - **Key Notes**:
     - Cache is **cleared** when the warehouse is resized, suspended, or restarted.

3. **Metadata Cache**:
   - Stores metadata information about table structure, micro-partitions, and query plans.
   - **Improves planning performance** by reducing the time spent on metadata retrieval.

### 16.4. Materialized Views

Materialized Views store **precomputed query results**, which are automatically updated as the underlying data changes. They can significantly improve performance for queries that frequently access the same data.

- **Use Cases**:
  - Ideal for repetitive queries or aggregations over large datasets.

- **Costs**:
  - Materialized views incur **storage costs** for the precomputed data and **compute costs** for background maintenance tasks.

### 16.5. Clustering and Pruning

Snowflake uses **automatic clustering** to organize data into **micro-partitions**, improving query performance by reducing the amount of data scanned.

- **Clustering**:
  - Data is automatically stored in micro-partitions based on query patterns.
  - **Clustering Keys**:
    - For large tables, explicitly defining a clustering key can improve performance for range queries or filtering.

- **Pruning**:
  - **Partition Pruning** minimizes the amount of data scanned by ignoring irrelevant micro-partitions based on query predicates (e.g., `WHERE` clauses).

- **Example**:
  - If a table is clustered by a `date` column, queries filtering on a date range will scan fewer partitions, reducing query execution time.

### 16.6. Search Optimization Service

The **Search Optimization Service** is designed to improve query performance for **point lookups** or queries with selective filters.

- The service creates and maintains additional indexing-like metadata to enable faster lookups.
- It is **optional** and specifically useful for queries that access small subsets of large tables.

Note that enabling the Search Optimization Service incurs additional **compute costs** for maintaining the metadata and **storage costs** for the additional structures.

- **Enable Search Optimization**:
  - Enable the service for specific tables or schemas based on workload requirements.
- **Cost**:
  - The service incurs additional compute costs for maintaining the metadata and storage costs for the additional structures.

```sql
-- Enable the Search Optimization Service for a table
ALTER TABLE my_table
SET SEARCH_OPTIMIZATION = TRUE;

-- Check the status of the Search Optimization Service
SHOW SEARCH OPTIMIZATION ON my_table;

-- Disable the Search Optimization Service
ALTER TABLE my_table
SET SEARCH_OPTIMIZATION = FALSE;
```

### 16.7. Summary of Optimization Techniques

| **Concept**                | **Description**                                                                               |
| -------------------------- | --------------------------------------------------------------------------------------------- |
| **Query Profiler**         | Visual tool to analyze execution plans and identify bottlenecks.                              |
| **SQL Tuning**             | Rewrite queries for better performance (e.g., avoid `SELECT *`, use filters, optimize joins). |
| **Caching**                | Improve query performance using result cache, warehouse cache, and metadata cache.            |
| **Materialized Views**     | Precompute query results for frequently accessed data.                                        |
| **Clustering and Pruning** | Use clustering keys and partition pruning to reduce the amount of data scanned.               |
| **Search Optimization**    | Improve performance for point lookups and selective queries using indexing-like metadata.     |
