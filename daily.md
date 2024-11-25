# Resume Prep
QnA for listed experiences
# Go, Redis, PSQL Prep
Redis
- [x] cache failures (penetration, hot key, avalanche)
- [x] strategies (aside, write through and back)
- [ ] deployment mode (single instance, replica, sentinel)
- [ ] data structures and comparisons
- [ ] 
Go
- [ ] Channel and synchronisation
- [ ] GC
- [ ] Routines architecture 
# Algorithm Prep
Grind 75 completed in interview condition, fast clear around 8 questions per day
# System Design Prep
System Design Interview Book & Drawing Software Practice
1. URL Shortener 
2. Chat App (Whatsapp)
3. Video Streaming Platform
4. E Commerce
5. Rate Limiter
6. Dropbox
7. Search Engine (Google) (done)
8. Grab
9. Notification System 
10. Cloud Storage (S3)
11. Distributed Queue (done)

## 22 Nov Notes
Done: Max Sub **Array**, Insert & Merge **Interval**, 0/1 Matrix **BFS**, Closest Points **Heap**, longest non-repeating substring **Array/Greedy**, level ordering binary tree, 
Notes: 
1. Interval questions - always sort by first element, and merge condition is `front[1] <= back[0]`
2. `golang` heap function always call with receiver as arg, `heap.Pop(&h)`, never `h.Pop()`

System Design - Search Part I, Queue Part I
Notes:
1. search `get` part, point of clarification - query pattern
2. new technique - database shards and dedicated database for each query
3. new technique - offloading heavy object store in blob storage, like s3
4. search domain - ranking by weight, by word frequency
5. important scoping questions to queue - delivery semantic, persistence
6. queue model - point to point vs topic and subscriptions
7. NEW component - queue broker: manages different message partitions to allow scalable topics, where messages to the topic are divided into individual partitions
8. Constraint on partition - one consumer per partition, handled by broker 
9. new concept - small and frequent I/O will affect throughput, favour batching

## 23 Nov Notes

System Design - Queue Part II
1. Distinguish between point to point and publish-topic-subscribe model
2. Understand trade off of ordering and scale by topic partition
3. Internal queue architecture - topic managed by brokers, consumer consumes from one or more partitions
4. Queue access and write pattern - sequential and no random, leverage on simplicity of WAL.
5. Database not ideal due to higher complexity from its rich features, that we are not using
6. WAL can scale easily with files acting as segments, a large file may not be ideal when the OS attempts to load the whole thing into RAM
7. Broker handles message read and write request to a partition, the only way to make them highly available is to create broker replicas.
8. Broker leader decides on the distribution plan and persist it in an external service.
9. Producer have one additional configurable `ACK=N`, to adjust trade off between durability and performance.
10. Consumer group coordination is required - we have `N` consumer needed to map to `M` partitions in a topic. To do this, we need to employ a coordination service to always redistribute when the number of consumer instances change.

Done: 3 Sum
Algorithm Notes:
1. While checking for duplicates, do `if i >= 1 && arr[i] == arr[i-1]`
2. Skipping duplicate value in iteration is a good way to keep unique results

## 24 Nov Notes
Completed: Water flow, Coin Change, Course Schedule, Clone Graph
Algorithm Notes:
1. Use `rows` and `cols` `rowCount`, `colCount` for BFS question
2. Keep consistent child function argument position, got time wasting bug in water flow question due to different arg ordering
3. Can use the provided parameter to construct in-degrees map, as the dependency may only be a subset of the entire space. For example, `numCourses=5, dep=[]`

System Design Notes:
1. Understand the access pattern of S3 like object store - one write many reads
2. Consider the similarity of Linux `inode` and an object store - separate metadata and content, and use metadata as index to the actual content 
3. The core of such system lies in metadata and object storage management
## 25 Nov Notes
- [ ] S3 Storage (evening)

System Design Notes:
1. Key concept for search - inverted indexing which allows `text -> document` type of query. 
2. Inverted index layer is where the query meets the data
3. Ranking and sorting - we can either embed algorithms like TF-IDF in the search layer (same server), or move it to a separate service if it needs to be scaled independently

More on Redis:
1. Three main cache pattern
	1. write through for high consistency, low writes
	2. write back for low consistency, high writes
	3. cache aside for potentially stale, varying read pattern
2. Cache failures
	1. cache penetration happens when large query on few missing keys - negative caching 
	2. cache avalanche when batch eviction - staggered TTL
	3. hot keys - shard keys to different instances to distribute load / vertical scaling as STF
