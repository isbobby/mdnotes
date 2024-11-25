# Overview

# Questions
**Redis Hot Key**
A hot key is a key that's accessed **way more frequent** than the other keys, this can cause a particular Redis instance to see a degraded performance.

There are solutions to address hot key issue.
1. identify the hot key, can use key space notification to monitor high writes, may need to rely on application logs or `MONITOR` command to identity hot read keys
2. distributed load of hot key, by sharding into `key-N` across different instances
3. a hot key may also represent an opportunity to implement rate limiting, based on access pattern
4. application memory caching

**Caching Strategies**
Write through - write to both data store and cache simultaneously.
- consistent, synchronised at the time of write
- may cache infrequent data 
- performance overhead not suitable for high writes
- good for read heavy, highly consistent applications 
Write back - write to cache, return and asynchronously write to database
- reduces load on backend database, as we can batch the writes
- sacrifice consistency to protect database 
- good for write heavy application where data consistency can be sacrificed 
Cache aside / lazy loading - application code loads data into cache when cache miss occurs. 
- selective caching
- may serve stale data if writes do not purge cache 
- higher backend load on cache misses, may see spikes 
- good for read heavy applications and with varying read patterns

**Cache Hazards**
Cache penetration - happens when cache is bypassed, subjecting database to heavy load
- big eviction in batch - vary TTL (actually avalanche)
- cache miss - negative caching with exponential back off TTL
- new ingested data / query inputs - cache preloading


