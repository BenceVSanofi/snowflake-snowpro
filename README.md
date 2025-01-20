# Snowflake SnowPro Core Certification

Notes for SnowPro Core Certification course [here](https://www.udemy.com/share/107srK3@7oBXnZzILRddmCPrD6_gfahyAsy1Vckxeh8lCT-OxVIOmA4F1RrOlQ40YMwFxm9LiA==/). The Snowflake docs can be found [here](https://docs.snowflake.com/en/). This course was taken in preparation for the "24C21 Snowflake Performance Automation and Tuning" course found [here](https://www.snowflake.com/wp-content/uploads/2022/06/Performance-Automation-and-Tuning-3-Day.pdf).

1. Snowflake features and architecture
2. Account access and security
3. Performance concepts: virtual warehouses
4. Performance concepts: query optimization
5. Data loading and unloading
6. Data transformations
7. Storage, data protection, and data sharing

Snowflake is a SaaS platform. It provides services like storage, compute, and management running across the three big cloud providers.

## Snowflake Features and Architecture

25% to 30% of the certification exam will be on this topic. It consists of:

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

1. Shared-disk
2. Shared-nothing
