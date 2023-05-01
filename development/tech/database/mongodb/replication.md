# MongoDB - Replication
## Replication
- The process of `synchronizing data` across `multiple` servers.
- Provides `redundancy` and increases `data availability`.
- Protects a database from the `loss` of a single server.
- Allows to `recover` from `hardware failure` and `service interruptions`.
- With additional copies of the data, you can dedicate one to `disaster recovery`, `reporting`, or `backup`.
- No downtime for `maintenance` (like `backups`, `index` rebuilds, `compaction`)
- `Read scaling` (extra copies to read from)
- `Replica` set is `transparent` to the application
## Replication in MongoDB
- `MongoDB` achieves `replication` by the use of `replica set`.
- A `replica set`
    - `a group of mongod instances` that host the `same data set`.
    - Can have `only one primary node`.
    - Applies `write` operations on the `primary` and then `records` the operations on the `primary`'s `oplog`. 
    - `Secondary` members `replicate` this `log` and apply the operations to their `datasets` in an `asynchronous` process.
    - The `oplog` is a special `capped collection` (will replace old records if reach max size) that keeps a rolling record of `all operations` that modify the data stored in your databases.
    - `All members` of the replica set can accept `read` operations.
    - A group of generally `minimum 3 nodes` are required
    - At the time of `automatic failover` or `maintenance`, election establishes for `primary` and `a new primary` node is elected.
    - After the recovery of `failed node`, it again join the replica set and works as a `secondary node`.
## Set Up a Replica Set
- Start the MongoDB server by specifying `--replSet` option
```
mongod --port 27017 --dbpath "D:\set up\mongodb\data" --replSet rs0
```
- It will start a mongod instance with the name `rs0`, on port 27017.
- In Mongo client, run `rs.initiate()` to initiate a `new replica set`.
## Add Members to Replica Set
- To add members to `replica set`, start mongod instances on multiple machines. Now start a mongo client and issue a command `rs.add()`.
```
>rs.add("mongod1.net:27017")
```
- You can add mongod instance to replica set `only` when you are `connected to primary node`. 
- To check whether you are connected to primary or not, issue the command `db.isMaster()` in mongo client.
## Replica Set Data Synchronization
- `MongoDB` uses `two` forms of data `synchronization`:
    - `Initial sync` to populate `new members` with the `full` data set, copies all the data from `one` member of the replica set to new member
    - `Replication` to apply `ongoing changes` to the entire data set. `Secondary` members copy the `oplog` from their `sync from source` and apply these operations in an `asynchronous` process
## Voting new primary
- Have `voting` and `non-voting` secondary members (can vote or not)
![](https://www.mongodb.com/docs/manual/images/replica-set-only-seven-voting-members.bakedsvg.svg)