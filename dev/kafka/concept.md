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
- ZooKeeper decides which is the leader, which are the follower

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
- **Each consumer within a group reads from exclusive partitions (no partitions distribute messages to 2 consumers in one group)**
- If there are more consumers than partitions within a group, some consumers will be inactive
- If you want to have high # of consumers, you must have high # of partitions
