<!-- omit in toc -->
# Snowflake SnowPro Core Certification

Snowflake is a cloud-native data platform offered as a service. It provides services like storage, compute, and management, running across the three big cloud providers.

<!-- omit in toc -->
## Table of Contents

- [Introduction](#introduction)
- [Snowflake Features and Architecture](#snowflake-features-and-architecture)
  - [Snowflake High-Level Overview](#snowflake-high-level-overview)
  - [Multi-Cluster Shared Data Architecture](#multi-cluster-shared-data-architecture)
    - [Storage Layer](#storage-layer)
    - [Query Processing Layer](#query-processing-layer)

## Introduction

This document contains notes for the SnowPro Core Certification course [here](https://www.udemy.com/share/107srK3@7oBXnZzILRddmCPrD6_gfahyAsy1Vckxeh8lCT-OxVIOmA4F1RrOlQ40YMwFxm9LiA==/). The Snowflake documentation can be found [here](https://docs.snowflake.com/en/). This course was taken in preparation for the "24C21 Snowflake Performance Automation and Tuning" course, available [here](https://www.snowflake.com/wp-content/uploads/2022/06/Performance-Automation-and-Tuning-3-Day.pdf). The course covers the following topics:

1. Snowflake features and architecture
2. Account access and security
3. Performance concepts: virtual warehouses
4. Performance concepts: query optimization
5. Data loading and unloading
6. Data transformations
7. Storage, data protection, and data sharing

## Snowflake Features and Architecture

Snowflake is a SaaS platform that provides services such as storage, compute, and management across AWS, GCP, and Azure. **25% to 30%** of the certification exam will be on this topic. It consists of:

- Snowflake high-level overview
- Multi-cluster shared data architecture
- Storage, compute, and services overview
- Snowflake editions and features
- Snowflake object model
- Snowflake account billing
- Snowflake connectivity

### Snowflake High-Level Overview

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

#### Query Processing Layer
