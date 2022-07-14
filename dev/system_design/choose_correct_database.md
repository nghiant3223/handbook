# How to Choose The Right Database for Your Application?
## Introduction
- Working with a database you’re already familiar with is perfectly fine if performance is not an important requirement for your system.
- But consider a situation when your application grows and after a couple of years your app starts facing some issues. It will become a headache for developers and administrators to fix the issue.
- It doesn't matter you are working on a project from scratch, or you’re already working on a mature project, it’s important to know the limitations of the database and identify when to add another type of DB in your project.
- There are more than 300 database management systems available in the market and choosing the one can be overwhelming for developers.
- A variety of options available in relational (MySQL, PostgreSQL, Oracle DB, etc) and non-relational (MongoDB, Apache HBase, Cassandra, etc.)
- You need to understand none of them fits on all kinds of projects requirement. Each one of them has some strengths and weaknesses.
## How do you make this decision when you’re architecting a given system?
- Ask a few important questions related to your project: 
  - How much data do you expect to store when the application is mature?
  - How many users do you expect to handle simultaneously at peak load?
  - What availability, scalability, latency, throughput, and data consistency does your application need?
  - How often will your database schemas change?
  - What is the geographic distribution of your user population?
  - What is the natural “shape” of your data?
  - Does your application need online transaction processing (OLTP), analytic queries (OLAP), or both?
  - What ratio of reads to writes do you expect in production?
  - What are your preferred programming languages?
  - Do you have a budget? If so, will it cover licenses and support contracts?
  - How strict are you with invalid data being sent to your database? (Ideally, you are very strict and do server-side data validation before persisting it to your database)
## Integration
- What system you need to integrate together?
- Make sure that your database management system can be integrated with other tools and services within your project.
- For example, if you have a big analytics job that’s currently running an Apache spark then probably you want to limit yourself to external databases that can connect easily to apache spark.
- Another example is ArangoDB which has excellent performance but libraries for this DBMS are still young and lack support. Using ArangoDB in combination with other tools may be risky, so the community suggests avoiding ArangoDB for complex projects.
## Scaling Requirement
- How much data are you really talking about? Is it really going to grow unbounded over time?
  - if so then you need some sort of database technology that is not limited to the data that you can store on one PC
  - You need to look at something like Cassandra or MongoDB or HBase where you can actually distribute the storage of your data across an entire cluster and scale horizontally instead of vertically.
- While choosing a database you also need to think about the transaction rate or throughput which means how many requests you intend to get per second.
  - Databases with high throughput can support many simultaneous users. If we are talking about thousands then again a single database service is not going to work out.
  - This is especially important when you are working on some big websites where we have a lot of web servers
  - You will have to choose a database that is distributed and allows you to spread out a load of those transactions more evenly.
  - In this case, NoSQL databases are a good choice instead of RDBMS.
## Support Consideration
  