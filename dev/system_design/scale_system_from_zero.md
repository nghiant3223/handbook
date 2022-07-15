# How to design a system to scale to your first 100 million users
## Start from scratch
- Start by designing a basic app with some users.
- The simplest way is to deploy the entire app on a single server.
  - A website (include APIs) is running on a webserver like Apache
  - A database like Oracle
- Clients connect to the DNS system to obtain the Internet Protocol (IP) address of the server where our system is hosted. Once the IP address is obtained, the requests are sent directly to our system.
- Domain Name System (DNS) server is used as a paid service provided by the hosting company and is not running on your own server.
![img.png](img/simple_system.png)
- Disadvantages:
  - If the database fails, the system fails
  - If the webserver fails, the entire system fails
- We don’t have failover and redundancy. If a server goes down, all goes down.
## The Art of Scalability
- Scalability usually means an ability to handle more users, clients, data, transactions, or requests without affecting the user experience by adding more resources.
### Scaling up
- There are many ways to scale vertically
  - Adding more I/O capacity by adding more hard drives in RAID arrays.
  - Improving I/O access times by switch to solid-state drivers (SSDs).
  - Switching to a server with more processors.
  - Improving network throughput by upgrading network interfaces or installing additional ones.
  - Reducing I/O operations by increasing RAM.
- Vertical scaling is a good option for small systems and can afford the hardware upgrade but it also comes with serious limitations as follows
  - It’s impossible to add unlimited power to a single server
  - When we upgrade RAM to the system, we must shut down the server and so, if the system just has one server, downtime is unavoidable
  - Powerful machines usually cost a lot more than popular hardware
- Scaling up not only applies to hardware terms but also in software terms,  for example, it includes optimizing queries and application code
- With the growth of the number of users, one server is never enough
- There are some advantages when adding more servers:
  - The webserver can be tuned differently than the database server
  - A webserver needs a better CPU and a database server thrives on more RAM
  - Using separate servers for the web tier and data tier allows them to scale independently of each other
### Scaling out
- We add more entities (machines, services) to our pool of resources.
- Horizontal scaling often initially costs more because we need more servers for the most basic, but it pays off at the later stage
- Increasing the number of servers means that more resources need to be maintained.
- The code of the system also needs changes to allow parallelism and distribution of work among multiple servers.
### Using load a balancer to balance the traffic across all nodes
- A load balancer is a specialized hardware or software component that helps to spread the traffic across a cluster of servers to improve responsiveness and availability of the system using various algorithms.
- It can be used in various places, for example; between the Web Servers and the Database servers and also between the Client and the Web Servers.
- HAProxy and NGINX are two popular open-source load balancing software.
- If server 1 goes offline, all the traffic will be routed to server 2 and server 3. The website won’t go offline. You also need to add a new healthy server to the server pool to balance the load.
- When the traffic is growing rapidly, you only need to add more servers to the web server pool and the load balancer will route the traffic for you.
- Load balancers employ various policies and work distribution algorithms to optimally distribute the load as follows
  - Round robin: each server receives requests in sequential order
  - Least number of connections: the server with the lowest number of connections will be directed to the request
  - Fastest response time: the server that has the fastest response time (either recently or frequently) will be directed to the request
  - Weighted: the more powerful servers will receive more requests than the weaker ones
  - IP Hash: a hash of the IP address of the client is calculated to redirect the request to a server
- Software load balancing is a cheap alternative to hardware load balancers. It operates at layer 4 (network layer) and layer 7 (application layer)
  - Layer 4: the load balancer uses the information provided by TCP at the network layer. At this layer, it usually selects a server without looking at the content of the request.
  - Layer 7: the requests can be balanced based on information in the query string, cookies, or any header we choose as well as the regular layer information including source and destination addresses.
### Scaling a relational database
- Relational database systems come with their challenges, especially when we need to scale them.
- There are many techniques to scale a relational database: master-slave replication, master-master replication, federation, sharding, denormalization, and SQL tuning
  - Replication usually refers to a technique that allows us to have multiple copies of the same data stored on different machines.
  - Federation (or functional partitioning) splits up databases by function
  - Sharding is a database architecture pattern related to partitioning by putting different parts of the data onto different servers and the different user will access different parts of the dataset
  - Denormalization attempts to improve read performance at the expense of some write performance by coping of the data are written in multiple tables to avoid expensive joins..
  - SQL tuning.
#### Master-slave replication
- The master-slave replication technique enables data from one database server (the master) to be replicated to one or more other database servers (the slaves) like in the below figure.
![img.png](img/master_slave_db.png)
- A client would connect to the master and update data
- The data would then ripple through the slaves until all the data is consistent across the servers
- Bottleneck:
  - If the master server goes down for whatever reason, the data will still be available via the slave, but new writes won’t be possible.
  - We need an additional algorithm to promote a slave to a master.