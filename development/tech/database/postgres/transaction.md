# Transactions
## Definition
- Bundles `multiple` steps into a `single`, `all-or-nothing` operation.
- The `intermediate` states between the steps are `not visible` to `other concurrent` transactions
- If some `failure occurs` that `prevents` the transaction from `completing`, then `none` of the steps `affect` the `database` at all.
- A transaction is said to be `atomic`, it either happens `completely` or `not at all`.
- `Once` a transaction is `completed`, it has been permanently `recorded` and `won't be lost` even if a `crash` happens.
- When `multiple` transactions are `running concurrently`, `each one` should `not be able to see` the `incomplete changes` made by others.
- The `updates` made so far by an `open transaction` are `invisible` to other transactions `until` the transaction `completes`.
- In `PostgreSQL`, a `transaction` is set up by `surrounding` the SQL commands of the transaction with `BEGIN` and `COMMIT` commands.
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100.00
    WHERE name = 'Alice';
-- etc etc
COMMIT;
```
- If we decide we do `not want to commit`, we can issue the command `ROLLBACK` instead of `COMMIT`, and all our `updates` so far will `be canceled`.
- `PostgreSQL` actually `treats` `every SQL statement` as `being executed within a transaction`. If you do `not` issue a `BEGIN` command, then each `individual statement` has an `implicit BEGIN` and `(if successful) COMMIT` `wrapped` around it.
- `Savepoints` allow you to `selectively discard parts` of the transaction, while `committing the rest`.
- `After defining` a savepoint with `SAVEPOINT`, you `can` if needed `roll back to the savepoint` with `ROLLBACK TO`. 
- All the transaction's database changes between defining the savepoint and rolling back to it are discarded, but changes earlier than the savepoint are kept.

After rolling back to a savepoint, it continues to be defined, so you can roll back to it several times. Conversely, if you are sure you won't need to roll back to a particular savepoint again, it can be released, so the system can free some resources. Keep in mind that either releasing or rolling back to a savepoint will automatically release all savepoints that were defined after it.

All this is happening within the transaction block, so none of it is visible to other database sessions. When and if you commit the transaction block, the committed actions become visible as a unit to other sessions, while the rolled-back actions never become visible at all.

Remembering the bank database, suppose we debit $100.00 from Alice's account, and credit Bob's account, only to find later that we should have credited Wally's account. We could do it using savepoints like this:

BEGIN;
UPDATE accounts SET balance = balance - 100.00
    WHERE name = 'Alice';
SAVEPOINT my_savepoint;
UPDATE accounts SET balance = balance + 100.00
    WHERE name = 'Bob';
-- oops ... forget that and use Wally's account
ROLLBACK TO my_savepoint;
UPDATE accounts SET balance = balance + 100.00
    WHERE name = 'Wally';
COMMIT;
This example is, of course, oversimplified, but there's a lot of control possible in a transaction block through the use of savepoints. Moreover, ROLLBACK TO is the only way to regain control of a transaction block that was put in aborted state by the system due to an error, short of rolling it back completely and starting again.

