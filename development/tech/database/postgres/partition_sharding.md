# PostgreSQL Sharding: An Overview and MongoDB Comparison
## PostgreSQL Partitioning
- There’s a `table inheritance` feature in `PostgreSQL` that `allows the creation of child tables` with the `same structure` as a `parent` table. Here’s an example:
- `Parent` table:
```sql
CREATE TABLE temperature (
  id BIGSERIAL PRIMARY KEY NOT NULL,
  city_id INT NOT NULL,
  timestamp TIMESTAMP NOT NULL,
  temp DECIMAL(5,2) NOT NULL
);
```
- `Child` tables `inherit` the structure of the `parent` table and are limited by constraints:
```sql
CREATE TABLE temperature_201901 (CHECK (timestamp >= DATE '2019-01-01' AND timestamp <= DATE '2019-01-31')) INHERITS (temperature);
CREATE TABLE temperature_201902 (CHECK (timestamp >= DATE '2019-02-01' AND timestamp <= DATE '2019-02-28')) INHERITS (temperature);
CREATE TABLE temperature_201903 (CHECK (timestamp >= DATE '2019-03-01' AND timestamp <= DATE '2019-03-31')) INHERITS (temperature);
```
- A `function` that `controls` in `which child table` a `new entry` should `be added` according to the `timestamp` field
```sql
CREATE OR REPLACE FUNCTION temperature_insert_trigger()
RETURNS TRIGGER AS $$
BEGIN
    IF ( NEW.timestamp >= DATE '2019-01-01' AND NEW.timestamp <= DATE '2019-01-31' ) THEN INSERT INTO temperature_201901 VALUES (NEW.*);
    ELSIF ( NEW.timestamp >= DATE '2019-02-01' AND NEW.timestamp <= DATE '2019-02-28' ) THEN INSERT INTO temperature_201902 VALUES (NEW.*);
    ELSIF ( NEW.timestamp >= DATE '2019-03-01' AND NEW.timestamp <= DATE '2019-03-31' ) THEN INSERT INTO temperature_201903 VALUES (NEW.*);
    ELSE RAISE EXCEPTION 'Date out of range!';
    END IF;
    RETURN NULL;
END;
```
- A `trigger` is added to the `parent table` that calls the `function above` when an `INSERT` is performed.
```sql
CREATE TRIGGER insert_temperature_trigger
    BEFORE INSERT ON temperature
    FOR EACH ROW EXECUTE PROCEDURE temperature_insert_trigger();
```
- Disadvantages:
    - The `amount` of `manual` work needed to `maintain` such an environment
    - The `lack` of `optimization/support` for `“distributed”` queries.
## Declarative partitioning
- `PostgreSQL 10` was released with `native support` for `table partitioning` through the `new declarative partitioning` feature.
- Main table:
```sql
CREATE TABLE temperature (
  id BIGSERIAL NOT NULL,
  city_id INT NOT NULL,
  timestamp TIMESTAMP NOT NULL,
  temp DECIMAL(5,2) NOT NULL
) PARTITION BY RANGE (timestamp);
```
- `Tables` defined as `partitions` of the `main` table; with `declarative` partitioning, there was `no need` for `triggers` anymore:
```sql
CREATE TABLE temperature_201901 PARTITION OF temperature FOR VALUES FROM ('2019-01-01') TO ('2019-02-01');
CREATE TABLE temperature_201902 PARTITION OF temperature FOR VALUES FROM ('2019-02-01') TO ('2019-03-01');
CREATE TABLE temperature_201903 PARTITION OF temperature FOR VALUES FROM ('2019-03-01') TO ('2019-04-01');
```
- It still missed the `greater` optimization and `flexibility` needed to consider it a `complete` partitioning solution. It `wasn’t possible` to perform an `UPDATE` that would `result in` `moving a row` from one partition `to a different partition`.
- `Postgres 11` add some addtional features related to partition:
    - The possibility to define a `default partition`, to which `any entry` that `wouldn’t fit a corresponding partition` would be `added` to.
    - Having `indexes` added to the main table `“replicated”` to the `underlying` partitions.
    - Support for `Foreign Keys`
## PostgreSQL Sharding
- `PostgreSQL` has implemented `sharding` on `top of partitioning` by `allowing any given partition` of a `partitioned table` to be `hosted by a remote server`.
- The basis for this is in `PostgreSQL`’s `Foreign Data Wrapper (FDW)` support.
- On the `remote server`, we create a `“partition”`- just like creating a simple table.
```sql
CREATE TABLE temperature_201904 (
  id BIGSERIAL NOT NULL,
  city_id INT NOT NULL,
  timestamp TIMESTAMP NOT NULL,
  temp DECIMAL(5,2) NOT NULL
);
```
- On the `local server`:
    - `loading` the `postgres_fdw extension`
    - `allowing` `application user` to `use` that `extension`
    - `creating an entry` to `access` the `remote server` 
    - finally `mapping` that `user` with a `user in the remote server` (`fdw_user`) that has `local access` to a `remote partition table`.

```sql
CREATE EXTENSION postgres_fdw;
GRANT USAGE ON FOREIGN DATA WRAPPER postgres_fdw to app_user;
CREATE SERVER shard02 FOREIGN DATA WRAPPER postgres_fdw
    OPTIONS (dbname 'postgres', host 'shard02', port 
    '5432');
CREATE USER MAPPING for app_user SERVER shard02 
    OPTIONS (user 'fdw_user', password 'secret');
```
```sql
CREATE FOREIGN TABLE temperature_201904 PARTITION OF temperature
    FOR VALUES FROM ('2019-04-01') TO ('2019-05-01')
    SERVER remoteserver01;
```
- Creating a `proper partition` of our main table in the `local server` that `will be linked` to the `table` of the `same name` in the `remote server`.

## When to partition a table?
- When a `table` grows `so big` that searching it becomes impractical even with the help of `indexes`
- When `data management` is such that the `target data` is often the `most recently added` and/or `older data` is constantly being `purged/archived`, or even `not being searched anymore` (at least `not as often`).
- If you are `loading data` from `different sources` and `maintaining` it as a `data warehousing` for `reporting` and `analytics`.
- For a `less expensive archiving or purging` of `massive data` that `avoids exclusive locks` on the `entire` table.
## When should we resort to sharding?
- To `scale out (horizontally)`, when even after partitioning a table, the amount of data is too great or too complex to be processed by a `single server`.
Use cases where the data in a big table can be divided into two or more segments that would benefit the majority of the search patterns. A common example of a scenario like this is that of a company whose customers are evenly spread across the United States and whose searches to a target table involves the customer ZIP code. A shard could then be used to host entries of customers located on the East Coast and another for customers on the West Coast.

- Sharding can also be used to `reduce the complexity` of queries by dividing a large table into smaller, more `manageable` pieces. This can lead to `faster query response times`
- In some cases, sharding can be used to address regulatory or compliance requirements by physically separating data that has different levels of sensitivity or access requirements. This can help organizations meet their legal obligations and ensure that data is stored and accessed appropriately.
Note, though, this is by no means an extensive list.
## How should we shard the data?
With sharding (in this context) being “distributed” partitioning, the essence of a successful (performant) sharded environment lies in choosing the right shard key – and by “right,” I mean one that will distribute your data across the shards in a way that will benefit most of your queries. In the example above, using the customer ZIP code as shard key makes sense if an application will more often be issuing queries that will hit one shard (East) or the other (West). However, if most queries would filter by, say, birth date, then all queries would need to be run through all shards to recover the full result set. This could easily backfire on performance with the shard approach, by not selecting the right shard key or simply by having such a heterogeneous workload that no shard key would be able to satisfy it.

It only ever makes sense to shard if the nature of the queries involving the target table(s) is such that distributed processing will be the norm and constitute an advantage far greater than any overhead caused by a minority of queries that rely on JOINs involving multiple shards. Due to the distributed nature of sharding, such queries will necessarily perform worse if compared to having them all hosted on the same server.

Learn more about sharding keys in our blog, Choosing a good sharding key in MongoDB (and MySQL).

## Why not simply rely on replication or clustering?
- `Sharding` should be `considered` when you can’t efficiently `break down` a `big` table through `data normalization`.
- Need to `plit to partitions` distributed across `different servers` to `spread the load` across `many servers`. 
- It `doesn’t need to be one partition per shard`
- `often`, `a single shard` will host `a number of partitions`.
- These data scaling technologies may well `complement` each other: `a PostgreSQL database` may host `a shard` with `part of a big table` as well as `replicate smaller tables` that are often used for read only, such as `a price list`, through logical replication.
## Some best practices for implementing PostgreSQL sharding
- `Evaluate the need` for sharding
    -  Before implementing sharding, evaluate `whether it is necessary` for your application. 
    - Sharding can help `improve performance and scalability`, but it also `adds complexity` to your database `architecture`. 
    - `Consider` your application’s `workload` and `growth projections` to determine whether sharding is necessary.
- `Choose` a `sharding strategy`
    - `horizontal`, `vertical`, and `functional` sharding
    - Consider `factors` such as `data distribution`, `query patterns`, and `hardware requirements` when choosing a sharding strategy.
- Plan for `data migration`
    - `Migrating` data to a `sharded database` can be a `complex` and `time-consuming` process
    - Develop a `plan` for `migrating` your data that includes `testing` and `validation` to ensure `data integrity`
    - Consider using tools such as `pg_dump` and `pg_restore` to simplify the `migration` process.
- `Backup` and `recovery` strategies
    - `Sharding` can add `complexity` to `backup` and `recovery` processes
    - It’s `important` to `plan` for these processes in advance
    - Consider using a `backup tool` that `supports sharded databases`. 
    - Develop a `recovery plan` to ensure that data can be `recovered` in the event of a `disaster`.
- `Test` thoroughly
    - `Before deploying` a sharded database into production, it is essential to `test` it thoroughly.
    - `Test` the `sharding strategy`, the `application architecture`, and `disaster recovery procedures`.
- `Monitor` and `optimize` performance
    - Sharding can help `improve performance`, `but` it also `introduces` new `performance challenges`
    - `Monitor` your `database performance` regularly and `optimize` your database as `necessary`
    - Consider using tools such as `pg_stat_statements` and `pgAdmin` to help `identify` performance `issues` and `optimize` your `database`.
## How does PostgreSQL sharding relate to MongoDB sharding?
- `MongoDB®` tackles the matter of `managing big collections` straight through `sharding`
    - There is `no concept` of `local partitioning` of `collections` in `MongoDB`. 
    - The `whole MongoDB scaling strategy` is based on `sharding`.
    - `MongoDB sharding process` has been made as `transparent` to the `application` as `possible`: all a `DBA` has to do is `define` the `shard key`.
- `Instead` of `connecting` to `a reference database server`, the `application` will connect to an auxiliary `router server` named `mongos`, which will `process` the `queries` and `request` the necessary information to the `respective shard`.
- In `PostgreSQL`
    - The application will `connect` and `query` the `main database server`. 
    - There `isn’t an intermediary router` like the `mongos`
    - `PostgreSQL’s query planner` will `process` the `query` and `create an execution plan`
    - When `data requested from a partitioned table` is found on `a remote server` PostgreSQL will request the data from it, as shown in the EXPLAIN output below:
```sql
   Remote SQL: UPDATE public.emp SET sal = $2 WHERE ctid = $1
   ->  Nested Loop  (cost=100.00..300.71 rows=669 width=118)
     	Output: emp.empno, emp.ename, emp.job, emp.mgr, emp.hiredate, (emp.sal * '1.1'::double precision), emp.comm, emp.deptno, emp.ctid, salgrade.ctid
     	Join Filter: ((emp.sal > (salgrade.losal)::double precision) AND (emp.sal < (salgrade.hisal)::double precision)) ->  Foreign Scan on public.emp  (cost=100.00..128.06 rows=602 width=112)
           	Output: emp.empno, emp.ename, emp.job, emp.mgr, emp.hiredate, emp.sal, emp.comm, emp.deptno, emp.ctid
```



