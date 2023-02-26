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
- Toil is operational activity tied to running a production service that tends to be manual, repetitive, automatable, tactical, devoid of enduring value, and that scales linearly as a service grows.
- Sources of toil include:
  - Interrupts (non-urgent service-related messages and emails)
  - On-call (urgent) response
  - Releases
  - Pushes
- The work of reducing toil and scaling up services is the “engineering”
  part of SRE.
#### Monitoring distributed systems
- Monitoring is a key attribute of SRE with the goal being to find out what
  is broken (or about to break) and why.
- If a system cannot self-heal, then an SRE should investigate the alert, identify and mitigate the issues, and determine the root cause.
- The priority metrics that SRE monitoring covers are:
  - Latency
  - Traffic
  - Errors
  - Saturation
#### Automate
- Automation is the raison d’être when it comes to SRE.
- The benefits of automation include:
  - Consistency
  - Time saving
  - Faster repairs and actions
  - Providing a platform that can be extended to other services
- The SRE writes code to automate management of the lifecycle of
  systems infrastructure, not their data.
#### Release engineering
- The SRE supports developers and release engineers to ensure new
  services and features are working well and can be supported in the
  future.
- The SRE will use tools developed by release engineers to ensure
  projects are released using consistent and repeatable methodologies.
  They will work together to develop strategies for:
  - Canarying changes
  - Pushing out new releases without interrupting services
  - Rolling back features that demonstrate problems
#### Simplicity
- The SRE approach to managing systems is keeping agility and stability
  in balance in the system.
- The SRE teams try to eliminate complexity in systems they onboard and for which they assume operational responsibility.
### ref
- [SRE Basics: Site Reliability Engineering Explained](https://www.bmc.com/blogs/sre-site-reliability-engineering/)