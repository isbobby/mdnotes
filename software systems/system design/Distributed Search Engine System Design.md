# Overview
A distributed search engine like Elastic has four main concepts
1. document
2. indices
3. mappings 
4. fields

**Document** - a document is the unit of data we are looking for, can be a `JSON`

**Indices** - an index is a collection of documents, where each document has a unique ID and a set of fields. Think of index as a database table, search happens here and returns document results that match the criteria

**Mapping & Fields** - the mapping is the schema of the index, which defines the fields that an index will have. This mapping is crucial in telling the search system **how** to interpret the data that's being stored.

## Text Indexing
The main query pattern is to search for documents by its contents, so this requires the construction of an inverted index. For example, given a string `Lucene is a search library`, we may extract non-stop word tokens: `[Lucene, search, library]`.

We can then create a `word -> document` index, where the keyword points to the corresponding document and its position in documents
```
Lucene, [document1]
search, [document1, document2]
library, [document1, document3]
```

When we have a big and highly cardinal text space, our index size can increase very rapidly. There are some workarounds to prevent the index from blowing up
1. Token filter, remove things like `uuid`, `timestamps`
2. High-cardinality fields like `user IDs` / `timestamps` can be stored as **doc values**, which are optimised for sorting, aggregation, and filtering.
3. Use `keyword` to enforce exact matching on `uuid` or other IDs
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

# High Level Design
