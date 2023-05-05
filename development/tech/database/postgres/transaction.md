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
- All the transaction's database `changes` `between` defining the `savepoint` and `rolling back to it` are `discarded`, but `changes` `earlier` than the `savepoint` are `kept`.
- After `rolling back` to a `savepoint`, it `continues` to be `defined`, so you can `roll back` to it `several times`.
- Either `releasing` or `rolling back` to a `savepoint` will `automatically release` all `savepoints` that were defined `after it`.
- If you `commit` the transaction block, the `committed actions` become `visible` as a `unit` to other `sessions`, while the `rolled-back actions` `never` become `visible` at all.
- `ROLLBACK TO` is the `only way` to `regain control` of a `transaction` block that was put in `aborted state` by the system `due to an error`, it will roll back `completely` and `starting` again.
- Used in conditional logic
```sql
START TRANSACTION;
SAVEPOINT before_update;

SELECT COUNT(*) INTO @count FROM customers WHERE state = 'California';

IF @count > 0 THEN
  UPDATE customers SET phone = '555-5555' WHERE state = 'California';
  COMMIT;
ELSE
  ROLLBACK TO before_update;
END IF;
```
- Used in Undoing a partial update, when it failed, you can run `ROLLBACK TO before_update`;
```sql
START TRANSACTION;
SAVEPOINT before_update;
UPDATE employees SET salary = salary * 1.1 WHERE job_title = 'manager';
```