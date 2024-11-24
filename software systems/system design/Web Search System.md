# Requirements
**Functional**
1. User can enter a search query 
2. Search engine gets the relevant result, as list ranked by some mechanism
3. These results are from the internet, they are available as HTML pages
4. User is shown a list of titles **and** descriptions
5. Fuzzy Search

**Non-Functional**
1. Handle query with high variance
# Mechanisms
**Part I - Read: User Interfacing Mechanism**
1. User requests are handled by an API server
2. The request fetches from a search result data store layer
3. This data store layer contains pre-computed lists
4. This list consists of title, URL, description, it's returned to the users

**Part II - Write: Data Generation Mechanism**
1. Web pages exist as HTML files on the internet
2. Web pages are reachable via URL, and each web page can contain other URLs
3. The crawler is responsible for exploring the web graph, and writing results to search result data store layer 
# Part I - Read: User Interfacing Mechanism
## User Interfacing API
**Basics**
1. Scaling - by allowing a load balancer to protect our API server, and cache frequently hit results to protect data store layer
2. Include `page` parameter in the API query param to allow pagination

**Spelling Preference**
Compile a spelling checker on client side, and check for spelling, while outputting prompt such as "continue search with your wrong spelling?"

This is because search with proper spelling are likely indexed and cached, and is likely the user's intention.
## Data Nuance
Each web page have the following attributes that are critical
1. URL
2. Site Content
3. Title
4. Description 
5. Hash 
6. *Last Updated* 
7. *Priority*

**Hash?** Hash here refers to the hash of the page content, this is because there are many pages with duplicate content, we can reduce database size by only including unique records.

**Estimates**
```
1 Trillion Pages 
100 Billion Unique Pages
Each Page ~2MB

Total Size: 100 Billion * 2MB = 200PB
```

We are not gonna have so much space in the world. Upon further inspection, we see the bulk of the data comes from *Site Content* - in fact, do we need to store this in our database? Or we can store it somewhere else?

## Search Result Data Store
The key concept here is to serve the request with different data bases with different data layout. Although the user sees a single API, the following options are provided
1. search by URL
2. search by word
3. search by hash

**Key Concept**
Based on the above estimation, we can see storing whole page in a relational database may not be efficient. Instead, we use the relational database as an indexing database which points to the blob corresponding to the content.

**Distributing Database Load with Shards**
We want to further scale database horizontally by shards to improve performance. We can spread the web pages with their URL as shard key. When a database query is received, a shard calculation function will be able to determine which database shard the data resides in.

**URL DB**
Contains `url, title` entries. Search fuzziness is required. Perfect match will be ranked first.

**Words DB**
A big search pattern is to search by words, or label of information. We will provision database for these queries.

**Authors DB**
If we consider video platforms, another search pattern is by author name. We can provision similar database for search by authors.

### Core Ranking Mechanism
The search data store is also responsible for fetching a list of result (not just one) to maximise probability of satisfying the user. In order to do this, we want to return the most relevant result. How do we determine if a result is more relevant?

We can go back to our data structure above, where we can include these additional fields for finer ranking -
```
author
popularity / clicked
```

For textual input, we can rank the results by frequency of occurrence, we also want to give words in header / title a bigger weight.

We can use popularity / clicked as a multiplier.

Last, we can construct a database which maps the query words to the URL of the content.
```
type wordIndex struct 
	word string
	score int
	url string 
```
Where score is the computed score based on above heuristic, and url leads to the actual content, stored in a network file store.

## Read Part Summary
The above defines the read pattern, where we serve user requests through horizontally scaled API server protected by load balancer, which queries data from a search result store layer, where
1. query by URL is served by `URL` DB
2. query by text / word is served by `words` DB
3. query by author is served by `author` DB
The database will be able to scale to handle load as they are sharded.

# Part II - Writing: Data Generation & Indexing
## For Web
Web page URLs have a recursive nature - each URL directs to a webpage with other URLs. 

## For Other Contents
