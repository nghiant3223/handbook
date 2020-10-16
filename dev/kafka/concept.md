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

### Consumer groups

- Consumer read data in consumer groups
- **No 2 consumers in one group receive message from the same partition**
- If there are more consumers than partitions within a group, some consumers will be inactive
- If you want to have high # of consumers, you must have high # of partitions

### Consumer offsets

- Kafka stores offsets at which a consumer group has been reading
- The committed offsets live in a topic named *__consumer_offsets*
- When a consumer in a group has processed data received from topic, it should be committing the offsets
- Committing the offsets means writing to the topic *__consumer_offsets*
- If a consumer dies, it will able to read back from where it left off thanks to the committed consumer offsets

### Delivery semantics for consumers

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
- Also has a concepts of leader and followers. Leader handles writes, followers handle reads

## Guarantees

- Messages appended to a partition of a topic in the order they are sent
- Consumer reads message from a partition of a topic in the ordered they are stored
- With the replication factor of N, preducers and consumers can tolerate upto N-1 brokers being down
- **As long as the number of partition remains constant for a topic (no new partitions)**, messages with the same key always go to the same partition
