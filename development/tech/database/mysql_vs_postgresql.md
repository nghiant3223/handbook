# MySQL vs PostgreSQL

|                      | PostgreSQL                                                                                                                   | MySQL                                                                       |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| Architecture         | Object relational; multiprocess                                                                                              | Relational; single process                                                  |
| Data types supported | Numeric, date/time, character, boolean, enumerated, geometric, network address, JSON, XML, HSTORE, arrays, ranges, composite | Numeric, date/time, character, spatial, JSON                                |
| Indexes supported    | B-tree, hash, GiST, SP-GiST, GIN, and BRIN                                                                                   | Primarily B-tree; R-tree, hash, and inverted indexes for certain data types |
| Performance          | Suited for applications with high volume of both reads and writes                                                            | Suitable for applications with high volume of reads                         |
| Security             | Access control, multiple encrypted connection options                                                                        | Access control, encrypted connections                                       |
| Support              | Community support. Companies that have their own release of PostgreSQL may offer support around it.                          | Community support, plus vendor-provided support contracts                   |

## Architecture
- PostgreSQL is an object-relational database, while MySQL is purely relational.
- PostgreSQL offers more complex data types and allows objects to inherit properties, but it also makes working with PostgreSQL more complex.
- PostgreSQL has a single, ACID-compliant storage engine.
- MySQL has support for 16 different storage engines suitable for different use cases (default is InnoDB).
- PostgreSQL spawns a new system process with its own memory allocation for each client connection it establishes, so it requires a lot of memory on systems with a high number of client connections.
- MySQL uses a single process and maintains one thread (or path of execution) per connection, which works well for most applications of less than enterprise scope.
- PostgreSQL also offers an INSTEAD OF trigger, and can execute complex SQL statements in a trigger using functions.
- PostgreSQL offers in addition the ability to call procedures written in languages other than SQL.
## Data types
- Postgres offers a wider variety of data types than MySQL.
- If your application deals with unstructured data, PostgreSQL may be better.
- If you’re using only basic character and numeric data types, both databases will suit you.
## Indexes
- With multiple indexing options to pick from, you can fine-tune your database performance as your data grows to get faster query responses from your database and an improved user experience for your application users.
## Security
- Both databases support user and group management and granting SQL privileges to roles.
- PostgreSQL: IP-based client filtering, authentication using PAM and Kerberos
- MySQL: PAM, native windows services, and LDAP for user authentication
- In terms of security, the two databases have comparable options.
## Support and documentation
- For both, can find documentation online
- Postgres: because it’s not developed by a company, lacks support engineers, but its community support forums are good.
- MySQL: offers similar forums, as well as paid support plans you can buy from Oracle.
## Which Is Better?
- PostgreSQL for any application:
  - Might grow to enterprise scope
  - Complex queries
  - Frequent write operations.
- MySQL: 
  - You’re new to the world of databases
  - Don’t expect your application to scale up
  - A quick tool for prototyping
- Other considerations:
  - Your cloud platform provider might offer benefits when it comes to running one database over the other
  - The application framework you use might be better suited for one
  - Your fellow developers may have opinions
  - MySQL is more widely used than PostgreSQL -> more developers and DBAs are familiar with it, and more third-party tools are available for it.
## Extracting database data for Business intelligence
- Both are operational databases, meant for transaction processing and not for analytics.
- To get a complete 360-degree view of your business, you should extract transactional data from your PostgreSQL or MySQL database and feed it into a data warehouse, where it can be used to power your data analytics and business intelligence (BI) tools like Tableau and Looker
## Ref
- https://www.fivetran.com/blog/postgresql-vs-mysql#:~:text=PostgreSQL%20is%20an%20object%2Drelational,%2C%20ACID%2Dcompliant%20storage%20engine.