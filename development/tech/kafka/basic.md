# Basic

Read [Kafka: The Definitive Guide](https://www.confluent.io/resources/kafka-the-definitive-guide/)

The unit of data within Kafka is called a **message**.
If you are approaching Kafka from a database background, you can think of this as similar to a **row** or a **record**.

A message can have an optional bit of **metadata**, which is referred to as a **key**.
This assures that messages with the same key are always written to the same partition.

For efficiency, messages are written into Kafka in batches.
A **batch** is just a collection of messages, all of which are being produced to the same topic and partition.
Of course, this is a tradeoff between latency and throughput: the larger the batches,
the more messages that can be handled per unit of time,
but the longer it takes an individual message to propagate.

Messages in Kafka are categorized into **topics**.
The closest analogies for a topic are a database table or a folder in a filesystem.
Topics are additionally broken down into a number of **partitions**.
Note that as a topic typically has multiple partitions,
there is no guarantee of message time-ordering across the entire topic,
just within a single partition.

Partitions are also the way that Kafka provides **redundancy** and **scalability**.
Each partition can be hosted on a different server,
which means that a single topic can be scaled horizontally across multiple servers to provide performance far beyond the ability of a single server.

Producers create new messages.
In general, a message will be produced to a specific topic.
By default, the producer does not care what partition a specific message is written to and will balance messages over all partitions of a topic evenly.

Consumers read messages.
The consumer subscribes to one or more topics and reads the messages in the order in which they were produced.

Consumers work as part of a consumer group, which is one or more consumers that work together to consume a topic.
The group assures that each partition is only consumed by one member.

A single Kafka server is called a **broker**.
The broker receives messages from producers, assigns offsets to them, and commits the messages to storage on disk.

A partition is owned by a single broker in the cluster,
and that broker is called the leader of the partition.
A partition may be assigned to multiple brokers, which will result in the partition being replicated

A key feature of Apache Kafka is that of **retention**,
which is the durable storage of messages for some period of time.
Kafka brokers are configured with a default retention setting for topics,
either retaining messages for some period of time (e.g., 7 days)
or until the topic reaches a certain size in bytes (e.g., 1 GB).
Once these limits are reached, messages are expired and deleted so that the retention configuration is a minimum amount of data available at any time
