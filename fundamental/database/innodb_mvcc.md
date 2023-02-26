# InnoDB Multiversion Concurrency Control

MVCC as a twist on row-level locking; it avoids the need for locking at all in many cases and can have much lower overhead. Depending on how it is implemented, it can allow nonlocking reads, while locking only the necessary rows during
write operations.

MVCC works by keeping a snapshot of the data as it existed at some point in time. This means transactions can see a consistent view of the data, no matter how long they run. It also means different transactions can see different data in the same tables at the same time! Each storage engine implements MVCC differently. Some of the variations include optimistic and pessimistic concurrency control

InnoDB implements MVCC by storing with each row two additional, hidden values that record when the row was created and when it was expired (or deleted). Rather than storing the actual times at which these events occurred, the row stores the system version number at the time each event occurred. This is a number that increments each time a transaction begins. Each transaction keeps its own record of the current system version, as of the time it began. Each query has to check each row’s version numbers against the transaction’s version. Let’s see how this applies to particular operations when the transaction isolation level is set to **REPEATABLE READ**:

- **SELECT**  
  InnoDB must examine each row to ensure that it meets two criteria:
  - InnoDB must find a version of the row that is at least as old as the transaction (i.e., its version must be less than or equal to the transaction’s version). This ensures that either the row existed before the transaction began, or the transaction created or altered the row.
  - The row’s deletion version must be undefined or greater than the transaction’s version. This ensures that the row wasn’t deleted before the transaction began.

  Rows that pass both tests may be returned as the query’s result.
  
- **INSERT**
  InnoDB records the current system version number with the new row.
- **DELETE**
  InnoDB records the current system version number as the row’s deletion ID.
- **UPDATE**
  InnoDB writes a new copy of the row, using the system version number for the new row’s version. It also writes the system version number as the old row’s deletion version
 
The result of all this extra record keeping is that most read queries never acquire locks. They simply read data as fast as they can, making sure to select only rows that meet the criteria. The drawbacks are that the storage engine has to store more data with each row, do more work when examining rows, and handle some additional housekeeping operations.

MVCC works only with the REPEATABLE READ and READ COMMITTED isolation levels. READ UNCOMMITTED isn’t MVCC-compatible because queries don’t read the row version that’s appropriate for their transaction version; they read the newest version, no matter what. SERIALIZABLE isn’t MVCC-compatible because reads lock every row they return.

By using MVCC, InnoDB and XtraDB solve the phantom read problem.
