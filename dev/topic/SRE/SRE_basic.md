# SRE Basics: Site Reliability Engineering Explained

### Introduction
- The cloud is well and truly becoming the default mode for running
  software in the digital age.
- When it comes to managing application
  performance and stability while responding to changes in business
  need, modern approaches such as SRE are fast taking root.
### What is site reliability engineering?
- SRE is a discipline that applies
   aspects of software engineering to IT operations, with the goal of
   creating ultra-scalable and highly reliable software systems.
```
SRE is what happens when you ask a software engineer to design an
operations team.
```
- Using engineers with software development backgrounds to
  perform work done by an operations team.
- Banking on these engineers to automate work that was
  previously done manually.
- While continuing to carry out some manual operational tasks, like ticketing and handling escalations, the SRE team is expected to primarily focus on engineering new products and services by automating and scaling applications and production environment systems because service usage increases, the effort to
  manage the operational load increases.
- Automation reduces the
   operational overhead, resulting in extra bandwidth that can be applied
   in supporting new features and improvements to existing applications.
- SRE also helps to reduce the
  friction of handovers from product development teams since they
  understand their perspective, even though the SRE’s focus is on
  operational stability.
### How does SRE work?
- Reliability is the ability of a product, service, or other configuration item to perform
  its intended function for a specified period of time or number of
  cycles.
- An SRE team has to be responsible for these
  components of each of their services:
  - Availability
  - Latency
  - Performance
  - Efficiency
  - Change management
  - Monitoring
  - Emergency response
  - Capacity planning
- The SRE priority is on making sure that existing services
  work as they should.
- SREs run services and are ultimately responsible for
  the health of these services.
- The SRE’s day-to-day activity involves building and operating large distributed computing systems.
- The foundational practices in making a
  service reliable are:
  - Supporting existing services by monitoring and addressing any
    issues arising.
  - Then building up to scaling.
  - Finally, supporting development of new product features.
- By working with users and product teams, the SRE is able to
  innovatively develop automated tools that result in better production
  environments characterized by:
  - Scalability
  - Graceful degradation during failure
  - Integration with other services and systems
  - Additional benefits
### SRE principles
#### Embracing risk
- SREs do not believe in 100% reliable services.
- SRE seeks to balance the risk of unavailability with the goals of rapid innovation and efficient service operations, so that users’ overall happiness is optimized.
#### Using Service Level Objectives
- To understand and therefore deliver services that meet the needs of
  users, service level objectives (SLOs) are used.
- SLOs are a target value
  or range of values for a service level that is measured by a Service Level
  Indicator (SLI) such as:
  - Availability
  - Error rate
  - Request latency
  - Throughput
  - Other indicators
- The structure for SLOs is either:
  - SLI ≤ target
  - Lower bound ≤ SLI ≤ upper bound
- Choosing and publishing SLOs to users sets expectations about how a
  service will perform, which helps reduce unfounded complaints to
  service owners about service performance (e.g., slowness).
#### Eliminating toil

### ref
- [SRE Basics: Site Reliability Engineering Explained](https://www.bmc.com/blogs/sre-site-reliability-engineering/)