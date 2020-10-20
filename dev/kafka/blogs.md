# Blogs

- [How to choose the number of topics/partitions in a Kafka cluster?](https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster/)
- [How to Choose the Number of Partitions](#how-to-choose-the-number-of-partitions)
- [How Kafka’s Consumer Auto Commit Configuration Can Lead to Potential Duplication or Data Loss](https://blog.newrelic.com/engineering/kafka-consumer-config-auto-commit-data-loss/)
- [Golang - Working with Kafka (Message Batching, ACKs, Logging)](https://www.sohamkamani.com/golang/working-with-kafka/)
- [Partition Assignment Strategy](https://medium.com/@anyili0928/what-i-have-learned-from-kafka-partition-assignment-strategy-799fdf15d3ab)

## How to Choose the Number of Partitions

There are several factors to consider when choosing the number of partitions:

- What is the throughput you expect to achieve for the topic? For example, do you expect to write 100 KB per second or 1GB per second?
- What is the maximum throughput you expect to achieve when consuming from a single partition? You will always have, at most, one consumer reading from a partition, so if you know that your slower consumer writes the data to a database and this database never handles more than 50 MB per second from each thread writing to it, then you know you are limited to 60MB throughput when consuming from a partition.
- You can go through the same exercise to estimate the maximum throughput per producer for a single partition, but since producers are typically much faster than consumers, it is usually safe to skip this.
- If you are sending messages to partitions based on keys, adding partitions later can be very challenging, so calculate throughput based on your expected future usage, not the current usage.
- Consider the number of partitions you will place on each broker and available diskspace and network bandwidth per broker.
- Avoid overestimating, as each partition uses memory and other resources on the broker and will increase the time for leader elections.

With all this in mind, it’s clear that **you want many partitions but not too many**. If you have some estimate regarding the target throughput of the topic and the expected throughput of the consumers, you can divide the target throughput by the expected consumer throughput and derive the number of partitions this way. So if I want to be able to write and read 1 GB/sec from a topic, and I know each consumer can only process 50 MB/s, then I know I need at least 20 partitions. This way, I can have 20 consumers reading from the topic and achieve 1 GB/sec. 

If you don’t have this detailed information, our experience suggests that limiting the size of the partition on the disk to less than 6 GB per day of retention often gives satisfactory results.
