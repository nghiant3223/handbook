# Master-slave & Master-master replication in Databases
## Ways of Updating Data
- How do we ensure that when data in `one` database is `changed`, that change is `reflected` on all the `other databases`? There are `2` ways of going:
### Synchronous Update
- Any `change` to one of the databases is `immediately propagated` to all the `other` replicas.
- An `update` operation is `complete only` when `all the databases` have been `updated`.
- Ensures that the `data` always stays `updated` and ensures `no data loss`.
- `Need to wait` for `all the replicas` to be `updated` before `moving on`. Hence the `write`, `delete`, `update` operations consume `more time`.
- Incase a single node `fails`, `recovery` would be pretty `easy` as all the nodes are updated to the latest data.
### Asynchronous Update
- All the replicas are `not updated at the same time`.
- `Changes are queued` and the `update is done` only at `a few specific intervals`. 
- An `update` operation is considered `complete` when it is `finished on one database`.
- Much `quicker` than `synchronous` updates.
- If the `database` which `contains the updated data goes down` `before` the change is `propagated` then the `data is lost`.
## Master-slave Model
- Only `one primary database` called the `Master` and several secondary databases called `slaves`. 
- `Write and update` operations can `only` be performed on the `master` database
- `Read` operations can be performed on the both `master` and `slave` databases.
- Since the `master updates the slaves` this model ensures `consistency` of data among `different replicas`. 
- If one of the `slaves crashes`, it can `reboot` and `sync with the master` very easily.
- If the `master` itself `goes down`, the `most recently updated slave` is made `master`.
- If the update configuration is `asynchronous` then `loss of data may occur`
- Suitable when you have to do a `lot of read` operations but only `a few write` operations. For example A `streaming service` like `Netflix`, where the `data` (Movies) have to be `written only once` but have to `be read many times` by users.
- `A load balancer` is used to `split the query traffic` among `different databases`.
## Master-master Model
- `All` the nodes are `primary`, all the databases and be `read` from and `written` to. 
- `Each` database `propagates` its `changes` to `every other database replica` so that the system stays consistent.
- `Conflicts` may arise if `two` databases are `updated concurrently`. Solved by `communicating with each other` and using `some algorithms` or based on the `timestamp` of the update. The `latest` update is `kept` and the `rest` are `discarded`.
- If `one` of the databases goes `down` the `other` masters can be used to `update` the database.
- If the system is `asynchronous` then if `one` of the databases goes `down`, it can lead to an `inconsistent` state.
- `Complicated` because of the need for `conflict resolution` which only becomes `worse` as the `number of databases increases`.
- Used for more `mission-critical` applications and applications that involve `a lot of writing`. For example A `social media website` like `Twitter` where the `new tweets` have to be `written to the database` and also `continuously read` from the database to be displayed on the user's feed.
- Most of the databases available today support both `Master-slave` and `Master-master` configuration.