# Transactions in MongoDB
## Overall
- In `MongoDB`, an `operation` on a `single document` is `atomic`. 
- Because you can use `embedded` documents and arrays to capture `relationships` between `data` in a `single document structure` instead of normalizing across multiple documents and collections, this `single-document atomicity` `obviates the need` for `multi-document transactions` for many `practical` use cases.
- `However`, for `situations` that require `atomicity` for `updates` to `multiple documents` or `consistency` between `reads` to `multiple documents`
- From version `4.0`, `MongoDB` provides the `ability` to perform `multi-document transactions` against `replica sets`.
- `Multi-document transactions` can be used across multiple `operations`, `collections`, `databases`, and `documents`. 
- `Multi-document transactions` provide an `“all-or-nothing”` proposition. When a `transaction commits`, all data `changes` made in the transaction are `saved`. `Until` a transaction `commits`, `no write` operations in the transaction are `visible` `outside` the transaction.
- In `most` cases, `multi-document transaction` incurs a `greater performance` cost over `single document writes`.
- The `availability` of `multi-document transaction` should `not be a replacement` for `effective` schema design. 
- `Modeling` your data `appropriately` will `minimize` the need for `multi-document transactions`.
## Transactions API
```mongodb
// Start a session.
session = db.getMongo().startSession( { readPreference: { mode: "primary" } } );

employeesCollection = session.getDatabase("hr").employees;
eventsCollection = session.getDatabase("reporting").events;

// Start a transaction
session.startTransaction( { readConcern: { level: "snapshot" }, writeConcern: { w: "majority" } } );

// Operations inside the transaction
try {
   employeesCollection.updateOne( { employee: 3 }, { $set: { status: "Inactive" } } );
   eventsCollection.insertOne( { employee: 3, status: { new: "Inactive", old: "Active" } } );
} catch (error) {
   // Abort transaction on error
   session.abortTransaction();
   throw error;
}

// Commit the transaction using write concern set at transaction start
session.commitTransaction();

session.endSession();
```
## Transactions and Sessions
- `Transactions` are `associated` with a `session`.
- can have `at most one` `transaction` for a `session`.
- If a `session` `ends` and it has an `open` transaction, the transaction `aborts`.
## Transactions and the mongo Shell
The following `mongo shell` methods are available for transactions:
```
Session.startTransaction()
Session.commitTransaction()
Session.abortTransaction()
```
## Transactions and Atomicity
- `Multi-document transactions` are `atomic`:
    - When a transaction `commits`, all data `changes` made in the transaction are `saved` and `visible` `outside` the transaction.
    - When a transaction `aborts`, all data `changes` made in the transaction are `discarded` `without` ever becoming `visible`.

## Transactions and Operations
For transactions:
- You can specify `CRUD` operations on existing `collections`. 
- The `collections` `can` be in `different databases`.
- You `cannot read/write` to `collections` in the `config`, `admin`, or `local` databases.
- You `cannot write` to `system.*` collections.
- For `cursors` created `outside` of transactions, you `cannot` call `getMore` `inside` a transaction.
- For `cursors` created `in` a transaction, you `cannot` call `getMore` `outside` the transaction.
- `Operations` that `affect` the `database catalog`, such as `creating` or `dropping` a `collection` or an `index`, are `not allowed` in `multi-document transactions`. 

## Transactions in Applications
- `Regardless` of the `database` system, whether `MongoDB` or `relational databases`, `applications` should `handle errors` during transaction `commits` and `implement retry logic` for transactions.
- `Python` code example for `retry` transaction:
```python
def run_transaction_with_retry(txn_func, session):
    while True:
        try:
            txn_func(session)  # performs transaction
            break
        except (ConnectionFailure, OperationFailure) as exc:
            # If transient error, retry the whole transaction
            if exc.has_error_label("TransientTransactionError"):
                print("TransientTransactionError, retrying "
                      "transaction ...")
                continue
            else:
                raise

def commit_with_retry(session):
    while True:
        try:
            # Commit uses write concern set at transaction start.
            session.commit_transaction()
            print("Transaction committed.")
            break
        except (ConnectionFailure, OperationFailure) as exc:
            # Can retry commit
            if exc.has_error_label("UnknownTransactionCommitResult"):
                print("UnknownTransactionCommitResult, retrying "
                      "commit operation ...")
                continue
            else:
                print("Error during commit ...")
                raise

# Updates two collections in a transactions

def update_employee_info(session):
    employees_coll = session.client.hr.employees
    events_coll = session.client.reporting.events

    with session.start_transaction(
            read_concern=ReadConcern("snapshot"),
            write_concern=WriteConcern(w="majority")):
        employees_coll.update_one(
            {"employee": 3}, {"$set": {"status": "Inactive"}},
            session=session)
        events_coll.insert_one(
            {"employee": 3, "status": {
                "new": "Inactive", "old": "Active"}},
            session=session)

        commit_with_retry(session)

# Start a session.
with client.start_session() as session:
    try:
        run_transaction_with_retry(update_employee_info, session)
    except Exception as exc:
        # Do something with error.
        raise
```
## Read Concern/Write Concern/Read Preference
### Transactions and Read Concern
- Read Concern
    - The `readConcern` option `allows` you to `control` the `consistency` and `isolation` properties of the `data read` from `replica sets` and `replica set shards`.
- Read concern level

| level          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| "local"        | The query returns data from the instance with no guarantee that the data has been written to a majority of the replica set members (i.e. may be rolled back). Default for: reads against primary reads against secondaries if the reads are associated with causally consistent sessions. Read concern local is available for use with causally consistent sessions and transactions. For more information, see the "local" reference page.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| "available"    | The query returns data from the instance with no guarantee that the data has been written to a majority of the replica set members (i.e. may be rolled back). Default for reads against secondaries if the reads are not associated with causally consistent sessions. For sharded collections, "available" read concern provides the lowest latency reads possible among the various read concerns but at the expense of consistency as "available" read concern can return orphaned documents. Read concern available is unavailable for use with causally consistent sessions. For more information, see the "available" reference page. New in version 3.6.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| "majority"     | The query returns the data that has been acknowledged by a majority of the replica set members. The documents returned by the read operation are durable, even in the event of failure. To use read concern level of "majority", replica sets must use WiredTiger storage engine. For MongoDB 4.0.3+ (and 3.6.1+), you can disable read concern "majority". For more information, see Disable Read Concern Majority. Read concern majority is available for use with causally consistent sessions and transactions. NOTE For operations in multi-document transactions, read concern "majority" provides its guarantees only if the transaction commits with write concern “majority”. Otherwise, the "majority" read concern provides no guarantees about the data read in transactions. For more information, see the "majority" reference page.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| "linearizable" | The query returns data that reflects all successful majority-acknowledged writes that completed prior to the start of the read operation. The query may wait for concurrently executing writes to propagate to a majority of replica set members before returning results. If a majority of your replica set members crash and restart after the read operation, documents returned by the read operation are durable if writeConcernMajorityJournalDefault is set to the default state of true. With writeConcernMajorityJournalDefault set to false, MongoDB does not wait for w: "majority" writes to be written to the on-disk journal before acknowledging the writes. As such, majority write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set. You can specify linearizable read concern for read operations on the primary only. Read concern linearizable is unavailable for use with causally consistent sessions. Linearizable read concern guarantees only apply if read operations specify a query filter that uniquely identifies a single document. TIP Always use maxTimeMS with linearizable read concern in case a majority of data bearing members are unavailable. maxTimeMS ensures that the operation does not block indefinitely and instead ensures that the operation returns an error if the read concern cannot be fulfilled. For more information, see the "linearizable" reference page. |
| "snapshot"     | Only available for operations within multi-document transactions. If the transaction is not part of a causally consistent session, upon transaction commit with write concern "majority", the transaction operations are guaranteed to have read from a snapshot of majority-committed data. If the transaction is part of a causally consistent session, upon transaction commit with write concern "majority", the transaction operations are guaranteed to have read from a snapshot of majority-committed data that provides causal consistency with the operation immediately preceding the transaction start.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
Through the effective use of write concerns and read concerns, you can adjust the level of consistency and availability guarantees as appropriate, such as waiting for stronger consistency guarantees, or loosening consistency requirements to provide higher availability.

MongoDB drivers updated for MongoDB 3.2 or later support specifying read concern. 
- Operations in a transaction use the transaction-level read concern. That is, any read concern set at the collection and database level is ignored inside the transaction.

You can set the transaction-level read concern at the transaction start.

If the transaction-level read concern is unset, the transaction-level read concern defaults to the session-level read concern.
If transaction-level and the session-level read concern are unset, the transaction-level read concern defaults to the client-level read concern. By default, client-level read concern is "local" for reads against the primary. See also Transactions and Read Preference.
Multi-document transactions support the following read concern levels:

"local"
Read concern "local" returns the most recent data available from the node but can be rolled back.
"majority"
Read concern "majority" returns data that has been acknowledged by a majority of the replica set members (i.e. data cannot be rolled back) if the transaction commits with write concern “majority”. [1]
If the transaction does not use write concern “majority” for the commit, the "majority" read concern provides no guarantees that read operations read majority-committed data.
[1]	For a three-member Primary-Secondary-Arbiter (PSA) replica set architecture, you may have disabled read concern “majority” to avoid cache pressure. Disabling "majority" does not affect transactions; i.e. you can specify read concern "majority" for transactions even if read concern "majority" is disabled.
"snapshot"
Read concern "snapshot" returns data from a snapshot of majority committed data if the transaction commits with write concern “majority”.
If the transaction does not use write concern “majority” for the commit, the "snapshot" read concern provides no guarantee that read operations used a snapshot of majority-committed data.
Transactions and Write Concern
Transactions use the transaction-level write concern to commit the write operations. Operations inside transactions ignore write concerns.

TIP

Do not explicitly set the write concern for the individual write operations inside a transaction.

You can set the transaction-level write concern at the transaction start:

If the transaction-level write concern is unset, the transaction-level write concern defaults to the session-level write concern for the commit.
If the transaction-level write concern and the session-level write concern are unset, transaction-level write concern defaults to the client-level write concern. By default, client-level write concern is w: 1.
Multi-document transactions support the following write concern w values:

w: 1
Write concern w: 1 returns acknowledgement after the commit has been applied to the primary.

IMPORTANT

When you commit with w: 1, your transaction can be rolled back if there is a failover.

When you commit with w: 1 write concern, transaction-level "majority" read concern provides no guarantees that read operations in the transaction read majority-committed data.

When you commit with w: 1 write concern, transaction-level "snapshot" read concern provides no guarantee that read operations in the transaction used a snapshot of majority-committed data.

w: "majority"
Write concern w: "majority" returns acknowledgement after the commit has been applied to a majority (M) of voting members; i.e. the commit has been applied to the primary and (M-1) voting secondaries.
When you commit with w: "majority" write concern, transaction-level "majority" read concern guarantees that operations have read majority-committed data.
When you commit with w: "majority" write concern, transaction-level "snapshot" read concern guarantees that operations have from a synchronized snapshot of majority-committed data.
Transactions and Read Preference
Operations in a transaction use the transaction-level read preference.

Using the drivers, you can set the transaction-level read preference at the transaction start:

If the transaction-level read preference is unset, the transaction uses the session-level read preference.
If transaction-level and the session-level read preference are unset, the transaction uses the client-level read preference. By default, the client-level read preference is primary.
Multi-document transactions that contain read operations must use read preference primary.

All operations in a given transaction must route to the same member.



