# MySQL Pitfalls

- You should escape _ and % for `LIKE` operand
- Default collation is case insensitive and accent-insensitive
- `INSERT ... ON DUPLICATE ... UPDATE` may cause deadlocks
- Transaction should be as small as possible
- Do not name column after 'key', it causes syntax error
- Whenever you encounter deadlocks, try adding reasonable indices to prevent range scan
