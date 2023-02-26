# SQLite vs MySQL: 5 Critical Differences
## Introduction to SQLite
- SQLite is an Open-Source Relational Database Management System [RDBMS] designed to work without a Database Administrator
- It is self-contained, file-based, and is known for its portability, performance in low memory environments, and reliability
- The data transactions carried out in SQLite are ACID compliant
- Most Relational Database engines are based on a server architecture that allows the program to run on a host server to communicate with processes to relay requests
- SQLite is based on a serverless architecture where any process can access the database reads and write to the database disk files directly
- Due to this functionality, all programs that are supposed to work with SQLite do not require additional setup, they just need permission to access the disk which simplifies the setup process
- The software library is Open-Source, it’s free and does not require any licenses to set up
## Key Features of SQLite
- Lightweight Installation: The SQLite library is small and can take up less than 600 KB of space.
- Ease of Use: SQLite does not require additional setup while installing on the system, also called Zero-Configuration database.
- Portability: The entire database is stored as a single file and can be shared via removable media of FTP [File Transfer Protocol].
- Embedded Applications: Perfect for database applications that require portability without future expansion such as mobile applications or games.
- Testing: Offers In-Memory mode to run tests without any overhead of actual Database Applications.
## SQLite vs MySQL: Architecture
### SQLite
- An Embedded Database which is a Database Engine that runs as a part of an app
- The SQLite solution is server-less and self-contained
### MySQL
- Requires a server and interaction of client and server architecture over a network
## SQLite vs MySQL: Data Types Supported
### SQLite
- Blob
- Integer
- Null
- Text
- Real
### MySQL
- Many types
## SQLite vs MySQL: Storage and Portability
### SQLite
- The SQLite software library is around 250 KB in size and can directly store the data into a single file
- The single files make it easier to transfer and share the data, as no configuration is required and processes can be carried out with a minimal amount of support
### MySQL
- The MySQL software library is around 600 MB in size
- The data needs to be condensed into a single file for sharing, this process can take a lot of time
## SQLite vs MySQL: Multiple User Access and Scalability
### SQLite
- SQLite’s scalability is limited and only appropriate for smaller databases
- Since the platform does not have any user management facility, it is not suitable for multiple user access
### MySQL
- MySQL on the other hand can handle a larger database size and offers a well-designed user management system
## SQLite vs MySQL: Security and Ease of Setup
### SQLite
- There is no inbuilt authentication process offered by SQLite and the data can be accessed by anyone
- The platform is easy to set up not requiring much configuration
### MySQL
- MySQL on the other hand offers a lot of security features such as user authentication using passwords, SSH, etc
- The setup process of MySQL is more complex there are a lot of resources available online to guide the user
## SQLite vs MySQL: Limitations of SQLite
- Limited Throughput and Size: While SQLite can support database size upto 140 TB, the optimal size of the database recommended for SQLite is only 1 TB.
- Network Requirements: Being a Serverless Database, SQLite does not provide direct access to data, access is rather built into the application. Thus if the deployment is present on a different system a high bandwidth and low latency engine-to-disk connection is required over the network, increasing the deployment costs
- Concurrency Limitations: Only one write application is allowed at a time on the SQLite Database, so it is not appropriate for applications requiring multiple writes
- Lack of User Management: No user management system/functionality is available as the data is directly written and read on an ordinary disk file which requires only one level/type of permission for access
- Lack of Security: Serverless Databases are inherently less secure in their implementation. Other security measures such as access control are not natively available
## SQLite vs MySQL: Limitations of MySQL
- Licensing Fees: While the basic community edition of the software is free; the commercial versions of the software require proprietary licenses
- Limited Basic Feature-set: As there is an option of licensed editions and other third-party tools, the community MySQL edition provides only limited functionality
- Delayed Updates: Since the acquisition of MySQL by Oracle in 2009, users have complained that the bug resolution and, software updates and development has been very slow compared to industry standards
- SQL Compliance: MySQL is developed keeping performance in consideration, which has led to incompatibilities with the Full-Standard SQL and its supported functionality
- The Issue with Concurrency: Concurrent read-writes can be problematic if multiple users are trying to access and change the data in the Database
## Ref
- https://www.youtube.com/watch?v=HQKwgk6XkIA
- https://www.youtube.com/watch?v=KHc2iiLEDoQ
- https://hevodata.com/learn/sqlite-vs-mysql/