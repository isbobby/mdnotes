# Overview
> From Block, File, to Object Storage

The very first ever storage - block storage. They are our hard disks and SSD, which attach to a computer physically to present raw storage space as volumes. Today we can also have access to block storage via high-speed network, or new block storage connectivity protocols.

File storage is built on top of block storage, which provides a higher level of abstraction, where data are stored as files in file hierarchy.

The system we are studying here would be the object storage. The object storage makes a **very deliberate trade off to sacrifice performance over durability**. It targets mainly "colder" data, and is mainly used for storage and back up. Data access are done over REST APIs.
# Scoping 
**Q**: What are the features we look for in an object storage?
**A**: If we take inspiration from S3, we have the following functionalities - bucket creation, object read and write, object versioning, listing objects in bucket.

**Q**: What is the typical data size?
**A**: We can store both massive (GB) objects and smaller objects (KB).
# High Level Design
