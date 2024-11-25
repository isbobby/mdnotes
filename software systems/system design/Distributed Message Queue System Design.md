# Overview 
Why queue? Most of the benefits arise from decoupling work loads, where
1. decoupled components can be updated independently
2. decoupled components can be scaled independently
3. decoupled component's failure has limited effect on another component
4. decoupling time consuming task allow better performance from asynchronous work
# Scoping
The primary requirement is quite simple - producers can send message to the message queue, and consumers can consume produced message. Some guiding questions are

**Q**: What is the message type, size and format?
**A**: Kilobytes range

**Q**: Do we allow repeated message consumption, or only once?
**A**: We allow the same message to be consumed by multiple consumer here, **note** that traditional message queue purges the message once it's consumed

**Q**: Do we need to maintain message ordering?
**A**: Yes, we want to preserve order. However, this is also an added feature not guaranteed by traditional message queue

**Q**: Do we need data retention and persistence?
**A**: Yes, we should provide a configurable retention period. This is also not a guarantee by traditional message queue.

**Q**: What are the supported delivery semantics?
**A**: At least once, at most once, exactly once.

**Q**: Performance constraints?
**A**: Support high throughput for workload such as log aggregation, also support low latency delivery for traditional message queue use cases.

## Requirements
1. Producers and consumers have APIs to send and receive message
2. Support `kb` size messages
3. Historical data is stored for a set period of time
4. Must deliver message the same order they were produced
5. Support at least once, at most once, exactly once semantics

**Non-functionals**
1. Scalable - as the system should be distributed in nature, and must be able to handle a sudden spike in message volume.
2. Persistence in distributed machines - if we decide to use multiple nodes.
# High Level Design
## Point to Point
In this model, producer sends message to the queue. Then, a single consumer may consume this message. Even if multiple consumer attempts to `consume`, the message only gets consumed by one consumer.

This is easy to implement. But the requirements map more naturally to a publish subscribe model (below).
## Publish-Subscribe
In a `pub-sub` model, the concept of topic is introduced
> **topic** - categories, or routes, for messages. Each topic is unique in a message queue. Producer will send to a desired topic instead.

Producer specifies the intended topic to deliver message to, and consumer specifies the topic to consume message from.

The first challenge we resolve by using `topic` is scaling up to data volume, for example, when large amount of data is ingested when there's a traffic spike. To handle this, we can again shard a topic into different shards, each holding a subset of the data.

We introduce another component - **message broker** - who is responsible for distributing incoming messages to different shards. This allows us to quickly scale by expanding the number of partition in a topic. Each partition still maintains a FIFO order, **although we do lose a global ordering as a trade off**. Messages appended to a partition have a monotonically increasing offset.
## Consumer Groups
When message volume increases we may scale consumer horizontally as well. We can further organise consumers into consumer groups. Each consumer group can subscribe to multiple topics and maintain its consuming offsets.

The danger here is having **two consumer instances** consuming from the same partition in parallel - this will break the ordering within a partition. To maintain partition level ordering, we need to constraint each partition to be consumed by one consumer instance.

## High Level Design Components

![[message_queue_high_level.png]]
**Clients**
- Producer: invokes `publish(topic, message)` API
- Consumer: invokes `consume(topic) message` API
**Service & Storage**
- Broker: holds multiple partition for a topic, distribute incoming message to each partition
- State Store: tracks consumer and consumer connection state
- Metadata: tracks the configuration (retention), and properties of a topic
**Coordination Service**
- Service discovery: tracks broker health
- Leader election: maintains one active controller in the queue cluster, who is responsible for assigning partition
- `ZooKeeper` or `etcd` are used to elect controller
# Deep Dive
## Data Storage for Retention
As a middleware, message queue observes **heavy read and write** traffic. But writes are restricted to `insert`, `update` and `delete` should be absent. The read requests will also be sequential (as opposed to random access).

**Candidate 1 - SQL, or NoSQL Databases**
Database can handle the storage requirement, but they are not ideal as it's hard to design a database which supports heavy read and write. We also wouldn't use the rich indexing feature as our access pattern is sequential.

Some performance downfalls are
- Databases use locks / row version control to manage concurrency, this adds overhead and is not needed when we have consumer `ack` mechanism
- Database store data in row based structure ([[Data Layout in Databases]]) where tuples are stored together, which adds overhead for append only access pattern
- Need to rely on **auto-incremented** primary keys to ensure ordering, indexing increases write complexity

**Candidate 2 - WAL**
Since we have scoped our write and read pattern to **sequential**, we can take simply use an append only log. Similar to `redo` log in `MySQL`.

The advantage comes from the synergy between WAL and queue access pattern. WAL has a pure sequential access pattern.

A new message is always appended to the tail of a WAL with a monotonically increasing offset. The simplest solution is to use the file line number. To scale better, we can even horizontally scale a WAL by dividing it into segments.

WAL will maintain one single active segment with a limit in size. When the size limit is reached, a new segment is created which replaces the current active segment to accept new writes. Non-active segment will stay to serve **read** only requests. We can truncate old non-active segment files if the partition storage limit is reached.

One thing to note here is that rotational disks should perform reasonably well as we only do sequential access. A disk read requires the following step
1. move the disk head to the correct track
2. wait for the disk to rotate
3. transfer 
Sequential access minimise time spent on disk head movement and disk rotation. In fact, sequential read and writes approach the disk's maximum throughput as very little time is spent on steps 1 and 2.
![[message_queue_topic_partition.png]]
## Data Structure Design
The key data structure here to consider for is the **message** itself. It's important to enforce a contract such that **no data transformation happen when message passes through the queue**. This is because the transformation process adds computation and memory overhead to **all messages**.

Hence, we want to eliminate data transfer and copying within the queue. We can use the following structure as a starting point
```
key []byte // used to choose partition via % operation, can be empty
value []byte
topic string
partition int
offset long
timestamp long
size integer
crc integer // CRC to check data corruption
```
This allows both producer and consumer to use the same data structure, without any transformation.

A further improvement (besides consistent producer and consumer data structure) is to use batching aggressively in all stages - producer, queue, and consumer. This is an active trade off decision - **larger batches increase throughput at the expense of latency**.

**Producer & Consumer Batching**: pack more messages in one network request.
**Queue Batching**: pack more messages in one disk read / write.
## Broker Replication
From the above, we see the broker is the most important component as it handles serving read and write requests, as well as manages data retention within partitions.

We have decided to use a single broker to handle messages. But in a distributed system, hardware issues are common and cannot be ignored. If the broker fails, then the entire queue will fail. An mitigation to increase the topic's availability is to create replications for a broker.

Instead of having a single broker, we can have a leader-follow replication set up. This is where the "external" coordination service kicks in, to elect a leader broker.
1. coordination service selects a leader broker
2. we have `N` partitions, `M` broker replicas
3. the leader broker will implement an algorithm to generate a distribution plan to distribute `N` partitions across `M` replicas
4. once done, leader persists the above plan in the coordination service

![[message_queue_replicated_brokers.png]]
The final set up / distribution plan may look like the above. Writes only go to leader partitions, read requests can be served from replicas.

With replication comes replication delay - this is also a configurable, where we allow a certain number of lagged messages. When the offset difference between replica and leader is within this limit, then we can conclude the replica is in-sync.

This is another trade off between performance and durability - with a strict in-sync condition, the leader may wait very long for all replicas to catch up and be in a consistent state before committing a write.

We can provide two variables to adjust this behaviour - `Replica ACK Count` and `Lag Message Limit`. 

**ACK ALL** - requires all replicas to receive and acknowledge the message. Slow, but high durability.
**ACK=1** - or only requires leader `ACK`, if leader fails before the message is asynchronously replicated, the message could be loss.
**ACK=0** -  basically UDP between producer and leader.
## Producer Flows
Life is easy if we are just doing point to point delivery, or if we just have one single broker.

However in a distributed environment, there can be multiple brokers to publish message to, the producer may contain a configuration value that points to the queue, but since brokers can be ephemeral, produce needs to know which broker / partition should the message be routed to.

There are two possible solutions here.
1. implement a routing layer in front of the broker replicas, it serves as a load balancer and broker gateway, directing traffic to the broker, and return response to client
2. produce takes the responsibility of discovering broker and partition, this will increase performance and reduce queue complexity
## Consumer Flows
Consumer retrieves data from the broker by tracking and providing an offset value - the location of the message previously consumed. However, there are also two possible methods

**Push: Broker -> Consumer**: producer push data to set of consumers by a network request, this pushes data immediately to the consumer with no latency, however
1. can overwhelm the data, or needs complex congestion control
2. difficult to ensure fair sharing, where fastest consumer gets most messages

**Pull:** Consumer can pull data from the broker (instead of waiting for a write request), this allows for these good bits
1. Consumer control consumption rate, consumer will not be overwhelmed and no need for congestion control
2. Scaling consumer is easy
However, if there's no data available, repeated calls to `receive` will waste network resources, a workaround is to implement long polling, allowing the consumer connection to wait for a new message.
### Consumer Rebalancing
When there's a huge amount of messages, we may scale up the number of our consumers, or we may scale down the number of consumers when there are less messages. If we have a single queue, it will be easy, however, we need to **rebalance** our consumers if we are employing partitions for topics.

This can be done with an added coordination feature in the broker. We can provide the following interface
```
Heartbeat(consumerID) (Acked Bool)
JoinGroup(consumerID) (Role Enum)
LeaveGroup(consumerID)
Sync(consumerID, dispatchPlan) (partitions []int)
```

`Heartbeat()` API is periodically called, to indicate `consumerID` is still alive. May want to sign the request to authenticate. Under normal circumstances, `Acked=true`. 

`JoinGroup` API should be called when `Heartbeat()` returns `false`. The broker will wait for the consumer to call `JoinGroup`. `JoinGroup` eventually returns the assigned role to the consumer.

`Sync` should be called after `JoinGroup`, the leader must provide a distribution plan (which consumer consumes which partition), whereas a follow will get the assigned partitions as the `sync` response.

The above accounts for
1. new consumer joins 
2. consumer leaves
3. consumer crashes, no heartbeat past threshold time
In all cases, the coordinator (broker) just need to request **all consumer** to `JoinGroup`, assign a leader, then send out distribution plan in `sync` again.

## Metadata Store
Last bit - we should store the configuration in a key value store, these configuration help the queue to decide the number of partitions, retention period, and distribution of replicas.

`ZooKeeper` - A hierarchical key value store can help provide the above services.

## Delivery Semantics
### At-most once
Producer can send message with `ACK=0`, fire and forget. Great for metrics and logs.
### At-least once
Producer need to set `ACK=1` or `ACK=all` to ensure the delivery is synchronous.
### Exactly once
The hardest and least performant guarantee, this requires the queue to deduplicate while a message is received, and ensure the one consumer receives the message once.

**Producer** must be idempotent, use **unique** message IDs for or sequence number to ensure no duplicate messages reach the event stream.

The broker may need to keep a record of unique ID, and only store if the incoming ID does not exists.

Lastly, the consumer may further track their offset by committing them to a durable store. When consumer restarts, this state can be restored.

