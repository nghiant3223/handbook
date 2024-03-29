# Strengthen the security of your APIs to counter the most common attacks

## Introduction
- API facilitates the exchange of data and their availability to a wide audience.
- However, while the spread of APIs has led to increasingly complex applications that improve business use and performance, it has also led to a sharp increase in cyber risk. 
- They may handle sensitive data – make them prime targets for cyber attacks.
- The security of APIs must therefore be at the core of a cybersecurity strategy.

## Why is API security important?
- Attacks targeting APIs are one of the most serious security threats facing businesses, as they provide direct access to sensitive data and functionalities.
- Web applications remain the primary target of attacks (90% according to Verizon*) and APIs now represent 90% of the attack surface of web applications.
- APIs provide a much more flexible channel to the back-end, in terms of the amount of information that can be retrieved from the servers, it means that APIs facilitate the transfer of large amounts of data much more easily than web applications can. 
- Some scenarios: firewalls, antivirus, and other technical security solutions cannot block attacks that exploit specific vulnerabilities in APIs. Similarly, vulnerability scanners used for automated security audits cannot identify most of these vulnerabilities.
- The objective of this article is to outline the best practices for securing your APIs, and to explain the common vulnerabilities that malicious attackers exploit to compromise them.

## REST API, SOAP & GraphQL
### REST APIs
- The REST (representational state transfer) architecture is the most common approach to building APIs.
- Based on a client-server relationship.
- Stateless, which means that the API does not store any state or data between requests.
- Based on the HTTP protocol and support TLS encryption.
- The TLS protocol keeps a connection and ensures that data exchanged between two systems (server and server, or server and client) is encrypted and not readable in the event of a Man In The Middle attack.
- Also use the JSON (JavaScript Object Notation) file format, which facilitates data transfer through web browsers.
### API and SOAP protocol
- Messaging standard defined by the World Wide Web Consortium (W3C) and used to create APIs written in XML.
- Support a wide range of communication protocols, such as HTTP, SMTP and TCP.
- Use the Web Services Description Language (WSDL) IDL to determine how a message is processed, the functionalities and modules included, the communication protocol(s) supported.
- A highly structured, tightly controlled and clearly defined standard: 
  - The messages can contain up to four components, including an envelope, a header, a body and a default (for error handling).
  - Use built-in protocols known as WS Security (Web Services Security). These protocols define a set of rules that allow the addition of privacy and authentication mechanisms (XML encryption and signatures, SAML tokens, etc.).
### GraphQL APIs
- A query language and execution environment developed as an alternative to the REST architecture.
- Have their own architecture based on a query schema system.
## The common vulnerabilities of APIs and how to protect them
### Lack of rate limiting, DoS and brute force attacks on APIs
#### DoS attacks
- An attack carried out with the aim of making services unavailable, damage the operation of a web service or the reputation of a company offering such services.
- By flooding an API with false requests, its resources are blocked from responding to those requests and not to others.
- For example: malicious actors use DoS attacks as a weapon to blackmail companies.
- Some solutions to prevent this: 
  - Request verification
  - Traffic monitoring
  - Rules and rate limiting
  - During an API or web application pentest, include DoS tests to assess the resistance of your services to this type of attack.
#### Brute force attacks on APIs
- An attacker uses tools to send a continuous stream of requests to an application or API – to test all possible combinations of a parameter, through a process of trial and error in the hope of guessing right.
- Objectives:
  - Brute force of an authentication form to steal an account
  - Brute force of a login to retrieve sensitive data
  - Brute force of a secret
#### Implement rate limiting mechanisms to counter DoS and brute force attacks
- Limit the number of requests that a given user is allowed to send in a defined period of time.
- For example:
  - After a user has been authenticated, you can limit each user to a certain number of API requests per hour, to prevent them from flooding the system with too many requests.
  - Before authenticating a user, you can apply limits to restrict the number of requests overall, or from a particular IP address or time slot.
  - You can apply rules to close connections completely when the limit is exceeded or to slow down the processing of requests. This process is known as “throttling”.
- There are different techniques for applying rate limiting: 
  - Token bucket
  - Leaky bucket
  - Fixed window
  - Sliding window
### Lack of user input validation and injection attacks
#### Code injections
- If attackers know the programming language used by an application or API, they can inject code through text input fields to force the web server to execute the desired instructions.
#### SQL injections (SQLi)
- An attacker injects data to manipulate SQL commands, thereby interacting with the database through unintended queries. 
- Lead to theft, deletion or manipulation of stored data.
#### Validate user input to prevent injection attacks
- Validating user input is the best defence against SQL and code injections.
- It should be assumed that data received by an application or API cannot be considered “always” safe.
- The most effective method of protecting against SQL injections is the use of prepared statements, which separate the SQL commands from the data sent by a user. 
  - The query only needs to be prepared once but can be executed several times with the same or different parameters.
  - When the query is prepared, the database will parse, compile and optimise its plan to execute the query.
  - By using prepared statements, you avoid repeating the analysis/compilation/optimisation cycle. In short, prepared queries use fewer resources and run faster.
  - If your application uses prepared statements exclusively, you can be sure that no SQL injection is possible (however, if you build other parts of the statement based on user input, you are still taking a risk).
### Lack of data encryption and Man In The Middle attacks
#### Man In The Middle attacks
- A malicious individual inserts himself into a communication or data transfer between a client and a server, a server and a server or a client and a client.
- To intercept sensitive data or to manipulate the communication in order to introduce malware.
- This type of attack is possible if and only if the communications are not encrypted.
#### Encrypting data with TLS to counter Man In The Middle attacks
- Indeed, TLS (successor to SSL) is an encryption protocol that ensures secure communications over a computer network.
- connections will have one or more of following properties:
  - Private (i.e. secure) because the data transmitted is encrypted.
  - The encryption keys are uniquely generated for each connection and are based on a shared secret negotiated at the beginning of the session.
  - Guarantees integrity because each transmitted message includes a signature verification of the integrity of the message, thus avoiding any undetected loss or alteration of the data during transmission.
- Help reduce or even eliminate the risks of Man In The Middle attacks.
- Recommend implementing the HSTS (http Strict Transport Security) header on your servers in order to force a browser to use HTTPS secure connections, without this setting, you run the risk of users accessing your domain without the HTTPS protocol, which can lead to a breach in communications.
### Security of authentication and authorisation in APIs
#### JWT Token Implementation
- JSON Web Tokens (JWT) are generated by a server when authenticating a user, before transmission to the client. They will be returned with each HTTP request, allowing the server to identify the user.
- Process: 
  - The information contained in the token is signed using a private key on the server
  - When it receives the token again, the server compares the signature sent by the client with the one it generated with its own private key and compares the results
  - If the signatures are identical, the token is valid.
- A JWT token is therefore used to authenticate users and thus assign them rights. A bad implementation can therefore be a source of vulnerabilities.
- A common vulnerability: server-side misconfiguration of tokens.
- A JWT token generally contains: 
  - A header, the algorithm used for the signature, in JSON encoded in Base64 (usually HS256).
  - A payload, the token information. This can be the user’s name, role or email.
  - A signature, the concatenation of the “Header” and “Payload” parts encrypted with the private key (held by the server).
- Some best practices:
  - Choose a reliable algorithm (HMAC SHA256) and configure the server to always check, and therefore reject the “none” variable in the header, which will allow attackers to bypass the integrity verification function to access the data.
  - Attackers can try to guess the private key held by the server, using brute force, so avoid defining a string of characters that is too obvious.
  - Pass Bearer tokens through HTTP headers because it will prevent from exposing the tokens to risks (for example put in URLs), or if we have to put it in URLs, we must ensure that: 
    - The clients making the requests include the “Cache-Control: no-store” header
    - The server also returns a “Cache-Control: no-store” header in its responses
    - The lifespan of the token is low
#### OAuth 2.0 & OpenID
- OAuth 2.0 & OpenID are essential standards for securing authentication and authorisation in APIs.
- OAuth 2.0 allows an application of any type to use a third-party API on behalf of a user.
- OpenID is a protocol for verifying a user’s identity with an authorisation server to obtain information about the user.
### Right issues and attacks on logical API processes: Mass Assignment, Lack of access control and IDOR
- It is therefore easy for an attacker to adapt REST requests to try to manipulate resources so we need to ensure that each access correctly checks the user’s rights and reacts accordingly.
#### Mass Assignment
- In some cases, the application accepts that the user can modify a resource, but only a restricted set of attributes, for example: cannot change user's role
#### Lack of access control and IDOR
- Insecure direct object references (IDORs) are common vulnerabilities that allow attackers to bypass permissions and access resources directly by changing the value of a parameter used to point directly to an object.
- For example, use id to retrieve a resource that is not belong to him
- It is therefore necessary to ensure for each request that the user has the correct rights to operate it.
- Security by UUID can be a plus, but it is still recommended to make sure that the user has the appropriate rights because UUID can be leaked.
#### Check user rights to prevent privilege escalation
- The web server should always check the user’s rights to use a feature/
### Vulnerabilities of GraphQL APIs
- Because their architecture is different, they face specific risks: introspection, rights issues
### Perform an API penetration test to assess risks and strengthen security
- A penetration test consists of testing any type of system with a dual objective: to identify vulnerabilities that an attacker could exploit and to recommend security fixes.
- During an API penetration test, it is particularly important to look for vulnerabilities both on the server side and on the application layer.
- Examples of server-side vulnerabilities:
  - Open and insecure services
  - Unupdated or outdated software
  - Bypassable security elements
  - Configuration errors
- Examples of API vulnerabilities (OWASP Top 10): 
  - Broken Object Level Authorization
  - Broken User Authentication
  - Excessive Data Exposure
  - Lack of Resources & Rate Limiting
  - Broken Function Level Authorization
  - Mass Assignment
  - Security Misconfiguration
  - Injection
  - Improper Assets Management
  - Insufficient Logging & Monitoring
## Ref
- https://www.vaadata.com/blog/how-to-strengthen-the-security-of-your-apis-to-counter-the-most-common-attacks/
- https://www.stackhawk.com/blog/golang-command-injection-examples-and-prevention/
- https://go.dev/doc/database/prepared-statements