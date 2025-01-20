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
  - [15.2. Virtual Warehouse Properties and Configuration](#152-virtual-warehouse-properties-and-configuration)
  - [15.3. Warehouse State and Properties](#153-warehouse-state-and-properties)

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

Virtual Warehouses can be created via the Snowflake UI or SQL commands.

### 15.2. Virtual Warehouse Properties and Configuration

Below are examples of Virtual Warehouse operations with comments to clarify their purpose:

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

### 15.3. Warehouse State and Properties

Virtual Warehouses have the following states:

- **Started**: The warehouse is running and can execute queries. In this state it consumes credits.
- **Suspended**: The warehouse is stopped and does not consume credits.
- **Resizing**: The warehouse is changing its size. A warehouse can be resized while running.
