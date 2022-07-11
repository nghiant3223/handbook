# Differences Between Single Processor and Multiprocessor Systems
- Single processor system contains only one processor while multiprocessor systems may contain two or more processors.
- Single processor systems use different controllers for completing special tasks such as DMA (Direct Memory Access) Controller. On the other hand, multiprocessor systems have many processors that can perform different tasks. This can be done in symmetric or asymmetric multiprocessing.
- Single processor systems can be more expensive than multiprocessor systems. If n processor multiprocessor system is available, it is cheaper than n different single processor systems because the memory, peripherals etc. are shared.
- It is easier to design a single processor system as compared to a multiprocessor system. This is because all the processors in the multiprocessor system need to be synchronized and this can be quite complicated.
- Throughput of a multiprocessor system is more than a single processor system. However, if the throughput of n single processor systems is T then the throughput of n processor multiprocessor system will be less than T.
- Single processor systems are less reliable than multiprocessor systems because if the processor fails for some reason then system cannot work. In multiprocessor systems, even if one processor fails than the rest of the processors can pick up the slack. At most the throughput of the system decreases a little.
- Most modern personal computers are single processor systems while multiprocessors are used in niche systems only.

## Ref
- https://www.tutorialspoint.com/Single-Processor-Systems#:~:text=Differences%20Between%20Single%20Processor%20and%20Multiprocessor%20Systems&text=Single%20processor%20system%20contains%20only%20one%20processor%20while%20multiprocessor%20systems,(Direct%20Memory%20Access)%20Controller.