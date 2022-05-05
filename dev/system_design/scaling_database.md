# Scaling Database

## Scaling RDBMS

### Master-slave replication

The master serves reads and writes, replicating writes to one or more slaves, which serve only reads. Slaves can also replicate to additional slaves in a tree-like fashion. If the master goes offline, the system can continue to operate in read-only mode until a slave is promoted to a master or a new master is provisioned.

Disadvantage(s): master-slave replication

- Additional logic is needed to promote a slave to a master.
- There is a potential for loss of data if the master fails before any newly written data can be replicated to other nodes.
- Writes are replayed to the read replicas. If there are a lot of writes, the read replicas can get bogged down with replaying writes and can't do as many reads.
- The more read slaves, the more you have to replicate, which leads to greater replication lag.
- On some systems, writing to the master can spawn multiple threads to write in parallel, whereas read replicas only support writing sequentially with a single thread.
- Replication adds more hardware and additional complexity.

### Master-master replication

Both masters serve reads and writes and coordinate with each other on writes. If either master goes down, the system can continue to operate with both reads and writes.

Disadvantage(s): master-master replication

- You'll need a load balancer or you'll need to make changes to your application logic to determine where to write.
- Most master-master systems are either loosely consistent (violating ACID) or have increased write latency due to synchronization.
- Conflict resolution comes more into play as more write nodes are added and as latency increases.
- There is a potential for loss of data if the master fails before any newly written data can be replicated to other nodes.
- Writes are replayed to the read replicas. If there are a lot of writes, the read replicas can get bogged down with replaying writes and can't do as many reads.
- The more read slaves, the more you have to replicate, which leads to greater replication lag.
- On some systems, writing to the master can spawn multiple threads to write in parallel, whereas read replicas only support writing sequentially with a single thread.
- Replication adds more hardware and additional complexity.

### Federation

Federation (or functional partitioning) splits up databases by function. For example, instead of a single, monolithic database, you could have three databases: forums, users, and products, resulting in less read and write traffic to each database and therefore less replication lag. Smaller databases result in more data that can fit in memory, which in turn results in more cache hits due to improved cache locality. With no single central master serializing writes you can write in parallel, increasing throughput.

Disadvantage(s): federation

- Federation is not effective if your schema requires huge functions or tables.
- You'll need to update your application logic to determine which database to read and write.
- Joining data from two databases is more complex with a server link.
- Federation adds more hardware and additional complexity.

### Sharding

Sharding distributes data across different databases such that each database can only manage a subset of the data. Taking a users database as an example, as the number of users increases, more shards are added to the cluster.

Similar to the advantages of federation, sharding results in less read and write traffic, less replication, and more cache hits. Index size is also reduced, which generally improves performance with faster queries. If one shard goes down, the other shards are still operational, although you'll want to add some form of replication to avoid data loss. Like federation, there is no single central master serializing writes, allowing you to write in parallel with increased throughput.

Common ways to shard a table of users is either through the user's last name initial or the user's geographic location.

Disadvantage(s): sharding
- You'll need to update your application logic to work with shards, which could result in complex SQL queries.
- Data distribution can become lopsided in a shard. For example, a set of power users on a shard could result in increased load to that shard compared to others.
- Rebalancing adds additional complexity. A sharding function based on consistent hashing can reduce the amount of transferred data.
- Joining data from multiple shards is more complex.
- Sharding adds more hardware and additional complexity.

## Denormalization

Denormalization attempts to improve read performance at the expense of some write performance. Redundant copies of the data are written in multiple tables to avoid expensive joins. Some RDBMS such as PostgreSQL and Oracle support materialized views which handle the work of storing redundant information and keeping redundant copies consistent.

Once data becomes distributed with techniques such as federation and sharding, managing joins across data centers further increases complexity. Denormalization might circumvent the need for such complex joins.

In most systems, reads can heavily outnumber writes 100:1 or even 1000:1. A read resulting in a complex database join can be very expensive, spending a significant amount of time on disk operations.

Disadvantage(s): denormalization
- Data is duplicated.
- Constraints can help redundant copies of information stay in sync, which increases complexity of the database design.
- A denormalized database under heavy write load might perform worse than its normalized counterpart.


### SQL tuning

SQL tuning is a broad topic and many books have been written as reference.

It's important to benchmark and profile to simulate and uncover bottlenecks.

- Benchmark - Simulate high-load situations with tools such as ab.
- Profile - Enable tools such as the slow query log to help track performance issues.

Benchmarking and profiling might point you to the following optimizations.

#### Tighten up the schema

- MySQL dumps to disk in contiguous blocks for fast access.
- Use CHAR instead of VARCHAR for fixed-length fields.
CHAR effectively allows for fast, random access, whereas with VARCHAR, you must find the end of a string before moving onto the next one.
- Use TEXT for large blocks of text such as blog posts. TEXT also allows for boolean searches. Using a TEXT field results in storing a pointer on disk that is used to locate the text block.
- Use INT for larger numbers up to 2^32 or 4 billion.
- Use DECIMAL for currency to avoid floating point representation errors.
- Avoid storing large BLOBS, store the location of where to get the object instead.
- VARCHAR(255) is the largest number of characters that can be counted in an 8 bit number, often maximizing the use of a byte in some RDBMS.
- Set the NOT NULL constraint where applicable to improve search performance.

#### Use good indices

- Columns that you are querying (SELECT, GROUP BY, ORDER BY, JOIN) could be faster with indices.
- Indices are usually represented as self-balancing B-tree that keeps data sorted and allows searches, sequential access, insertions, and deletions in logarithmic time.
- Placing an index can keep the data in memory, requiring more space.
- Writes could also be slower since the index also needs to be updated.
- When loading large amounts of data, it might be faster to disable indices, load the data, then rebuild the indices.

#### Avoid expensive joins

Denormalize where performance demands it.

#### Partition tables

Break up a table by putting hot spots in a separate table to help keep it in memory.

#### Tune the query cache

In some cases, the query cache could lead to performance issues.

## Why NoSQL is better at "scaling out" than RDBMS?

### Opinion 1

- ACID vs BASE - NoSQL typically leaves out some of the ACID features of SQL, sort of 'cheating' it's way to higher performance by leaving this layer of abstraction to the programmer. This has already been covered by previous posters.

- Horizontal Scaling - The real advantage of NoSQL is horizontal scaling, aka sharding. Considering NoSQL 'documents' are sort of a 'self-contained' object, objects can be on different servers without worrying about joining rows from multiple servers, as is the case with the relational model.

Let's say we want to return an object like this:

```
post {
    id: 1
    title: 'My post'
    content: 'The content'
    comments: {
      comment: {
        id: 1
      }
      comment: {
        id: 2
      }
      ...

    views: {
      view: {
        user: 1
      }
      view: {
        user: 2
      }
      ...
    }
}
```

In NoSQL, that object would basically be stored as is, and therefore can reside on a single server as a sort of self-contained object, without any need to join with data from other tables that could reside on other DB servers.

However, with Relational DBs, the post would need to join with comments from the comments table, as well as views from the views table. This wouldn't be a problem in SQL ~UNTIL~ the DB is broken into shards, in which case 'comment 1' could be on one DB server, while 'comment 2' yet on another DB server. This makes it much more difficult to create the very same object in a RDBMS that has been scaled horizontally than in a NoSQL DB.

### Opinion 2

RDBMS have ACID ( http://en.wikipedia.org/wiki/ACID ) and supports transactions. Scaling "out" with RDBMS is harder to implement due to these concepts.

NoSQL solutions usually offer record-level atomicity, but cannot guarantee a series of operations will succeed (transaction).

It comes down to: to keep data integrity and support transactions, a multi-server RDBMS would need to have a fast backend communication channel to synchronize all possible transactions and writes, while preventing/handling deadlock.

This is why you usually only see 1 master (writer) and multiple slaves (readers).

## References

- https://github.com/donnemartin/system-design-primer#sql-or-nosql
