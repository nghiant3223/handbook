# SRE fundamentals: SLIs, SLAs and SLOs
### Defining the terms of site reliability engineering
- Without them, you cannot know if your system is reliable, available or even useful.
- If they don’t tie explicitly back to your business objectives, then you don’t have data on whether the choices you make are helping or hurting your business.
### Service-Level Objective (SLO)
- When we set out to define the terms of SRE, we wanted to set a precise numerical target for system availability. We term this target the availability Service-Level Objective (SLO) of our system.
- Any discussion we have in the future about whether the system is running sufficiently reliably and what design or architectural changes we should make to it must be framed in terms of our system continuing to meet this SLO.
- Define the lowest level of reliability that you can get away with for each service, and state that as your SLO.
- Every service should have an availability SLO—without it, your team and your stakeholders cannot make principled judgments about whether your service needs to be made more reliable (increasing cost and slowing development) or less reliable (allowing greater velocity of development).
### Service-Level Agreement (SLA)
- An SLA normally involves a promise to someone using your service that its availability SLO should meet a certain level over a certain period, and if it fails to do so then some kind of penalty will be paid.
- If you’re charging your customers money, you will probably need an SLA.
- The availability SLO in the SLA is normally a looser objective than the internal availability SLO.
- If you have an SLO in your SLA that is different from your internal SLO, as it almost always is, it’s important for your monitoring to measure SLO compliance explicitly.
### Service-Level Indicator (SLI)
- We also have a direct measurement of a service’s behavior: the frequency of successful probes of our system. This is a Service-Level Indicator (SLI).
### Summary
- If you’re building a system from scratch, make sure that SLIs and SLOs are part of your system requirements.
- If you already have a production system but don’t have them clearly defined, then that’s your highest priority work.
### Ref
- [SRE fundamentals: SLIs, SLAs and SLOs](https://cloud.google.com/blog/products/devops-sre/sre-fundamentals-slis-slas-and-slos)