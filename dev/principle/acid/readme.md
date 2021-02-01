# ACID

- Atomicity: All operations in a transaction must either succeed or all are rolled back

  - For Example: a transaction to transfer funds from one account to another involves making a withdrawal operation from the first account and a deposit operation on the second. If the deposit operation failed, you don’t want the withdrawal operation to happen either.[6]

- Consistency: The database integrity constraints are valid on completion of the transaction. Which demands that the data must meet all the validation rules. All validation rules must be checked to ensure consistency

  - For example: If a field-type in database is Integer, it should accept only Integer value's and not some kind of other. If you want to store other types in this field, consistency are violated. At this condition transaction will rollback.

- Isolated: Simultaneously occurring transactions do not interfere with each other. Contentious concurrent access is moderated by the database so that transactions appear to run sequentially

  - For example: a teller looking up a balance must be isolated from a concurrent transaction involving a withdrawal from the same account. Only when the withdrawal transaction commits successfully and the teller looks at the balance again will the new balance be reported.[6]

- Durable: Irrespective of hardware or software failures, the updates made by the transaction are permanent.

  - For example: InnoDB is a transactional storage engine for MySQL, which by definition must guarantee durability. It accomplishes this by writing its updates in two places: once to a transaction log and again to an in-memory buffer pool. The transaction log is synced to disk immediately, whereas the buffer pool is only eventually synced by a background thread.[7]