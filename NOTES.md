# Snowflake Course Notes

### 1. Total Micro-Partitions vs. Scanned Micro-Partitions

- **Total Micro-Partitions**:
  - Represents the total number of **micro-partitions** available in a table. These are Snowflake's smallest unit of data storage, automatically created and organized during data ingestion.
  - Micro-partitions are optimized for storage and query performance (e.g., range metadata is stored for fast partition pruning).

- **Scanned Micro-Partitions**:
  - The number of **micro-partitions actually scanned** during query execution.
  - Snowflake uses **partition pruning** to minimize the number of micro-partitions scanned, based on query filters and clustering.
  - **Optimization**: Fewer micro-partitions scanned indicates better query performance, as irrelevant partitions are skipped.

### 2. Percentage Scanned from the Data Cache

- **Data Cache**:
  - Snowflake's **warehouse cache** stores recently queried data (on local SSDs) to improve query performance.
  - Queries that access data in the cache avoid retrieving data from the storage layer, leading to faster execution.

- **Percentage Scanned from the Cache**:
  - Indicates how much of the query’s data scan was served from the **warehouse cache** versus the storage layer.
  - A **high percentage** from the cache is desirable, as it means fewer expensive reads from the storage layer.

- **Best Practices**:
  - Use a **persistent Virtual Warehouse** for frequently accessed datasets to maximize cache hits.
  - Avoid resizing or suspending warehouses unnecessarily, as it clears the cache.

### 3. Spilling to Local or Remote Storage

- **Spilling**:
  - Occurs when a query requires more memory than is available in the **warehouse’s allocated memory**.
  - Snowflake temporarily writes (or "spills") intermediate data to **local storage** (on SSDs) or **remote storage** (in Snowflake’s storage layer).

- **Local Storage Spilling**:
  - Data is spilled to the local SSDs of the Virtual Warehouse.
  - Typically faster than remote storage because it avoids network latency.

- **Remote Storage Spilling**:
  - If local storage is insufficient, Snowflake spills data to its central storage layer.
  - Slower than local spilling due to increased I/O and network transfer times.

- **Mitigation**:
  - To reduce spilling:
    - Use appropriately sized warehouses for queries with high memory requirements.
    - Optimize query logic (e.g., avoid unnecessary joins or large intermediate results).

### Key Takeaways

| **Concept**                            | **Explanation**                                                                                      | **Optimization Tips**                                                                                             |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Total vs. Scanned Micro-Partitions** | Total is the full number of partitions in the table, while scanned is the subset used for the query. | Use **filters** and **clustering keys** to minimize scanned micro-partitions.                                     |
| **Data Cache Percentage**              | The proportion of data served from the cache instead of the storage layer.                           | Use persistent warehouses and avoid frequent suspensions or resizes to retain cached data.                        |
| **Spilling to Storage**                | Temporary storage of intermediate query results to local or remote storage when memory is low.       | Resize the warehouse or optimize queries to avoid spilling. Prefer local spilling over remote spilling for speed. |

Let me know if you'd like additional examples or more details on any of these topics!
