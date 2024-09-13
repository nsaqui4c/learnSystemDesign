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
| <span style="color:red">Data Structure</span>| |
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



#### When to Use SQL:
**Structured Data:**

* Your data is structured and can fit into predefined schemas (tables with rows and columns).
* Example: Financial systems, inventory management, and enterprise resource planning (ERP).
  
**ACID Transactions:**

* You need strict consistency, and transactions are crucial.
* Example: Banking systems, order management, and any application where data integrity is critical.
  
**Complex Queries:**

* Your application requires complex joins, aggregations, and subqueries.
* Example: Data analytics platforms, reporting systems.
  
**Long-term Consistency:**

* If consistency is more important than availability, especially in smaller systems where scalability is not the top priority.
* Example: Centralized databases for small to medium-sized applications.
#### When to Use NoSQL:
**Unstructured or Semi-Structured Data:**

* Your data doesn’t fit well into tables (e.g., documents, JSON, multimedia).
* Example: Content management systems, document storage, real-time web apps.
  
**High Scalability:**

* You need to scale horizontally to handle massive amounts of data across distributed systems.
* Example: Big data applications, social media platforms, IoT data storage.
  
**Flexible Schema:**

* Your data structure is constantly evolving, and you need the ability to quickly adjust the schema.
* Example: Startups or fast-growing apps that require agility in development.
  
**Eventual Consistency:**

* If your system can tolerate eventual consistency, NoSQL databases can offer high availability.
* Example: Social media feeds, recommendation engines, real-time analytics, or any application where speed is prioritized over strong consistency.
  
**Summary:**
* Use SQL for structured data, complex transactions, and applications needing ACID compliance.
* Use NoSQL for large-scale, distributed systems with flexible schema, unstructured data, or where high availability and horizontal scalability are more important than strict consistency.



### Sharding
 Database partioning into smaller, faster and more easily manged part.
* Sharding Technique
  * Horizontal partitioning      -> range based partitioning   -> We store data according user ID-> first partition will have user 1-100, then 101-200 and so on.
  * Vertical partitioning        -> feature based partitioning -> DB for userProfile, DB for friend list, DB for images
  * Directory based partitioning -> create a directory server which will have mapping of data and its DB-> first lookup directory to find the DB and then lookup data in that DB
    * most commonly used.
    * We can add more DB easily, as we can update directory server
  * Hashed Based partition       -> we generate a hash function and accordingly decide, in which DB to save data.
     * hash function is tightly coupled with number of DB
     * If DB increase or decrease we need to change function.
   
Problem :
1) Joins and Denormalization are difficult
2) Rebalancing in case on DB is highly utilized, because of incorrect sharding criteria
