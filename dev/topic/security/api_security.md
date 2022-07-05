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
- REST is based on a client-server relationship.
- REST is stateless, which means that the API does not store any state or data between requests.
- REST APIs are based on the HTTP protocol and support TLS encryption.
- The TLS protocol keeps a connection and ensures that data exchanged between two systems (server and server, or server and client) is encrypted and not readable in the event of a Man In The Middle attack.
- REST APIs also use the JSON (JavaScript Object Notation) file format, which facilitates data transfer through web browsers.
### API and SOAP protocol
- SOAP is a messaging standard defined by the World Wide Web Consortium (W3C) and used to create APIs written in XML.
- SOAP supports a wide range of communication protocols, such as HTTP, SMTP and TCP.

## Ref
- https://www.vaadata.com/blog/how-to-strengthen-the-security-of-your-apis-to-counter-the-most-common-attacks/