# Characteristics

## Encapsulation

Hide class properties, methods to prevent client code from accessing them.

## Abstraction

Higher level of hiding, which hide the implementation. Client code only cares about the behavior or what this class can do, doesn't care about the implementation.

## Polymorphism

Poly=many, morphism=types. A interface can hold many implementaion type at run time. The behavior of method can change dynamically.

## Inheritance

A class can be derived from other class, inheriting other class properties and methods.

## Composition over Inheritance

Suppose we are given a task to build a logger with some base method like Info, Warn, Error and the log line must be persist in File, MySQL or Elasticsearch. If we want the logger to store log in file, in MySQL or in ElasticSearch then we can create FileLogger class, MySQLLogger class or ElasticSearchLogger respectively. What if we want the logger to persist log line in both file and MySQL? We can create FileMySQLLogger but it's very cumbersome because we have to create many classes to match the requirement.

Instead, we can create a interface LogPersister and create FilePersist, MySQLPersist, ElasticSearchPersist classes to implement the interface. The client code only has references to three persistent mechanisms: filePersist FilePersist, mysqlPersist MySQLPersist, elasticSearchPersist ElasticSearchPersist. Also, the behavior of what log to use can be changed in runtime.