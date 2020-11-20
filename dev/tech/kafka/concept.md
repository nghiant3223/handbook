# Concepts

## Topic, partition and offset

- Topic is a stream of data
- You may get many topic as you want
- A topic is identified by its name
- Topics are split into partitions, each partition is ordered
- Each message within a topic have a incremental id, called offset
- Number of partition needs to be specified when topic is created
- Offset only has a meaning for a specific partition
- Order is guarantee only within a partition (not accross partitions)
- Data is kept only for a limited time (default is 1 week)
- Once data is written to a partition, it can't be changed (immutable)
- Data is assigned randomly to a partition unless a message key is provided (round-robin fashion)

## Brokers

- A cluster is composed of multiple brokers (servers)
- Each broker is identified by a ID (integer)
- Each broker contains certain topic partitions (not all data)
- After connecting to any broker (bootstrap broker), you will be connected to the entire cluster
- Paritions of one topic do not stay in the same broker but they may be distributed over brokers in the cluster

## Topic replication

- Topics should have a replication factor (> 1)
- This way, if one server is down, another broker can still serve the data
- **At any time, only ONE broker can be a leader for a given partition**
- **Only that leader can receive and serve data for a partition**
- The other brokers (followers) will synchronize the data
- Therfore, each partition has one leader and multiple followers
- ZooKeeper decides which is the leader, which are the followers

## Producers

- Producers write data to topics (which is made of partitions)
- Producers automatically know which broker and partion to write to
- Producers can choose to receive ACK of data writes:
  - ACK = 0: Producer won't wait for acknowledgement (possible data loss)
  - ACK = 1: Producer will wait for leader acknowledgement (limited data loss)
  - ACK = all: Producer will wait for leader + follower acknowledgement (no data loss)
- Messages can be sent by producers in batch
- By default, producers distribute messages evenly to topic's partitions

### Message key

- Producer can send a key with the message (string, number, etcd.)
- If key = null, data is sent in round-robin fashion
- If a key is sent, then all messages for that key will always go to the same partition
- A key is basically sent if you need message ordering for a specific field (ex: truck_id)
- We get this gurantee thanks to key hashing which depends on the number of partitions

## Consumers

- Consumers read data from a topic (identified by name)
- Consumer know which broker to read from
- In case of broker failures, consumers know how to recover
- Data is read in order **within each partitions**
- Messages can be received by consumers in batch and consumed in sequential order only
- If you want to postpone processing of some messages you can republish them to separate topics, one for each delay value
- Processing failed messages can be achieved by cloning the message and republishing it to one of retry topics with updated information about attempt number and next retry timestamp
- Remember to call close the consumer before exiting. This will close network connection and sockets, trigger the rebalance immediately rather than wait for the group coordinator to discover that the consumer stopped sending heartbeats and is likely dead, which will take longer and therefore result in a longer period of time in which consumers can’t consume messages from a subset of the partitions
- **One consumer per thread is the rule**. You can’t have multiple consumers that belong to the same group in one thread and you can’t have multiple threads safely use the same consumer

### Polling

- Consumers make polling to parititon to check whether they have messages to receive (it retrieves messages starting from the latest commit offset)
- Consumers must keep polling Kafka or they will be considered dead and the partitions they are consuming will be handed to another consumer in the group to continue consuming
- Method `poll` takes a timeout parameter. It specifies how long it will take `poll` to return, with or without data. It also means that how fast do you want to return control to the thread that does the polling

### Groups

- Consumer read data in consumer groups
- **No 2 consumers in one group receive message from the same partition**
- If there are more consumers than partitions within a group, some consumers will be inactive
- If you want to have high # of consumers, you must have high # of partitions

### Commits

- Automatic commit: commit the largest offset returned `poll()` after each interval
- Synchronous commit: commit the latest offset returned by `poll()` and return once the offset is committed, throwing an exception if commit fails for some reason
- Asynchronous commit: commit the latest offset returned by `poll()` and return once the offset is committed, keep going no without waiting for the commit response
- Offset specified commit: commit some specific offset, which can be in the middle of batch, returned from `poll()` synchronously or asynchronously

### Offsets

- Kafka stores offsets at which a consumer group has been reading
- The committed offsets live in a topic named *__consumer_offsets*
- When a consumer in a group has processed data received from topic, it should be committing the offsets
- Committing the offsets means writing to the topic *__consumer_offsets*
- If a consumer dies, it will able to read back from where it left off thanks to the committed consumer offsets

### Delivery semantics

- Consumers can choose when to commit offset
- There are 3 delivery semantics:
  - At most once:
    - Offsets are committed as soon as the message is delivered
    - If the message goes wrong, the message will be lost (it won't be read again)
  - At least once:
    - Offsets are committed after the message is processed
    - If the processing goes wrong, the message will be read again
    - This can results in duplication processing of message. Make sure the processing is idempotent (i.e. processing the message again won't impact the system)
    - Exactly once:
      - Can be achieved for Kafka => Kafka workflows using Kafka Stream API
      - For Kafka => External System workflows, use idempotent consumer

### Rebalance

- Rebalance is moving partition ownership from one consumer to another
- It happens when:
  - New consumer is added to the group
  - Consumer shuts down or crashses
  - New parition is added the topic which consumer is consuming
- During a rebalance, **consumers can’t consume messages** => Rebalance is basically a short window of unavailability of the entire consumer group
- After a rebalance, each consumer may be assigned a new set of partitions than the one it processed before. In order to know where to pick up the work, the consumer will read the **latest committed offset** of each partition and continue from there.
- During rebalance, the consumer loses its current state; if it was caching any data, it will need to refresh its caches—slowing down the application until the consumer sets up its state again

### Rebalance Listeners

- A consumer will want to do some cleanup work before exiting and also before partition rebalancing
- If you know your consumer is about to lose ownership of a partition, you will want to commit offsets of the last event you’ve processed. If your consumer maintained a buffer with events that it only processes occasionally (e.g., the currentRecords map we used when explaining pause() functionality), you will want to process the events you accumulated before losing ownership of the partition. Perhaps you also need to close file handles, database connections, and such
- There are 2 listeners:
  - `onPartitionsRevoked(Collection<TopicPartition> partitions)`: Called before the rebalancing starts and after the consumer stopped consuming
messages. This is where you want to commit offsets, so whoever gets this partition next will know where to start.
  - `onPartitionsAssigned(Collection<TopicPartition> partitions)`: Called after partitions have been reassigned to the broker, but before the consumer starts consuming messages.

## Broker Discovery

- Every broker is also called a *bootstrap server*
- That means that you only need to connect to one broker and you be connected to the entire cluster
- Each broker knows about all brokers, topics and partitions (metadata)

## ZooKeeper

- Manages brokers by keeping a list of them
- Helps in performing leader election for partitions. When one broker goes down, new partition replica is elected to be the leader
- ZooKeeper sends notifications to Kafka in any case of changes (e.g. new topic, broker dies, broker comes up, delete topics, etc...)
- **Kafka can't not work without ZooKeeper**
- Operates with an odd number of servers (3, 5, 7)

## Guarantees

- Messages appended to a partition of a topic in the order they are sent
- Consumer reads message from a partition of a topic in the ordered they are stored
- With the replication factor of N, preducers and consumers can tolerate up to N-1 brokers being down
- **As long as the number of partition remains constant for a topic (no new partitions)**, messages with the same key always go to the same partition
- Number of partitions of a topic cannot be decreased

## Configuration

- `auto.offset.reset`: Controls the behavior of the consumer when it starts reading a partition for which it doesn’t have a committed offset or if the committed offset it has is invalid. If it's set to `latest`, the consumer will start reading from the latest records (records that were written after the consumer started running). The alternative is `earliest` the consumer will read all the data in the partition, starting from the very beginning.
