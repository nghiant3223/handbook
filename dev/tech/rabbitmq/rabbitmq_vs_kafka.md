# RabbitMQ vs Kafka

## Usecases

- RabbitMQ:
  - Used for for long-running tasks, when I need to run reliable background jobs. And for communication and integration within, and between applications, i.e as middleman between microservices
  - Advanced and flexible routing rules
  - Message timing control is supported (controlling either message expiry or message delay)
  - Advanced fault handling capabilities, in cases when consumers are more likely to fail to process messages (either temporarily or permanently). RabbitMQ uses NACK and Dead Letter Queue for handling such situation
  - Suitable for long-running task processing
  - Priority queue supported
- Kafka:
  - Used as streaming platform (messaging + distributed storage + processing of data)
  - Message retention for extended periods, including the possibility of replaying past messages
  - Horizontal scaling (why RabbitMQ is only good for vertical scaling)
  - Failure retries are handled mannually
  - Using *pull* model. With Pull based system the consumer can consume based on their capability where push systems will push the messages irrespective of the state of consumer thereby putting consumer at high risk

## References

- [When to use RabbitMQ over Kafka?](https://stackoverflow.com/questions/42151544/when-to-use-rabbitmq-over-kafka/42154452)
- [RabbitMQ vs Kafka Part 1 - Two Different Takes on Messaging](https://jack-vanlightly.com/blog/2017/12/4/rabbitmq-vs-kafka-part-1-messaging-topologies)
- [RabbitMQ vs Kafka Part 2 - RabbitMQ Messaging Patterns](https://jack-vanlightly.com/blog/2017/12/5/rabbitmq-vs-kafka-part-2-rabbitmq-messaging-patterns-and-topologies)