# System Design

### Database
There are two types of DB
* SQL      ->
* No SQL   ->
  * key-value DB
  * Documnet DB (JSON Object)
  * GraphDB -> consist of nodes and edges. Relationship of nodes is shown by edges.

#### CAP Theorem -> We cannot achieve all three in a distributed system
* Consistent                -> if we write in one DB, all DB should have same data.
* Availability              -> Every request should get a response, either error or response, even in case of high load.
* Partition Tolerance       -> should be able to partition i.e distributed system. we have a backup server in case of any fault
  ![image](https://github.com/user-attachments/assets/ce4a330f-7214-47ec-bc14-44f9ca800101)

#### ACID

#### BASE


| SQL | NOSQL |
| :---:  | :---: |
| Data Structure| |
| Data is stored in tables with predefined schemas. | Data is stored in various formats such as documents, key-value pairs, wide-column stores, or graphs. |
| Each row must conform to the structure defined by the schema. | Schema is often flexible or schema-less, meaning the structure of data can vary from one record to another. |
| Scalability| |
|Primarily vertically scalable (you scale by increasing the resources of a single machine, like CPU or RAM).|Typically horizontally scalable (you scale by adding more servers or nodes).|
|Some SQL databases (e.g., MySQL, PostgreSQL) support horizontal scaling with sharding, but it's more complex.| Designed to handle large amounts of distributed data. |
| Transactions and ACID Compliance: ||
|Strong support for ACID (Atomicity, Consistency, Isolation, Durability) properties.| Supports BASE (Basically Available, Soft state, Eventual consistency) properties.|
|Ideal for applications requiring reliable transactions (like banking or financial systems).| Transactions are often less strict and optimized for scalability rather than consistency.|
| Performance | |
| Great for structured data and when transactions are critical. May slow down as data grows if not properly optimized. | Optimized for high throughput and large amounts of unstructured or semi-structured data. Generally performs better for read-heavy and write-heavy workloads at scale. |
