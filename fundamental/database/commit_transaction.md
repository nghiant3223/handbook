# Commit transaction
- Until you commit a transaction:
  - You can see any changes you have made during the transaction by querying the modified tables, but other users cannot see the changes. After you commit the transaction, the changes are visible to other users' statements that execute after the commit.
  - You can roll back (undo) any changes made during the transaction with the ROLLBACK statement