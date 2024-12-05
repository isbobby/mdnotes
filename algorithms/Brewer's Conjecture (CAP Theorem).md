These are desirable characteristics for a system
1. Consistency - every read request will receive the most recent write, or an error
2. Availability - every request received by a non-failing node in the system must result in a response
3. Partition Tolerance - the system can continue to operate despite an arbitrary number of messages being dropped by the network between nodes
However, when a network partition occurs, the system must decide to do one of the following
1. Preserve consistency by decreasing availability of partitioned nodes
2. Maintain availability of partitioned nodes, allow them to serve stale data at the cost of consistency
3. In the absence of partition, both availability and consistency can be satisfied.