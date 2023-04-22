# Scaling PostgreSQL for Large Amounts of Data
- Scalability is the property of a system/database to handle a growing amount of demands by adding resources.
## Horizontal Scaling
- Add more database nodes as `slave` nodes. 
- Can help to improve the `read` performance balancing the traffic between the nodes.
- Need to add a `load balancer `to distribute traffic to the correct node depending on the `policy` and the `node state`.
- To avoid a `single point of failure`, should consider adding `two or more` load balancer nodes and using some tool like `Keepalived`, to ensure the `availability`.
- `PostgreSQL` doesn’t have native `multi-master support`, if we want to implement it to `improve the write` performance, need to use an external tool.
## Vertical Scaling
- Some configuration `parameter` to allow PostgreSQL to use a new or `better hardware` resource.
    - `work_mem`: Specifies the `amount of memory` to be used by internal sort operations and hash tables `before` writing to `temporary disk files`.
    - `maintenance_work_mem`: Specifies the `maximum amount of memory` to be used by `maintenance` operations, such as `VACUUM`, `CREATE INDEX`, and `ALTER TABLE ADD FOREIGN KEY`. `Larger` settings might `improve performance` for `vacuuming` and for `restoring` database dumps.
    - `autovacuum_work_mem`: Specifies the `maximum amount of memory` to be used by each `autovacuum worker` process.
    - `autovacuum_max_workers`: Specifies the `maximum number of autovacuum processes` that may be running `at any one time`.
    - `max_worker_processes`: Sets the `maximum number of background processes` that the system can `support`: `vacuuming`, `checkpoints`, and `more maintenance jobs`.
    - `max_parallel_workers`: Sets the `maximum number of workers` that the system can support for `parallel operations`. `Parallel workers` are taken from the `pool of worker processes`.
    - `max_parallel_maintenance_workers`: Sets the maximum number of `parallel workers` that can be started by a `single utility command`.
    - `effective_cache_size`: Sets the planner’s assumption about the `effective size of the disk cache` that is available to a single query.
    - `shared_buffers`: Sets the amount of `memory` the database server uses for `shared memory buffers`.
    - `temp_buffers`: Sets the `maximum number of temporary buffers` used by each database `session`.
    - `effective_io_concurrency`: Sets `the number of concurrent disk I/O operations` that PostgreSQL expects can be executed `simultaneously`. `Raising` this value will increase the `number of I/O operations` that `any individual PostgreSQL session` attempts to initiate in `parallel`.
    - `max_connections`: Determines the `maximum number of concurrent connections` to the `database` server.
## Monitoring
- `Scaling our PostgreSQL database` is a complex process, so we should `check some metrics` to be able to determine the best strategy to scale it.
- We can monitor the `CPU`, `Memory` and `Disk usage` to determine if there is some configuration issue. For example, if we’re seeing a `high server load` but the `database activity is low`, it’s probably `not needed to scale` it, need to check the `configuration` parameters to `match` it with our `hardware resources`.
- Checking the `disk space` used by the PostgreSQL node per database can help us to confirm if we `need more disk` or even `a table partitioning`. 
- To `check the disk space` used by a database/table we can use some PostgreSQL function like `pg_database_size` or `pg_table_size`.
- From the database side, we should check:
    - `Amount of connection`
    - `Running queries`
    - `Index` usage
    - `Bloat`: Database bloat is `disk space` that `was used` by a `table` or `index` and `is available` for `reuse` by the database but has `not been reclaimed`.
    - `Replication Lag`: The number of `seconds` that the `replica's state` is `lagging` `behind the state` of the `primary instance`. 
## ClusterControl as a Scaling and Monitoring System
- `ClusterControl` is a database management tool can help us to cope with `both scaling ways` that we saw earlier and to `monitor all the necessary metrics` to confirm the scaling requirement.
## Reference
- [Scaling PostgreSQL for Large Amounts of Data](https://severalnines.com/blog/scaling-postgresql-large-amounts-data/#:~:text=There%20are%20two%20main%20ways,to%20an%20existing%20database%20node.)