# Overview
A distributed search engine like Elastic has these main concepts
1. Text Indexing with inverse index
2. Partition for distributed data storage
## Text Indexing
The main query pattern is to search for documents by its contents, so this requires the construction of an **inverted index**. For example, given a string `Lucene is a search library`, we may extract non-stop word tokens: `[Lucene, search, library]`.

We can then create a `word -> document` index, where the keyword points to the corresponding document and its position in documents
```
Lucene, [document1]
search, [document1, document2]
library, [document1, document3]
```

The `word` will be in sorted order, this allows for **prefix matching**. A reverse inverted index (`apple -> elppa`) can also be created simultaneously, which allows for **suffix matching**.

When we have a big and highly cardinal text space, our index size can increase very rapidly. There are some workarounds to prevent the index from blowing up
1. Token filter, remove things like `uuid`, `timestamps`
2. High-cardinality fields like `user IDs` / `timestamps` can be stored as **doc values**, which are optimised for sorting, aggregation, and filtering.
3. Use `keyword` to enforce exact matching on `uuid` or other IDs
## Text Indexing Process
When a document is inserted to the search database, the following occurs
1. A document is stored in a **search index**, the **search index** functions similar to a database table
2. The document will pass through an analyser, which breaks the document down into tokens
3. An inverted index (or other indexes) are created based on the tokens
4. The document is stored with a corresponding document ID in this index segment. The ID is only unique to the current segment.
![[search_system_doc_indexing.png]]

The search engine (Lucene) will also handle multiple index segments, the segment design is similar to [LSM trees](LSM%20Tree.md) which inherit some writing advantages. A memory buffer is also used to perform batch writes which further boost write performances. This comes at a cost of query data consistency - it will be **near real time**.

The deletion of a document by ID does not modify the data its original location, instead it adds the deleted ID into a separate location (mark as deleted). Segments may get merged as an optimisation.

![[search_index_and_segments.png]]
# Scoping
**Q**: What are the types of search we support?
**A**: Full text, fuzzy, faceted, geo-spatial are some search types, we can focus on full text and fuzzy.

**Q**: Should the system support advanced queries?
**A**: Some advanced queries are wildcard, boolean, and range queries, we can support them all.

**Q**: Should the system provide ranking and scoring? How should they work?
**A**: There are multiple ranking and scoring models, such as **TF-IDF** (term frequency) or **BM25**. Also take into consideration of **freshness** and **popularity**.

**Q**: What are the types of data that need to be indexed?
**A**: Text, numerical, multimedia

**Q**: How many results do we need to return? Do we want to provide pagination?
**A**: Depends on the query, and pagination is required.

**Q**: How is the data ingested? And how frequent does the data update?
**A**: Unlike [[Web Search System Design]], we don't need to implement a crawler. We can assume the data can be written to us via REST APIs.

**Q**: What indexing structure do we support?
**A**: Inverted index, forward indices.
# System Design
Designing is simpler after breaking the system down into sub components.
- **Indexing System**: Converts raw data into a searchable format.
- **Cluster Management**: Manages node health, data replication, and failover.
- **Query Engine**: Processes user search queries and returns relevant results.
- **API Layer**: Provides user-friendly interfaces for data ingestion and querying.
- **Monitoring and Metrics**: Tracks system performance and detects issues.
## Indexing System
The indexing system is the most unique component to search system. The above section on indexing process gives a good summary on how indexing is carried out in a search engine like Lucene.

As we look to distribute the indexing system, we further break it down into these sub-components
- index node - the server running the particular index
- shard - a lower level storage unit than an index, index contains multiple shard, each holding its own set of data
- segment - the lowest level of storage unit, holds the actual data structure which supports inverse index and the document data themselves
## Cluster Management
As we distributed and scale the nodes, we need proper cluster management for these nodes in order to
1. ensure reliability with replication
2. provide scalability options
3. node life cycle management
### Node Roles
Nodes can have different roles
1. master node - manages cluster, indices and shards
2. data node - stores and indexes data, handles search operation, needs good machines
3. ingestion node - ingests and enrich data before storing
4. coordinator node - load balances and routes the client request to the relevant node
5. remote cluster client node - manages cross cluster operations
## Data Replication
Data replication in an Elastic cluster is done by replicating shards. Shard can be configured to have any number of replica shards, which are asynchronously updated when there are incoming writes.

# System Diagram
![[search_system_diagram.png]]