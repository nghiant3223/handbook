# Redis Tips

- Delete keys by pattern:  
`redis-cli KEYS "pattern" | xargs redis-cli DEL`

- Batch get:  
`MGET member [member]`
