# ACID, MVCC, and how PostgreSQL handles concurrent queries
- `SQL` databases implement `ACID` in different ways
- `NoSQL` databases implement it `incompletely`
## Atomicity
- Either `every` task in a transaction gets `performed`, or they `all fail`.
```
BEGIN
    UPDATE account
    SET balance = balance - 100
    WHERE name = "Mike"
----------------- DATABASE FAILURE -------------------
    UPDATE account
    SET balance = balance + 100
    WHERE name = "KYLE"
COMMIT
```
## Isolation
- The result of running statements in a transaction `do not affect` the rest of the database until the transaction is `committed`.
- There’s multiple levels of isolation which prevent different things from happening.
- `Uncommitted read`level means no isolation (`XID1` will read uncommit value from `XID2`) -> cause `dirty read` -> incorrect information
- `Read committed` level prevents a `dirty read`.
```
    BEGIN XID1                          BEGIN XID2
    SELECT quantity, price
    FROM inventory
    WHERE item = "trike"
                                    UPDATE inventory
                                    SET quantity = quantity + 20
                                    WHERE item = "trike"
    SELECT sum(price * quantity)
    FROM inventory                  
    COMMIT                          FAILURE,rollback
```
- `Unrepeatable Read`: 
    - May not always want the most current data. Banks, for instance, prize consistency over availability.
    - How to fix? 
        - An current transaction only read data from transactions `committed before the start` of the current transaction
        - It is called the `repeatable data` isolation level. 
        - Applies at the `row level`.

```
BEGIN XID1              BEGIN XID2
SELECT quantity, price
FROM inventory
WHERE item = "trike"
(10, 12)
                        UPDATE inventory
                        SET quantity = quantity + 20
                        WHERE item = "trike"
                        
                        COMMIT
SELECT sum(price * quantity)
FROM inventory
(360)
COMMIT      
```

- `Phantom read`:
    - Instead of `updating` a row, we `insert` a new row into the table -> give an `unrepeatable read` (`repeatable data` isolation level only ensure an updated row, not new inserted row)
    - Solved by `serializable` isolation level:
        - Any `concurrent` execution of `a set of serializable transactions` is guaranteed to produce the `same effect` as running them `one at a time`.
        - `Slows down` performance, but if we are running a `bank`, we want to prevent any kind of mistake.
## Consistency
- Not always important, but crucial.
- Consistency ensures that a transaction can only bring the database from one consistent state to another, preserving database invariants
- Any `data` written to the database must be `valid` according to all defined rules, including `constraints`, `cascades`, triggers, and any combination thereof.
- This prevents database corruption by an illegal transaction.
- For example: a transaction `T` has `T1` and `T2`, `T1` substracts X 100 and `T2` add Y 100
    - The total amount before and after the transaction must be maintained. 
    - Total before `T` occurs = 500 + 200 = 700. 
    - Total after `T` occurs = 400 + 300 = 700. 
    - Therefore, the database is `consistent`. 
    - `Inconsistency` occurs in case `T1` completes but `T2` fails. As a result, `T` is incomplete.
## Durability
- PostgreSQL does write to `RAM` and to `disk` `concurrently`.
- Once the transaction has `completed` execution, the `updates` and `modifications` to the database are stored in and written to `disk` and they `persist` even if a `system failure` occurs.
- Updates now become `permanent` and are stored in `non-volatile` memory.
- The effects of the transaction are `never lost`. 
## ACID in PostgreSQL
- If a transaction starts that references any row of data in any way, it will be `locked` and place all the  other reads function in a queue.
- When it was unlocked after the write transaction commits, we execute all reads in the queue.
- It gives us complete `consistency`, but at the `cost` of `availability` and affect performace.
- How do we work around blocking reads during writes?
- PostgreSQL does this using `Multiversion Concurrency Control`, or `MVCC`.
- When you `update` a row on a table in `PostgreSQL`, it `adds another row` to the table `instead of writing to the old row`.
- What data is used to decide to show and hide the records to a specific queries? 
    - Each transaction has an id, we can keep track of which transactions changed which data by keeping the XIDs of the transactions that create or remove each row in the `xmin` and `xmax` columns of a table.
    - `PostgreSQL` will only show the data the `xmin` of which is not greater than the current XID, because that is a `future change`, and the `xmax` of a delete transaction is on that row is not equal or lesser than current XID, because that means it `was deleted`. (it means it is readable only if `xmin` <= current transaction id <= `xmax`)
    - `PostgreSQL` is `not completely immutable`
    - If it can prove a row can not be seen, it will delete it and allow that table to reuse that disk space for another row. 
    - What happens when we get to `the max possible transaction id`? The number will `wrap around` and `start from zero again`.
## MongoDB vs Postgres
- `Consistency`: `MongoDB` lacks of `consistency` of data constraints because `MongoDB` has no real datatype constraints.
- `Concurrency`: `MongoDB` uses `optimistic concurrency control`. Imagine the model that `PostgreSQL` uses, but with `timestamps`
    - Check if any `write timestamps` are `greater` than our `read timestamps`, and if so, we `rollback` and `start again`. We do the read again, and this time there is no conflict, so we end the transaction. (current read timestamp >= any `write timestamps` -> readable)
- The difference is, across multiple servers, keeping track of a current transaction is difficult because that is local, but `time is global`.
- For example: we split our database across two servers, `S1` and `S2`, and we make a `transaction` that `hits both`, they communicate over an `inconsistent` connection, and this model may break.
- How `MongoDB` handles this?
    - If `S1` gets hit first, and it records its timestamp.
    - However, it has not made its changes `‘visible’` yet, meaning a read to this data will still see the old data. 
    - It then tells `S2` to do its job, which records its timestamp.
    - `S1` takes this data, and then commits under `S2’s` timestamp.
    - Meaning, any read of either data that is still `inflight`, will see this timestamp during validation, and will roll back to reflect the new data.
