# `MaxIdleConnection` and `MaxOpenConnection`

The db pool may contain 0 or more idle connections to the database. These were connections that were made, used, and rather than closed, were kept around for future use. The number of these we can keep around is `MaxIdleConns`.

When you request one of these idle connections, it becomes an `Open` connection, available for you to use. The number of these you can use is `MaxOpenConns`.

Now, there is no point in ever having any more idle connections than the maximum allowed open connections, because if you could instantanously grab all the allowed open connections, the remain idle connections would always remain idle. It's like having a bridge with four lanes, but only ever allowing three vehicles to drive across it at once.

Therefore, we would like to ensure that

```
MaxIdleConn <= MaxOpenConns
```

The functions are written to preserve this invariant by reducing `MaxIdleConns` whenever it exceeds MaxOpenConns. Note that the documentation says, only MaxIdleConns is ever reduced to match `MaxOpenConns`, the latter is never true.

To answer the question of why a developer might want to adjust these separately: consider the case of an application that is usually quiet, but occasionally needs to open a large number of connections. You may wish to specify a large MaxOpenConns, but a very small `MaxIdleConns`, to ensure that your application can open as many connections in requires whenever it needs to, but releases these resources quickly, freeing up memory both for itself and the database. Keeping an idle connection alive is not free, and it's usually done because you want to turn it into usable connection soon.

So the reason there are two numbers here is that these are two parameters that you might have a good reason to vary individually. Of course, the semantics of the API mean that if you don't care about setting both these values, you can just set the one that you care about, which is probably `MaxOpenConns`
