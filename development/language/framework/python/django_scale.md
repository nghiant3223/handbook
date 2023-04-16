# Scaling Django for millions of users
## Scalable Architecture
- Verticle scaling
- Horizontal scaling
    - Add more machines and distribute traffic across your machines with the help of a `load balancer`.
    - `Microservice`: Splitting applications to individual services (SOA) help you scale them individually as your load increases
    - Requires you to make your app `stateless` and `keep the state “outside”`. This can be achieved in Django with `Cache Backends` (`Memcache` or `Redis`) to `store data on another server` to achieve statelessness.
## Connections and Connection Pooling
-`CONN_MAX_AGE` param in settings.py: defines the `maximum lifetime` of your `connection`.
- By default `Django` `closes the connection` at `the end of each request`.
- Should consider setting  `CONN_MAX_AGE` to `None` (unlimited persistent connections) or a `suitable value` depending on your request volume at the applications’ end. 
- Use tools like `PgBouncer` that help set the pool size, the max clients you want to handle at any moment (`default_pool_size`), and the number of clients that can connect to the DB ( `max_client_conn`).
## Talking of Databases
- Choosing the `right database`: for example: `Postgres` for its `performance`, `proven capabilities` at `scale`, `data integrity` and it keep updating version.
- `Indexes` – Adding appropriate indexes can speed up your DB (SELECT/UPDATE/INSERT) Queries and reduce the time to respond to users. It is best to look at slow (above `30 ms`) queries.
## Django Middleware
- Removing the `extra middleware` that your app is `not benefiting from` because Every request that is made to the Django backend passes through these middleware and adds extra time (`20-30ms`) to the request/response cycle. 
- The place to remove may be from settings > `INSTALLED_APPS` and settings > `MIDDLEWARE`.
## Django Code Optimizations
- `select_related()` and `prefetch_related()`: to prevent `N+1` problems.
- `Bulk queries` are preferred to `insert/update large amounts` of datasets (typically `1000` to `5000` records) using `bulk_create()` and `bulk_update()`.
- `Only querying specific information` from the DB tables. For instance, using functions like `values()`, `only()`
## Scalable Infrastructure
- `Design` the infrastructure to keep `scalability` and `maintenance` in mind. 
- `Docker` containers managed by `Kubernetes` can help orchestrate the containers and allow to `scale` (up or down) the `number of nodes` at run time `without any down time`.
- `Serverless` architecture
    - Good for use cases where `the number of requests has short and large bursts` or where `the load is event-driven`, take an action on an event, something that is stateless.
    - Not suited for distributed, stateful processing, long-running jobs. `AWS Lambda` is a good option for this case.
- `Amazon RDS` can help you `scale vertically` in case the `traffic increases suddenly`
## What gets measured, gets analyzed and acted upon
- This is the `most important`
- How do you figure something is wrong? Where is the bottleneck – is the CPU usage high or is it the Memory? 
- Setting up `monitoring systems` to identify issues and also to understand if your changes indeed fixed the issues.
- Setting up `debug logs` in Django `settings` > `LOGGING` section also helps in `understanding the time it takes to run a query`.
- Ideally should make sure that the `response time` is `under 100ms`, and in turn make sure `queries` are executed `within 20ms`. Note: should be enabled only in development setup.
## Conclusion
- Keep `monitoring`, keep `measuring` and keep `fixing`
## Reference
- [Scaling Django for millions of users
](https://technobeans.com/2020/12/01/scaling-django-for-millions-of-users/)