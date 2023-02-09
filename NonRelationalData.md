# Non-Relational Data in Azure

******************************

### Azure Storage for Non-relational Data

One of the core services in Microsoft Azure, offers a range of options for storing data in the cloud.

#### Azure blob storage

Service to store unstructure data as binary large objects, or blobs. Efficient way to store data files in a format optimized for cloud-based storage, applications can read and write them by using the Azure blob storage API.
In a Azure storage account, you store blobs in containers, which is a way of grouping related blobs together and control who can read and write blobs inside a container.

![blob container](img/blob-container.jpg)

A container can organize blobs in a hierarchy of virtual folders, similar to files in a file system on disk. By default, these folders are simply a way of using a "/" character in a blob name to organize the blobs into namespaces. Folders are purely virtual, and can't perform folder-level operations to control access or perform bulk operations.

Azure blob storage supports three types of blob:

- Block blobs: is handled as a set o blocks; each block can vary in size, up to 100MB; each block blob can contain up to 50,000 blocks. giving a maximum size of 4.7TB. The block is the smallest amount of data that can be read or written as an individual unit, you can change the content of a block blob by uploading new blocks, but cannot modify existing blocks. Used to store large, binary objects that change infrequently. Optimized for uploading and download large files.
- Page blobs: is organized as a collection of fixed size 512-byte pages; can fetch, store and modify the content by writing to individual pages if necessary, but you cannot delete or change existing pages. Page blobs have a maximum size of 8 TB. Optimized for read and write operations, not for large data transfers. Azure uses page blobs to implement virtual disk storage for virtual machines.
- Append blobs: is a block blob optimized to support append operations. Can only add blocks to the end of an append blob; updating or deleting existing blocks ins't supported. Each block can vary in size, up to 4 MB; maximum size of an append blob is 195GB.

Blob storage provides three access tiers which help to balance access latency and storage cost:

- Hot tier: is the default, used for blobs that are accessed frequently; blob data is stored on high-performance media.
- Cool tier: lower performance and reduced storage price compared to Hot tier; used for data that is accessed infrequently. It's commom for newly created files to be accessed frequently initially, but less so as time passes. In these situations, create a blob in the Hot tier, and migrate to Cool tier later. Blobs can be migrated from Cool tier back to Hot tier.
- Archive tier: provides the lowest storage cost, but with increased latency. Intended for historical data, that mustn't be lost, but is requiled to only rarely. Archive tier is stored in an offline state. Tipycal latency for Hot and Cool tiers are a few miliseconds, for Archive tier it can take hours. To retrieve data from a Archive tier, you must change the access tier to Hot or Cool, the blob will be reehydrated, you can read the blob only when the rehydration process is completed.

You can create lifecycle management policies to automatically move a blob from Hot to Cool, and then to the Archive tier, as it ages and is used less frequently (policy is based on the number of days since modification). Lifecycle management policy can also delete outdated blobs.

#### Azure Data Lake Storage Gen2

Azure Data Lake Store (Gen1) is a separate service for hierarchical data storage for analytical data lakes, often called big data analytical solutions that work with structured, semi-structured and unstructured data stored in files. Azure Data Lake Storage Gen2 is a newer version of this service integrated into Azure Storage, taking advantage of the scalability of blob storage and the cost-control of storage tiers, combined with the hierarchical file system capabilities and compatibility with major analytics systems of Azure Data Lake Store.
Services in Azure HDInsight, Azure Databricks, and Azure Synapse Analytics can mount a distributed file system hosted in Azure Data Lake Storage Gen2 and use it to process huge volumes of data.

![data lake gen2](img/data-lake-gen2.jpg)

To create an Azure Data Lake Storage Gen2 file system, you must enable the Hierarchical Namespace option of an Azure Storage Account. You can do this initially creating the storage account, or you can upgrade an existing Azure Storage Account to support Data Lake Gen2, but be aware that upgrading is a one-way-process, after upgrading a storage account to support hierarchical namespace for blob storage, you can't revert it to a flat namespace.

#### Azure Files
A way to create cloud-based network shares, such as typically found in on-premises organizations to make documents and other files available to multiple users, eliminating hardware costs and maintenance overhead, with the benefit of hight availability and scalable storage.
Enables to share up to 100TB of data in a single storage account, this data can be distributed across any number of file shares in the account. The maximum size of a single file is 1TB, can define quotas to limit the size of each share. Supports up to 2000 concurrent connections per shared file.

![azure file storage](img/az-files.jpg)

You can upload files to Azure File Storage using the Azure portal, or tools such as AzCopy, or can also use the Azure File Sync service to synchronize locally cached copies of shared files with the data in Azure File Storage

Provides two performance tiers:

- Standard: uses hard disk-based hardware
- Premium: uses solid-state disks hardware. Offers greater throughput bit higher price.

Azure Files supports two network file sharing protocols:

- Server Message Block (SMB): used across multiple operating systems (Windows, Linux, macOS).
- Network File System (NFS) used by some Linux and macOS versions. To create an NFS share, you must use a premium tier storage account and create and configure a virtual nework through which access to the share can be controlled.

#### Azure Tables
NoSQL storage solution that makes use of tables containing key/value data items. Each item is represented by a row that contains colums for the data fields that need to be stored.
Enables you to store semi-structured data. ALl rows in a table must have a unique key (composed of a partition key and row key), and when you modify data in a table, a timestamp column records the date and time the modification was made, but other than that , che columns in each row can vary. Have no concept of foreign keys, relationships, stored procedures, views and other objects you might find in a relational database. Usually store denormalized data, with each row holding the entire data for a logical entity.

![azure table](img/az-table.jpg)

To ensure fast access, a table is split into partitions. Partitions is a mechanism for grouping related row, based on a common property or partition key. Rows that share the same partition key will be stored together. Partitioning not only helps to organize data, but also improves scalability and performance in the following ways:

- Partitions are independent from each other, and can grow or shrink as rows are added to, or removed from, a partition. A table can contain any number of partitions.
- When you search for data, you can include partition key in the search criteria to narrow down the volume of data to be examined, and improves performance by reducing the amount of I/O needed to locate the data.

The key in a table comprises two elements; the partition key that identifies the partition containing the row, and a row key that is unique to each row in the same partition. Items are stored in row key order. If an application adds a new row, Azure ensures that the row is placed in the correct position in the table. This scheme enable to quickly perform point queries that identify a single row, and range queries that fetch a contiguous block of rows in a partition.