# Server Overload Definition
- Server Overload happens when conditions cause a server to exhaust its resources so that it fails to handle incoming requests. 
- For example, the server will no longer be responsive to requests from its email and web servers as it fails to process them, and the applications will no longer function for users.
## What is Server Overload?
- There are several factors that help the server handle its load: hard drive speed, memory, and processor speed.
- Virtual memory or hard drive space and bus speeds may also affect how the server handles the load, but neither is typically implicated in server overload.
- In many cases operations are using too much bandwidth, and in other situations the system uses too much RAM or runs out of processor power.
- When it is overloaded at any given point, it responds too slowly or not at all, and that is reflected in load speeds for websites and user experience with applications and tools
## Why is my Server Overloaded?
- Several common causes:
  - Sudden natural traffic spikes: If too many users attempt to use a site at once, it can crash a server, or cause server overload
  - Unavailable servers: At times, one server becomes unavailable due to sudden malfunction, hacking, or even planned maintenance (Planned maintenance is the process of detailing what materials, tools, tasks, and services are required to solve a problem. The purpose of planned maintenance is to determine what work needs to be completed and how it needs to be done.). At these times, the backup server handles all extra traffic, and can easily experience server overload.
  - Malware such as worms and viruses: when a worm or virus infects enough computers or browsers, this type of malware can disrupt normal operations by causing abnormally high server traffic and sudden network spikes. This results in web server overload.
  - DoS or DDoS denial of service attacks: Hackers launch denial-of-service DoS attacks and distributed-denial-of-service DDoS attacks to render a server unavailable to intended users. These malicious actors flood the network with false requests, and cause the server to deny real requests, crashing it.
## How to Fix Server Overload
- Server overload error codes: the server returns an HTTP error code, such as 503. 
  - A 503 error code means the service is temporarily unavailable due to server overload.
  - A 504 gateway timeout error or 504 server overload error code signals an overload because one server is taking too long to respond to another.
  - 408, 500, and 502 are also server overload error codes, all of which signal inappropriate overload conditions.
- Delayed requests: the server delays requests, and responses take longer than usual.
- Reset or denied TCP connections: before users see any content returned, the server resets or denies TCP connections.
- Partial content: the server returns just a portion of the content the user requests.
## How to Prevent Server Overload with Load Balancing
- Load balancing distributes network traffic across an organization’s servers as a group, easing the flow of incoming traffic to each server.
- The load balancer sits between the servers and the client and uses an algorithm to route requests.
- If one server fails, the load balancer avoids system disruption by redirecting traffic to other functional servers.
## Reference
- https://avinetworks.com/glossary/server-overload/#:~:text=Server%20Overload%20happens%20when%20conditions,no%20longer%20function%20for%20users.
- https://www.upkeep.com/learning/planned-maintenance