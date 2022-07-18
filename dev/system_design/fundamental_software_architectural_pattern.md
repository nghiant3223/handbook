# Fundamental Software Architectural Patterns
- Patterns are essentially reusable solutions to common problems
- They define a solution for a variety of quality attributes and contemplate multiple components in a software system.
- The scope is broader for the architectural patterns while design patterns represent a way to structure classes to build the best internal structure.
## Layers Pattern
- Perhaps the most used (and abused) pattern which is the layered pattern.
- Partitioning a system into separate layers, organizing components within them by related criteria, allows developers and teams to work better together.
- The layers promote good practices of low coupling and high cohesion.
- Each module must be assigned to one and only one layer, where upper layers are allowed to use lower layers (one way only)
- It is a design that is justified in terms of maintainability, offering that different teams can collaborate on different modules in parallel
- Example:
![img.png](img/layer_pattern.png)
## Client Server Pattern
- A central authority (Server) offers one or more services that are consumed by one or more consumers (Client).
- This model offers the possibility of building distributed systems where the server centralizes the resources and workload in one place so that later multiple consumers use this data independently.
- User applications consume the data and are responsible for providing an interface to interact with them, so the client and server often cooperate to achieve a common system
## Pipe and Filter Pattern
- With the pipe and filters pattern, each component named filter is responsible for a single transformation or operation on the data.