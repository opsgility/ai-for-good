# Problem Analysis  

Understanding ecology though tracking animal populations.  Search out and aggregate animal population data.  

### Overview  

You are the leader of a group of climate scientists who are concerned about the dwindling polar-bear population in the Arctic. As such, your team has placed hundreds of motion-activated cameras at strategic locations throughout the region. Rather than manually examine each photograph to determine whether it contains a polar bear, you have been challenged to devise an automated system that processes data from these cameras in real time and displays an alert on a map when a polar bear is photographed.  

You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. And you need it fast, because climate change won't wait.  

### Learning Objectives  

Over the next few lessons you will learn to:  

- Configure real-time stream ingestion using Azure IoT Hub  
- Analyze real-time streaming data using Azure Stream Analytics  
- Predict results using Azure Functions and Cognitive Services  
- Visualize output in real-time using Azure SQL Database and Power BI  

## Ingesting Streaming Data with IoT Hub ##  

In this lesson, you will create a storage account and an IoT hub and write a [Node.js](https://nodejs.org/) app that connects them to a simulated camera array. Then you will test one of the virtual cameras by having it upload a photograph to blob storage and transmit an event containing the blob URL and other information to the IoT hub.  

### Objectives ###  

In this lesson, you will learn how to:  

- Create an Azure storage account  
- Create an Azure IoT hub  
- Make authenticated calls to an Azure IoT hub  
- Upload images to Azure blob storage from an app or device  

# Data Storage in Azure ###  

## Blob Storage  

Blob storage is a cloud based object store used for raw data storage.  

## Data Lake Storage  

A data lake is a storage repository that holds a large amount of data in its native, raw format. [Azure Data Lake](https://azure.microsoft.com/en-us/services/data-lake-analytics/) stores are optimized for scaling to terabytes and petabytes of data. The data typically comes from multiple heterogeneous sources, and may be structured, semi-structured, or unstructured. The idea with a data lake is to store everything in its original, untransformed state. This approach differs from a traditional data warehouse, which transforms and processes the data at the time of ingestion.  

Advantages of a data lake:  

- Data is never thrown away, because the data is stored in its raw format. This is especially useful in a big data environment, when you may not know in advance what insights are available from the data.  
- Users can explore the data and create their own queries.  
- May be faster than traditional ETL tools.  
- More flexible than a data warehouse, because it can store unstructured and semi-structured data.  

A complete data lake solution consists of both storage and processing. Data lake storage is designed for fault-tolerance, infinite scalability, and high-throughput ingestion of data with varying shapes and sizes. Data lake processing involves one or more processing engines built with these goals in mind, and can operate on data stored in a data lake at scale.  

## When to use a data lake  

Typical uses for a data lake include data exploration, data analytics, and machine learning.  

A data lake can also act as the data source for a data warehouse. With this approach, the raw data is ingested into the data lake and then transformed into a structured queryable format. Typically this transformation uses an `Extract, Load, and Transform` process [(ELT) data loading](https://docs.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading), where the data is ingested and transformed in place. Source data that is already relational may go directly into the data warehouse, using an `Extract, Transform, and Load` [(ETL) process](extract, transform, and Load` [(ETL) pipeline](https://databricks.com/glossary/etl-pipeline), skipping the data lake.  

Data lake stores are often used in event streaming or IoT scenarios, because they can persist large amounts of relational and nonrelational data without transformation or schema definition. They are built to handle high volumes of small writes at low latency, and are optimized for massive throughput.  

## Challenges

- Lack of a schema or descriptive metadata can make the data hard to consume or query.  
- Lack of semantic consistency across the data can make it challenging to perform analysis on the data, unless users are highly skilled at data analytics.  
- It can be hard to guarantee the quality of the data going into the data lake.  
- Without proper governance, access control and privacy issues can be problems. What information is going into the data lake, who can access that data, and for what uses?  
- A data lake may not be the best way to integrate data that is already relational.  
- By itself, a data lake does not provide integrated or holistic views across the organization.  
- A data lake may become a dumping ground for data that is never actually analyzed or mined for insights.  

## Relevant Azure services  

- [Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/) is a hyperscale, Hadoop-compatible repository.  

- [Azure Databricks](https://azure.microsoft.com/en-us/services/databricks/)  

## Relational Databases  

Relational databases store data in tables made up of rows and columns. The data stored in relational databases is generally transactional data. Azure supports various types of relational databases such as Azure SQL Database and MySQL. Data stored in a relational database is considered structured data. Structured data is data that has a fixed schema. When working with structured data the developer must know the schema.   

Transactional data is information that tracks the interactions related to an organization's activities. These interactions are typically business transactions, such as payments received from customers, payments made to suppliers, products moving through inventory, orders taken, or services delivered. Transactional events, which represent the transactions themselves, typically contain a time dimension, some numerical values, and references to other data.  

Transactions typically need to be atomic and consistent. Atomicity means that an entire transaction always succeeds or fails as one unit of work, and is never left in a half-completed state. If a transaction cannot be completed, the database system must roll back any steps that were already done as part of that transaction. In a traditional RDBMS, this rollback happens automatically if a transaction cannot be completed. Consistency means that transactions always leave the data in a valid state. (These are very informal descriptions of atomicity and consistency. There are more formal definitions of these properties, such as [ACID](https://social.technet.microsoft.com/wiki/contents/articles/51484.sql-server-concurrency-control-models-acid-properties-and-transaction-isolation-levels.aspx).)  

Transactional databases can support strong consistency for transactions using various locking strategies, such as pessimistic locking, to ensure that all data is strongly consistent within the context of the enterprise, for all users and processes.  

The most common deployment architecture that uses transactional data is the data store tier in a 3-tier architecture. A 3-tier architecture typically consists of a presentation tier, business logic tier, and data store tier. A related deployment architecture is the N-tier architecture, which may have multiple middle-tiers handling business logic.  

#### *Typical traits of transactional data*

Transactional data tends to have the following traits:  

| Requirement | Description |
| --- | --- |
| Normalization | Highly normalized |
| Schema | Schema on write, strongly enforced|
| Consistency | Strong consistency, ACID guarantees |
| Integrity | High integrity |
| Uses transactions | Yes |
| Locking strategy | Pessimistic or optimistic|
| Updateable | Yes |
| Appendable | Yes |
| Workload | Heavy writes, moderate reads |
| Indexing | Primary and secondary indexes |
| Datum size | Small to medium sized |
| Model | Relational |
| Data shape | Tabular |
| Query flexibility | Highly flexible |
| Scale | Small (MBs) to Large (a few TBs) |

#### *When to use this solution*  

Choose OLTP when you need to efficiently process and store business transactions and immediately make them available to client applications in a consistent way. Use this architecture when any tangible delay in processing would have a negative impact on the day-to-day operations of the business.  

OLTP systems are designed to efficiently process and store transactions, as well as query transactional data. The goal of efficiently processing and storing individual transactions by an OLTP system is partly accomplished by data normalization &mdash; that is, breaking the data up into smaller chunks that are less redundant. This supports efficiency because it enables the OLTP system to process large numbers of transactions independently, and avoids extra processing needed to maintain data integrity in the presence of redundant data.  

#### *Challenges*  

Implementing and using an OLTP system can create a few challenges:  

- OLTP systems are not always good for handling aggregates over large amounts of data, although there are exceptions, such as a well-planned SQL Server-based solution. Analytics against the data, that rely on aggregate calculations over millions of individual transactions, are very resource intensive for an OLTP system. They can be slow to execute and can cause a slow-down by blocking other transactions in the database.  
- When conducting analytics and reporting on data that is highly normalized, the queries tend to be complex, because most queries need to de-normalize the data by using joins. Also, naming conventions for database objects in OLTP systems tend to be terse and succinct. The increased normalization coupled with terse naming conventions makes OLTP systems difficult for business users to query, without the help of a DBA or data developer.  
- Storing the history of transactions indefinitely and storing too much data in any one table can lead to slow query performance, depending on the number of transactions stored. The common solution is to maintain a relevant window of time (such as the current fiscal year) in the OLTP system and offload historical data to other systems, such as a data mart or [data warehouse](https://docs.microsoft.com/en-us/azure/architecture/data-guide/relational-data/data-warehousing).  

#### *OLTP in Azure*  

Applications such as websites hosted in [App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview), REST APIs running in App Service, or mobile or desktop applications communicate with the OLTP system, typically via a REST API intermediary.  

In practice, most workloads are not purely OLTP. There tends to be an analytical component as well. In addition, there is an increasing demand for real-time reporting, such as running reports against the operational system. This is also referred to as HTAP (Hybrid Transactional and Analytical Processing). For more information, see [Online Analytical Processing (OLAP)](./online-analytical-processing.md).  

In Azure, all of the following data stores will meet the core requirements for OLTP and the management of transaction data:  

- [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)  
- [SQL Server in an Azure virtual machine](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=/azure/virtual-machines/windows/toc.json)  
- [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/)  
- [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)  

#### *Key selection criteria*

To narrow the choices, start by answering these questions:  

- Do you want a managed service rather than managing your own servers?  

- Does your solution have specific dependencies for Microsoft SQL Server, MySQL or PostgreSQL compatibility? Your application may limit the data stores you can choose based on the drivers it supports for communicating with the data store, or the assumptions it makes about which database is used.  

- Are your write throughput requirements particularly high? If yes, choose an option that provides in-memory tables.  

- Is your solution multitenant? If so, consider options that support capacity pools, where multiple database instances draw from an elastic pool of resources, instead of fixed resources per database. This can help you better distribute capacity across all database instances, and can make your solution more cost effective.  

- Does your data need to be readable with low latency in multiple regions? If yes, choose an option that supports readable secondary replicas.  

- Does your database need to be highly available across geo-graphic regions? If yes, choose an option that supports geographic replication. Also consider the options that support automatic failover from the primary replica to a secondary replica.  

- Does your database have specific security needs? If yes, examine the options that provide capabilities like row level security, data masking, and transparent data encryption.  

# Non-Relational & NoSQL Data Stores

NoSQL data stores are designed to store any data that is not structured. NoSQL data can be either unstructured data such as binary data like images, videos or Word documents or semi-structured such as JSON. The difference between these two is that semi-structured data is self-describing, meaning that the schema is part of the file format.  

Semi-structured data is commonly stored in a document, these documents are generally stored in a common format such as JSON. JSON file formatting allows the developer to define the schema of the data from within the application as opposed to having the database enforce the schema. It is important to point out that semi-structured data is not schema-less, you have merely shifted the responsibility for maintaining and enforcing schema compliance to the application.  

A *non-relational database* is a database that does not use the tabular schema of rows and columns found in most traditional database systems. Instead, non-relational databases use a storage model that is optimized for the specific requirements of the type of data being stored. For example, data may be stored as simple key/value pairs, as JSON documents, or as a graph consisting of edges and vertices.  

What all of these data stores have in common is that they don't use a [relational model](https://docs.microsoft.com/en-us/learn/modules/describe-concepts-of-relational-data/). Also, they tend to be more specific in the type of data they support and how data can be queried. For example, time series data stores are optimized for queries over time-based sequences of data, while graph data stores are optimized for exploring weighted relationships between entities. Neither format would generalize well to the task of managing transactional data.  

The term *NoSQL* refers to data stores that do not use SQL for queries, and instead use other programming languages and constructs to query the data. In practice, "NoSQL" means "non-relational database," even though many of these databases do support SQL-compatible queries. However, the underlying query execution strategy is usually very different from the way a traditional RDBMS would execute the same SQL query.  

The following sections describe the major categories of non-relational or NoSQL database.  

## Document data stores  

A document data store manages a set of named string fields and object data values in an entity referred to as a *document*. These data stores typically store data in the form of JSON documents. Each field value could be a scalar item, such as a number, or a compound element, such as a list or a parent-child collection. The data in the fields of a document can be encoded in a variety of ways, including XML, YAML, JSON, BSON, or even stored as plain text. The fields within documents are exposed to the storage management system, enabling an application to query and filter data by using the values in these fields.  

Typically, a document contains the entire data for an entity. What items constitute an entity are application-specific. For example, an entity could contain the details of a customer, an order, or a combination of both. A single document might contain information that would be spread across several relational tables in a relational database management system (RDBMS). A document store does not require that all documents have the same structure. This free-form approach provides a great deal of flexibility. For example, applications can store different data in documents in response to a change in business requirements.  

[Example document data store](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview#document-databases)  

The application can retrieve documents by using the document key. This is a unique identifier for the document, which is often hashed, to help distribute data evenly. Some document databases create the document key automatically. Others enable you to specify an attribute of the document to use as the key. The application can also query documents based on the value of one or more fields. Some document databases support indexing to facilitate fast lookup of documents based on one or more indexed fields.  

Many document databases support in-place updates, enabling an application to modify the values of specific fields in a document without rewriting the entire document. Read and write operations over multiple fields in a single document are typically atomic.  

Relevant Azure services:  

- [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)  

- [Azure Cosmos MongoDB](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-introduction)  


## Columnar data stores  

A columnar or column-family data store organizes data into columns and rows. In its simplest form, a column-family data store can appear very similar to a relational database, at least conceptually. The real power of a column-family database lies in its denormalized approach to structuring sparse data, which stems from the column-oriented approach to storing data.  

You can think of a column-family data store as holding tabular data with rows and columns, but the columns are divided into groups known as column families. Each column family holds a set of columns that are logically related and are typically retrieved or manipulated as a unit. Other data that is accessed separately can be stored in separate column families. Within a column family, new columns can be added dynamically, and rows can be sparse (that is, a row doesn't need to have a value for every column).  

The following link shows an example with two column families, `Identity` and `Contact Info`. The data for a single entity has the same row key in each column family. This structure, where the rows for any given object in a column family can vary dynamically, is an important benefit of the column-family approach, making this form of data store highly suited for storing data with varying schemas.  

[Example of column-family data](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-introduction)  

Unlike a key/value store or a document database, most column-family databases physically store data in key order, rather than by computing a hash. The row key is considered the primary index and enables key-based access via a specific key or a range of keys. Some implementations allow you to create secondary indexes over specific columns in a column family. Secondary indexes let you retrieve data by columns value, rather than row key.  

On disk, all of the columns within a column family are stored together in the same file, with a certain number of rows in each file. With large data sets, this approach creates a performance benefit by reducing the amount of data that needs to be read from disk when only a few columns are queried together at a time.  

Read and write operations for a row are typically atomic within a single column family, although some implementations provide atomicity across the entire row, spanning multiple column families.  

Relevant Azure services:  

- [Cosmos DB Cassandra API](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)  

- [HBase in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hbase-overview)  

## Key/value data stores

A key/value store is essentially a large hash table. You associate each data value with a unique key, and the key/value store uses this key to store the data by using an appropriate hashing function. The hashing function is selected to provide an even distribution of hashed keys across the data storage.  

Most key/value stores only support simple query, insert, and delete operations. To modify a value (either partially or completely), an application must overwrite the existing data for the entire value. In most implementations, reading or writing a single value is an atomic operation. If the value is large, writing may take some time.  

An application can store arbitrary data as a set of values, although some key/value stores impose limits on the maximum size of values. The stored values are opaque to the storage system software. Any schema information must be provided and interpreted by the application. Essentially, values are blobs and the key/value store simply retrieves or stores the value by key.  

[Example of data in a key/value store](https://docs.microsoft.com/en-us/azure/hdinsight/hbase/apache-hbase-overview)  

Key/value stores are highly optimized for applications performing simple lookups using the value of the key, or by a range of keys, but are less suitable for systems that need to query data across different tables of keys/values, such as joining data across multiple tables.

Key/value stores are also not optimized for scenarios where querying or filtering by non-key values is important, rather than performing lookups based only on keys. For example, with a relational database, you can find a record by using a WHERE clause to filter the non-key columns, but key/values stores usually do not have this type of lookup capability for values, or if they do, it requires a slow scan of all values.

A single key/value store can be extremely scalable, as the data store can easily distribute data across multiple nodes on separate machines.

Relevant Azure services:  

- [Azure Cosmos DB Table API](https://docs.microsoft.com/azure/cosmos-db/table-introduction)  
- [Azure Cache for Redis](https://azure.microsoft.com/services/cache/)  
- [Azure Table Storage](https://azure.microsoft.com/services/storage/tables/)  

## Graph data stores  

A graph data store manages two types of information, nodes and edges. Nodes represent entities, and edges specify the relationships between these entities. Both nodes and edges can have properties that provide information about that node or edge, similar to columns in a table. Edges can also have a direction indicating the nature of the relationship.  

The purpose of a graph data store is to allow an application to efficiently perform queries that traverse the network of nodes and edges, and to analyze the relationships between entities. The following diagram shows an organization's personnel data structured as a graph. The entities are employees and departments, and the edges indicate reporting relationships and the department in which employees work. In this graph, the arrows on the edges show the direction of the relationships.  

![Example of data in a graph data store](../../guide/technology-choices/images/graph.png)  

This structure makes it straightforward to perform queries such as "Find all employees who report directly or indirectly to Sarah" or "Who works in the same department as John?" For large graphs with lots of entities and relationships, you can perform complex analyses quickly. Many graph databases provide a query language that you can use to traverse a network of relationships efficiently.  

Relevant Azure service:  

- [Azure Cosmos DB Graph API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)  

## Time series data stores  

Time series data is a set of values organized by time, and a time series data store is optimized for this type of data. Time series data stores must support a very high number of writes, as they typically collect large amounts of data in real time from a large number of sources. Time series data stores are optimized for storing telemetry data. Scenarios include IoT sensors or application/system counters. Updates are rare, and deletes are often done as bulk operations.  

[Time series data](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview#time-series-databases)  

Although the records written to a time series database are generally small, there are often a large number of records, and total data size can grow rapidly. Time series data stores also handle out-of-order and late-arriving data, automatic indexing of data points, and optimizations for queries described in terms of windows of time. This last feature enables queries to run across millions of data points and multiple data streams quickly, in order to support time series visualizations, which is a common way that time series data is consumed.  

Relevant Azure services:  

- [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)  

- [OpenTSDB with HBase on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hbase-overview)

## Object data stores  

Object data stores are optimized for storing and retrieving large binary objects or blobs such as images, text files, video and audio streams, large application data objects and documents, and virtual machine disk images. An object consists of the stored data, some metadata, and a unique ID for accessing the object. Object stores are designed to support files that are individually very large, as well provide large amounts of total storage to manage all files.  

[Object Data Storage](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview#time-series-databases)  

Some object data stores replicate a given blob across multiple server nodes, which enables fast parallel reads. This in turn enables the scale-out querying of data contained in large files, because multiple processes, typically running on different servers, can each query the large data file simultaneously.

One special case of object data stores is the network file share. Using file shares enables files to be accessed across a network using standard networking protocols like server message block (SMB). Given appropriate security and concurrent access control mechanisms, sharing data in this way can enable distributed services to provide highly scalable data access for basic, low-level operations such as simple read and write requests.

Relevant Azure services:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)  
- [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)  

- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)  

## External index data stores

External index data stores provide the ability to search for information held in other data stores and services. An external index acts as a secondary index for any data store, and can be used to index massive volumes of data and provide near real-time access to these indexes.  

For example, you might have text files stored in a file system. Finding a file by its file path is quick, but searching based on the contents of the file would require a scan of all of the files, which is slow. An external index lets you create secondary search indexes and then quickly find the path to the files that match your criteria. Another example application of an external index is with key/value stores that only index by the key. You can build a secondary index based on the values in the data, and quickly look up the key that uniquely identifies each matched item.  

[Search Engine Data](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/data-store-overview#time-series-databases)

The indexes are created by running an indexing process. This can be performed using a pull model, triggered by the data store, or using a push model, initiated by application code. Indexes can be multidimensional and may support free-text searches across large volumes of text data.

External index data stores are often used to support full text and web-based search. In these cases, searching can be exact or fuzzy. A fuzzy search finds documents that match a set of terms and calculates how closely they match. Some external indexes also support linguistic analysis that can return matches based on synonyms, genre expansions (for example, matching "dogs" to "pets"), and stemming (for example, searching for "run" also matches "ran" and "running").

Relevant Azure service:  

- [Azure Search](https://azure.microsoft.com/services/search/)  

## Typical requirements  

Non-relational data stores often use a different storage architecture from that used by relational databases. Specifically, they tend toward having no fixed schema. Also, they tend not to support transactions, or else restrict the scope of transactions, and they generally don't include secondary indexes for scalability reasons.  

## Data Ingestion  

Data ingestion is the process used to get the data into the Azure Data Explorer, where it can then be queried.  There are two main types of data ingestion, batch and streaming.

### Batch Ingestion  

Batch ingestion optimizes throughput for high-volume and performant ingestion.  This can also provide performance improvements in query results.  

The default batching values are set to 5 minutes, 1000 items, or 500MB.  

### Streaming Ingestion  

Streaming ingestion is real-time, ongoing data ingestion from various sources such as Event Hub and IoT Hub.  Generally useful for smaller sets of data, the data is placed in a row store and eventually moved to column store extents.  Using a tool like Stream Analytics, you can quickly query your live stream data for the results you need.

[Batching vs. Streaming ingestion](https://docs.microsoft.com/en-us/azure/data-explorer/ingest-data-overview#batching-vs-streaming-ingestion)    

## Batch Processing  

Big data solutions often use long-running batch jobs to filter, aggregate, and otherwise prepare the data for analysis. Usually these jobs involve reading source files from scalable storage (like HDFS, Azure Data Lake Store, and Azure Storage), processing them, and writing the output to new files in scalable storage.  

The key requirement of such batch processing engines is the ability to scale out computations, in order to handle a large volume of data. Unlike real-time processing, however, batch processing is expected to have latencies (the time between data ingestion and computing a result) that measure in minutes to hours.  

## Technology choices for batch processing  

Big data solutions need to be able to scale out computations over long-running jobs.  There are a number of tools available in Azure to assist with Batch Processing.  

### Azure Synapse Analytics  

[Azure Synapse](https://docs.microsoft.com/azure/sql-data-warehouse/) is a distributed system designed to perform analytics on large data. It supports massive parallel processing (MPP), which makes it suitable for running high-performance analytics. Consider Azure Synapse when you have large amounts of data (more than 1 TB) and are running an analytics workload that will benefit from parallelism.  

### Azure Data Lake Analytics  

[Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-overview) is an on-demand analytics job service. It is optimized for distributed processing of very large data sets stored in Azure Data Lake Store.  

- Languages: [U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started) (including Python, R, and C# extensions).  

- Integrates with Azure Data Lake Store, Azure Storage blobs, Azure SQL Database, and Azure Synapse.  

- Pricing model is per-job.  

### HDInsight  

HDInsight is a managed Hadoop service. Use it deploy and manage Hadoop clusters in Azure. For batch processing, you can use [Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview), [Hive](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-hive), [Hive LLAP](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started), [MapReduce](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-mapreduce).  

- Languages: R, Python, Java, Scala, SQL  

- Kerberos authentication with Active Directory, Apache Ranger based access control  

- Gives you full control of the Hadoop cluster  

### Azure Databricks  

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) is an Apache Spark-based analytics platform. You can think of it as "Spark as a service." It's the easiest way to use Spark on the Azure platform.  

- Languages: R, Python, Java, Scala, Spark SQL  

- Fast cluster start times, autotermination, autoscaling.  

- Manages the Spark cluster for you.  

- Built-in integration with Azure Blob Storage, Azure Data Lake Storage (ADLS), Azure Synapse, and other services. See [Data Sources](https://docs.azuredatabricks.net/data/data-sources/index.html#data-sources).  

- User authentication with Azure Active Directory.  

- Web-based [notebooks](https://docs.azuredatabricks.net/notebooks/index.html#notebooks) for collaboration and data exploration.  

- Supports [GPU-enabled clusters](https://docs.azuredatabricks.net/clusters/gpu.html#gpu-enabled-clusters)  

### Azure Distributed Data Engineering Toolkit  

The [Distributed Data Engineering Toolkit](https://github.com/azure/aztk) (AZTK) is a tool for provisioning on-demand Spark on Docker clusters in Azure.  

AZTK is not an Azure service. Rather, it's a client-side tool with a CLI and Python SDK interface, that's built on Azure Batch. This option gives you the most control over the infrastructure when deploying a Spark cluster.  

- Bring your own Docker image.  
- Use low-priority VMs for an 80% discount.  
- Mixed mode clusters that use both low-priority and dedicated VMs.  
- Built in support for Azure Blob Storage and Azure Data Lake connection.  

## Key selection criteria  

To narrow the choices, start by answering these questions:  

- Do you want a managed service rather than managing your own servers?  

- Do you want to author batch processing logic declaratively or imperatively?  

- Will you perform batch processing in bursts? If yes, consider options that let you auto-terminate the cluster or whose pricing model is per batch job.  

- Do you need to query relational data stores along with your batch processing, for example to look up reference data? If yes, consider the options that enable querying of external relational stores.  

## Stream Processing  

Azure provides a number of options when it comes to messaging solutions.  Within the messaging ecosystem, there are tools available to query, filter, and aggregate information from messages.  

The key requirement of such streaming processing engines is the ability to process multiple perpetual streams of data as efficiently as possible.  

## Technology choices for stream processing  

In order to work with queues and messages, a number of tools and products are available in Azure for working with large data streams.  

-   [Azure Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics)  

-   [Azure Databricks](https://azure.microsoft.com/en-us/services/databricks/)  

-   [Apache Hadoop, Spark, and Kafka](https://azure.microsoft.com/en-us/free/hdinsight)  

-   [Apache Storm in Azure](https://docs.microsoft.com/en-us/azure/hdinsight/storm/apache-storm-quickstart)  

-   [Azure Functions](https://azure.microsoft.com/en-us/services/functions/)  

-   [Azure WebJobs](https://docs.microsoft.com/en-us/azure/app-service/webjobs-dotnet-deploy-vs)  

Review [This Guide](https://docs.microsoft.com/en-us/azure/architecture/data-guide/technology-choices/stream-processing) for more information on technology choices for stream processing.  