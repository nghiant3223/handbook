# MySQL Pitfalls

- You should escape `_` and `%` for `LIKE` operand
- Default collation is case insensitive and accent-insensitive
- `INSERT ... ON DUPLICATE ... UPDATE` may cause deadlocks
- Transaction should be as small as possible
- Do not name column after `key`, it causes syntax error
- Whenever you encounter deadlocks, try adding reasonable indices to prevent range scan
- To prevent single `INSERT`, `UPDATE` for multiple records, try `INSERT ... ON CONFLICT DO UPDATE` for batch upsert
- Use `utf8mb4` encoding for string that requires emojis
- Use `utf8mb4` encoding and `utf8mb4_bin` collation for string that requires accent-sensitive
- Index may not be used even when their key parts appear in `WHERE`, it's up to the query optimizer whether to use the index or not
- If you have a column of type number `X` that must be positive you have a business to decrement it, make your query look like `UPDATE ... SET X=X-1 WHERE X >= 0` for safety.
