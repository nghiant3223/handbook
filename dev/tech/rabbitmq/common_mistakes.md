# RabbitMQ Common Mistakes

## 1. Don’t open and close connections or channels repeatedly

Have long-lived connections if possible, and use channels for each task. The handshake process for an AMQP connection is quite complex and requires at least 7 TCP packets (more if TLS is used). Channels can be opened and closed more frequently if needed. Even channels should be long-lived if possible, e.g., reuse the same channel per thread for publishing. Don’t open a channel each time you are publishing. If you can't have long-lived connections, then make sure to gracefully close the connection. Best practice is to reuse connections and multiplex a connection between threads with channels.

AMQP connections: 7 TCP packets
AMQP channel: 2 TCP packets
AMQP publish: 1 TCP packets (more for larger messages)
AMQP close channel: 2 TCP packets
AMQP close connection: 2 TCP packets
Total 14-19 packets (+ Acks)

## 2. Don’t use too many connections or channels

Try to keep the connection/channel count low. Use separate connections to publish and consume. Ideally, you should have one connection per process, and then use one channel per thread in your application.

Reuse connections
1 connection for publishing
1 connection for consuming

## 3. Don’t share channels between threads

Don’t share channels between threads, as most clients don’t make channels thread-safe for performance reasons.

## 4. Don't have queues that are too large or too long

Short queues are fastest; when a queue is empty, and it has consumers ready to receive messages, as soon as a message is received by the queue it goes straight out to the consumer.

Having many messages in a queue places a heavy load on RAM usage. When this happens, RabbitMQ will start flushing (page out) messages to disk to free up RAM, and when that happens queueing speeds will deteriorate.

Problems with long queues

Small messages embedded in the queue index
Take a long time to sync between nodes
Time-consuming to start a server with lots of messages
RabbitMQ management interface collects and stores stats for all queues

## 5. Don't use old RabbitMQ/Erlang versions or RabbitMQ clients/libraries

Stay up-to-date with the latest stable versions of RabbitMQ and Erlang. Make sure you are using the latest recommended version of client libraries.

## 6. Don't have an unlimited prefetch value

A typical mistake is to have an unlimited prefetch, where one client receives all messages. This can lead to the client running out of memory and crashing, and then all messages are re-delivered. More information about prefetch can be found here.

## 7. Don't ignore lazy queues

Use lazy queues to achieve predictable performance or if you have large queues.

With lazy queues, the messages go straight to disk, thereby minimizing the RAM usage, though throughput will be lower.

Lazy queues create a more stable cluster, with more predictable performance. Your messages will not, without a warning, get flushed to disk.

## 8. Limit queue size with TTL or max-length, if possible

Applications that get hit by spikes of messages, and where throughput is a priority, set a max-length on the queue. This keeps the queue short by discarding messages from the head of the queues so that it’s never larger than the max-length setting.

## 9. Use multiple queues and consumers

Achieve better throughput on a multi-core system with multiple queues and consumers. You will achieve optimal throughput if you have as many queues as cores on the underlying node(s).

## 10. Persistent messages and durable queues for a message to survive a server restart

If you cannot afford to lose any messages, make sure that your queue is declared as “durable” and your messages are sent with delivery mode "persistent" (delivery_mode=2).

For high throughput send transient messages to non-lazy queues.

## 11. Split your queues over different cores

Queue performance is limited to one CPU core. You will, therefore, get better performance if you split your queues into different cores, and also into different nodes if you have a RabbitMQ cluster.

## 12. Consume (push), don’t poll (pull) for messages

Make sure that your consumer consumes messages from the queue instead of using basic get actions.

## 13. Missing an HA policy while creating a new vhost on a cluster

When you create a new vhost on a cluster, don't forget to enable an HA-policy for that vhost (even if you don’t have an HA setup, you will need it for plan changes). Messages will not be synced between nodes without an HA-policy.
