# MongoDB Sharding
## Sharding
- The process of `storing data` records across `multiple machines`
- Sharding solves the problem with `horizontal scaling`.
- With sharding, `add more machines` to support data growth and the demands of `read` and `write` operations.
## Why Sharding?
- In `replication`, `all writes` go to `master` node
- `Latency sensitive queries `still go to `master`
- `Single replica` set has limitation of `12` nodes
- `Memory` can't be `large enough` when `active dataset` is `big`
- `Local disk` is `not big enough`
- `Vertical scaling` is `too expensive`
## Sharding in MongoDB
- There are `three` main components:
    - `Shards`: used to `store data`. In `production` environment, each `shard` is a separate `replica set`.
    - `Config Servers` − `store` the `cluster's metadata`, contains a `mapping` of `the cluster's data set` to the `shards`. The `query router` uses this metadata to `target operations` to specific `shards`. In `production` environment, sharded clusters have exactly `3` config servers. The `config` database contains the `collections` that contain the `sharded cluster metadata`. `MongoDB` writes data to the `config` database when the `metadata` changes, such as after a chunk `migration` or a chunk split.
    - `Query Routers` − mongo instances
        - `interface` with `client applications` and `direct operations` to the appropriate `shard`. 
        - The query router `processes` and `targets` the operations to `shards` and then `returns results` to the `clients`.
        - Applications never connect or communicate directly with the shards. 
        - A `sharded cluster` can contain `more than one query router` to `divide` the `client request load`. It is call `mongos`. 
        - The `mongos` instances `cache` this data and use it to `route` read and write operations to the `correct shards`. 
        - `mongos` updates the `cache` when there are `metadata changes` for the cluster, such as `adding a shard`. `Shards` also read `chunk metadata` from the `config servers`. 
        - A `mongos` instance routes a query to a cluster by:
            - `Determining` the `list of shards` that must `receive` the `query`.
            - `Establishing` a `cursor` on `all targeted shards`.
            - The `mongos` then `merges` the `data` from each of `the targeted shards` and `returns the result document`. 
            - Certain query `modifiers`, such as 
`sorting`, are `performed on each shard` before mongos retrieves the results. 
- `Mongod` = The `primary daemon process` for the MongoDB system. It handles data requests, manages data access, and performs background management operations.

### Primary Shard
- `Each database` in a sharded `cluster` has a `primary shard` that `holds` all the `un-sharded collections` for that database.
- The `mongos` selects the `primary shard` when creating a new database by `picking the shard` in the cluster that has the `least amount of data`.

### Shard Key Cardinality
- The `cardinality` of a shard key determines the `maximum number of chunks` the balancer can create. 
- Where possible, choose a shard key with `high cardinality`.
- If you chose to shard on `continent`, the shard key would have a `cardinality` of `7`.
- If your data model requires sharding on a key that has `low cardinality`, consider using an `indexed compound of fields` to `increase cardinality`.
- A `shard key` with `high cardinality` does `not` `guarantee even distribution` of data across the sharded cluster. The 
frequency
 of the shard key and the potential for 
monotonically changing shard key values
 also contribute to the distribution of the data.

Shard Key Frequency
The frequency of the shard key represents how often a given shard key value occurs in the data. If the majority of documents contain only a subset of the possible shard key values, then the chunks storing the documents with those values can become a bottleneck within the cluster. Furthermore, as those chunks grow, they may become indivisible chunks as they cannot be split any further. This reduces the effectiveness of horizontal scaling within the cluster.

The following image illustrates a sharded cluster using the field X as the shard key. If a subset of values for X occur with high frequency, the distribution of inserts may look similar to the following:

Diagram of poor shard key distribution due to high frequency
click to enlarge
If your data model requires sharding on a key that has high frequency values, consider using a compound index using a unique or low frequency value.

A shard key with low frequency does not, on its own, guarantee even distribution of data across the sharded cluster. The 
cardinality
 of the shard key and the potential for 
monotonically changing shard key values
 also contribute to the distribution of the data.