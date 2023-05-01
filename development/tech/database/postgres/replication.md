# PostgreSQL Replication
## What Is PostgreSQL Replication?
- `PostgreSQL` replication is defined as the process of `copying data` from a `PostgreSQL` database server to `another` server.
- The `source` database server is also known as the `“primary”` server.
- The database server receiving the copied data is known as the `“replica”` server.
- All `writes` go to a `primary` node. The primary node can then `apply` these `changes` and `broadcast` them to `secondary` nodes.
## Automatic Failover
- `Failover` is a `method` for `recovering data` if the `primary` server `goes down` for any reason.
- If configured PostreSQL to manage your `physical streaming replication`, users will be `protected` from `downtime` due to a `primary` server down.
- `No built-in` tools, need `setup`.
- There are `dedicated` tools that allow `automatic failover` and automatic switching to the standby, cutting down on `database downtime`.

## Benefits of Using PostgreSQL Replication
- `Fault tolerance`: If the `primary` server `fails`, the `standby` server can act as a `server` because the contained `data` for both `primary` and `standby` servers is the `same`.
- `System testing in parallel`: While upgrading a new system, you need to make sure that the system `fits` well with existing `data`, hence the `need` to `test` with a `production database copy` before `deployment`.
## How PostgreSQL Replication Works
- `Streaming replication`: Replicates data from the `primary` node to the `secondary`, then `copies data` to `Amazon S3` for `backup storage`.
- `Volume-level replication`: Replicates data at the `storage layer`, starting from the `primary` node to the `secondary` node, then `copies data` to `Amazon S3` or for `backup storage`.
- `Incremental backups`: Replicates data from the `primary` node while constructing a `new secondary` node from `Amazon S3`, allowing for `streaming directly` from the `primary` node.
### Method 1: Streaming replication
- `streaming replication` also known as `WAL replication` can be set up seamlessly after installing `PostgreSQL` on `all` servers. 
- Based on `moving` the `WAL files` from the `primary` to the `target` database.
### Method 2: Volume-level replication
- `Changes` are written to a `persistent` volume (`disk`) which gets `synchronously mirrored` to `another` volume.
- `Compatibility` and data `durability` in `cloud` environments with `all relational databases`.
- `Cons`: In PostgreSQL replication this method needs you to `replicate` both `WAL` log and `table` data, `can’t` afford to lose a `single` byte, as that could `leave` your database in a `corrupt` state.
- This method is normally used with `Amazon RDS`.

## Models of PostgreSQL Database Replication
- We can achieve horizontal scalability mainly by `single-master replication` (`SMR`) and `multi-master replication` (`MMR`).
- `Single-master` replication allows `data` to be `modified` only on a `single` node, and these `modifications` are `replicated` to `one` or `more` nodes. 
- Most of the time, `Single-master` is `enough` for the application because it’s `less complicated` to configure and manage along with `no chances of conflicts`. 
- `Single-master` replication is also `unidirectional`, since replication data flows in `one direction` mainly, from the `primary` to the `replica` database.
- `multi-master` replication allows `more than one` node to act as the `primary` node. `Changes` are replicated to their `counterpart` tables in every other `primary` database. In this model, `conflict resolution` schemes are often employed to avoid `problems` like `duplicate primary keys`.
- A few `advantages` to using `multi-master`:
    - In the case of `host failure`, `other hosts` can still give `update` and `insertion` services.
    - The `primary` nodes are `spread out` in `several` different `locations`, so the `chance` of `failure` of `all primary` nodes is very `small`.
- `PostgreSQL` doesn’t support `multi-master` natively. These `solutions` may be open-source, free, or paid.
## Types of PostgreSQL Replication
- `Physical` replication: deals with `files` and `directories`, used to maintain a `full copy` of the `entire data` of a `single cluster`, are done at the `file system level`.
- `Logical` replication: is a way of `reproducing` `data` entities and their `modifications`, deals with `databases`, `tables`, and `DML` operations and is done at the `database cluster level`. It uses a `publish and subscribe model` where `one or more subscribers` are subscribed to one or more `publications` on a `publisher` node.
- The `replication` process `starts` by `taking a snapshot` on the `publisher database` and then `copying` it `to` the `subscriber`. `Subscribers` `pull` data from the publications, `applies` the data in the `same order` as the `publisher` so that `transactional consistency` is `guaranteed`.
## How To Set Up PostgreSQL Replication
- Config `pg_hba.conf` and `postgresql.conf` on `primary` server and `secondary` servers.
## Reference
- [PostgreSQL Replication: A Comprehensive Guide](https://kinsta.com/blog/postgresql-replication/#:~:text=PostgreSQL%20replication%20is%20defined%20as,as%20the%20%E2%80%9Creplica%E2%80%9D%20server.)
