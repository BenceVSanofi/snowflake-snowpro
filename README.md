<!-- omit in toc -->
# Snowflake SnowPro Core Certification

This document contains notes and study materials to help you prepare for the **Snowflake SnowPro Core Certification Exam**. It covers key concepts, features, and architecture of Snowflake, as well as practical topics like query optimization, data loading, and performance tuning. The content is designed to serve as a reference for understanding Snowflake’s core functionality.

Snowflake is a cloud-native **data platform** offered as a service (SaaS). It provides key services like **storage**, **compute**, and **management** and runs on the three major cloud providers: AWS, GCP, and Azure. Snowflake’s unique architecture allows it to handle diverse workloads such as data warehousing, data lakes, data engineering, and data science.

<!-- omit in toc -->
## Table of Contents

- [Introduction](#introduction)
- [Features and Architecture](#features-and-architecture)
  - [High-Level Overview](#high-level-overview)
  - [Multi-Cluster Shared Data Architecture](#multi-cluster-shared-data-architecture)
    - [Storage Layer](#storage-layer)
    - [Query Processing or Compute Layer](#query-processing-or-compute-layer)
    - [Services Layer](#services-layer)
- [Editions and Features](#editions-and-features)
- [Object Model](#object-model)
  - [Organization](#organization)
  - [Account](#account)
  - [Database and Schema](#database-and-schema)

## Introduction

This document contains notes and study material for the **SnowPro Core Certification** course available on [Udemy](https://www.udemy.com/share/107srK3@7oBXnZzILRddmCPrD6_gfahyAsy1Vckxeh8lCT-OxVIOmA4F1RrOlQ40YMwFxm9LiA==/). The official Snowflake documentation is available at [Snowflake Docs](https://docs.snowflake.com/en/).

These notes were prepared as part of my study for the *24C21 Snowflake Performance Automation and Tuning* course, available at [Snowflake Performance Automation and Tuning](https://www.snowflake.com/wp-content/uploads/2022/06/Performance-Automation-and-Tuning-3-Day.pdf).

## Features and Architecture

Snowflake (SF) is a SaaS platform that provides services such as storage, compute, and management across AWS, GCP, and Azure. **25% to 30%** of the certification exam will be on this topic. It consists of:

- High-level overview
- Multi-cluster shared data architecture
- Storage, compute, and services overview
- Editions and features
- Object model
- Account billing
- Connectivity

### High-Level Overview

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

### Multi-Cluster Shared Data Architecture

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

#### Storage Layer

- The storage layer is **persistent and infinitely scalable cloud storage**, residing in cloud providers' blob storage services such as AWS S3. Snowflake users, by proxy, benefit from the availability and durability guarantees of the cloud providers' blob storage. Data loaded into Snowflake is organized into databases and schemas and is accessible primarily as tables. All table data is stored in the centralized storage layer, acting as a single source of truth.
- Both structured and semi-structured data files can be loaded and stored in Snowflake.
- When data files are loaded or rows inserted into a table, Snowflake reorganizes the data into its **proprietary compressed, columnar table file format**. Since data is columnar:
  - Only the columns needed for a query are read, reducing the amount of data read from disk (**read optimization**).
  - This columnar structure also improves **data compression**, as columns are more likely to have similar values.
- Data is also **encrypted** at rest and in transit.
- The data that is loaded or inserted is partitioned into what Snowflake calls **micro-partitions**. This allows Snowflake to ignore partitions that are not needed for a query (**partition pruning**).
- **Storage is billed** by how much data is stored, based on a flat rate per TB calculated monthly.
- **Data is not directly accessible** in the underlying blob storage, only via SQL commands.

#### Query Processing or Compute Layer

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

#### Services Layer

The services layer is a collection of **highly available and scalable services** that coordinate activities such as authentication, query optimization, and overall system management across all Snowflake accounts. This **global multi-tenant layer** is responsible for managing the virtual warehouses and the storage layer. It enables Snowflake to avoid creating a separate instance for each account (resulting in **economies of scale**) and facilitates **secure data sharing** between accounts.

Similar to the underlying virtual warehouse resources, the services layer also runs on cloud compute instances. Note that users have **no direct control** over the services layer—it is fully managed by Snowflake.

Services managed by this layer include:

- **Authentication and access control**
- **Infrastructure management**
- **Transaction management** to ensure ACID compliance
- **Metadata management** to store information about databases, tables, and users
- **Query parsing and optimization**
- **Security**

## Editions and Features

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

## Object Model

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

### Organization

Organizations are used to:

- Manage one or more Snowflake accounts.
- Set up and administer Snowflake features that make use of multiple accounts, e.g., database replication and failover.
- Monitor usage across accounts.

To set up an organization, you must contact Snowflake support, provide the organization name, and nominate an account. The `ORGADMIN` role is assigned to that account. The `ORGADMIN` is responsible for managing the lifecycle of accounts and can:

- Create accounts, view organization details, and list regions.
- Enable cross-account features, e.g., set up database replication for an account.
- Monitor account usage by querying the `ORGANIZATION_USAGE` schema in the Snowflake database.

### Account

An **account** is the administrative unit for a collection of storage, compute, and cloud services, deployed and managed entirely on a selected cloud platform. Each account has the following characteristics:

- **Cloud Platform**: Each account is hosted on a single cloud provider (AWS, GCP, or Azure).
- **Region**: Each account is provisioned in a single geographic region (consider regulatory restrictions when moving data between regions).
- **Edition**: Each account is created with a single Snowflake edition (can be updated later).
- **System Role**: An account is created with the system-defined role `ACCOUNTADMIN`, which should be used sparingly. Enforce the principle of least privilege to maintain security.

The **account identifier** is a concatenation of the account locator, region ID, and cloud provider. Alternatively, you can use the concatenation of the organization name and account name.

### Database and Schema

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
