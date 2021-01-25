# Consumer Acknowledgements and Producer Confirms

## `ack`, `nack` and `reject`

- `ack` will acknowledge the message, which tells RabbitMQ that this message has been handled. RabbitMQ will mark the message as acknowledged, and remove it from the queue permanently. (see <https://www.rabbitmq.com/amqp-0-9-1-reference.html#`basic.ack`>)
- `nack` is a "negative acknowledge" or "not acknowledged" - this tells RabbitMQ that the message was not handled properly. By default, `nack` will put the message back in the queue for later handling. You can also force the message to not requeue with `nack`. (see <https://www.rabbitmq.com/amqp-0-9-1-reference.html#`basic.nack`>)
- `reject` is an explicit "not acknowledged" and do not requeue (by default). RabbitMQ will drop the message from the queue entirely, as the message will not be processable in that queue. you can specify a `requeue` parameter for reject, like nack. (see <https://www.rabbitmq.com/amqp-0-9-1-reference.html#basic.reject>)

The advantage of `nack` over `reject` is that `nack` works with multiple messages while `reject` works with only one message. People generally use `nack` to put a message back on the queue and handle it later while use `reject` when they have nothing to do with the message at all and want to discard the message right away.

## Requeuing of Deliveries

When a message is requeued, it will be placed to its original position in its queue, if possible. If not (due to concurrent deliveries and acknowledgements from other consumers when multiple consumers share a queue), the message will be requeued to a position closer to queue head.

Requeued messages may be immediately ready for redelivery depending on their position in the queue and the prefetch value used by the channels with active consumers. This means that if all consumers requeue because they cannot process a delivery due to a transient condition, they will create a requeue/redelivery loop. Such loops can be costly in terms of network bandwidth and CPU resources. Consumer implementations can track the number of redeliveries and reject messages for good (discard them) or schedule requeueing after a delay.

## Channel Prefetch Setting (QoS)

Because messages are sent (pushed) to clients asynchronously, there is usually more than one message "in flight" on a channel at any given moment. In addition, manual acknowledgements from clients are also inherently asynchronous in nature. So there's a sliding window of delivery tags that are unacknowledged. Developers would often prefer to cap the size of this window to avoid the unbounded buffer problem on the consumer end. This is done by setting a "prefetch count" value using the basic.qos method. The value defines the max number of unacknowledged deliveries that are permitted on a channel. Once the number reaches the configured count, RabbitMQ will stop delivering more messages on the channel unless at least one of the outstanding ones is acknowledged.

For example, given that there are delivery tags 5, 6, 7, and 8 unacknowledged on channel Ch and channel Ch's prefetch count is set to 4, RabbitMQ will not push any more deliveries on Ch unless at least one of the outstanding deliveries is acknowledged. When an acknowledgement frame arrives on that channel with delivery_tag set to 5 (or 6, 7, or 8), RabbitMQ will notice and deliver one more message. Acknowledging multiple messages at once will make more than one message available for delivery.

It's worth reiterating that the flow of deliveries and manual client acknowledgements is entirely asynchronous. Therefore if the prefetch value is changed while there already are deliveries in flight, a natural race condition arises and there can temporarily be more than prefetch count unacknowledged messages on a channel.

## Consumer Acknowledgement Modes, Prefetch and Throughput

Acknowledgement mode and QoS prefetch value have significant effect on consumer throughput. In general, increasing prefetch will improve the rate of message delivery to consumers. Automatic acknowledgement mode yields best possible rate of delivery. However, in both cases the number of delivered but not-yet-processed messages will also increase, thus increasing consumer RAM consumption.

Automatic acknowledgement mode or manual acknowledgement mode with unlimited prefetch should be used with care. Consumers that consume a lot of messages without acknowledging will lead to memory consumption growth on the node they are connected to. Finding a suitable prefetch value is a matter of trial and error and will vary from workload to workload. Values in the 100 through 300 range usually offer optimal throughput and do not run significant risk of overwhelming consumers. Higher values often run into the law of diminishing returns.

Prefetch value of 1 is the most conservative. It will significantly reduce throughput, in particular in environments where consumer connection latency is high. For many applications, a higher value would be appropriate and optimal.

## When Consumers Fail or Lose Connection: Automatic Requeueing

When manual acknowledgements are used, any delivery (message) that was not acked is automatically requeued when the channel (or connection) on which the delivery happened is closed. This includes TCP connection loss by clients, consumer application (process) failures, and channel-level protocol exceptions (covered below).

Note that it takes a period of time to [detect an unavailable client](https://www.rabbitmq.com/heartbeats.html).

Due to this behavior, consumers must be prepared to handle redeliveries and otherwise be implemented with idempotence in mind. Redeliveries will have a special boolean property, redeliver, set to true by RabbitMQ. For first time deliveries it will be set to false. Note that a consumer can receive a message that was previously delivered to another consumer.

## Client Errors: Double Acking and Unknown Tags

Should a client acknowledge the same delivery tag more than once, RabbitMQ will result a channel error such as `PRECONDITION_FAILED - unknown delivery tag 100`. The same channel exception will be thrown if an unknown delivery tag is used.

Another scenario in which the broker will complain about an "unknown delivery tag" is when an acknowledgement, whether positive or negative, is attempted on a channel different from that on which the delivery was received on. Deliveries must be acknowledged on the same channel.

## Publisher Confirms

To enable confirms, a client sends the confirm.select method. Depending on whether no-wait was set or not, the broker may respond with a confirm.select-ok. Once the confirm.select method is used on a channel, it is said to be in confirm mode. A transactional channel cannot be put into confirm mode and once a channel is in confirm mode, it cannot be made transactional.

Once a channel is in confirm mode, both the broker and the client count messages (counting starts at 1 on the first confirm.select). The broker then confirms messages as it handles them by sending a `basic.ack` on the same channel. The delivery-tag field contains the sequence number of the confirmed message. The broker may also set the multiple field in `basic.ack` to indicate that all messages up to and including the one with the sequence number have been handled.

## Negative Acknowledgments for Publishes

In exceptional cases when the broker is unable to handle messages successfully, instead of a `basic.ack`, the broker will send a `basic.nack`. In this context, fields of the `basic.nack` have the same meaning as the corresponding ones in `basic.ack` and the requeue field should be ignored. By nack'ing one or more messages, the broker indicates that it was unable to process the messages and refuses responsibility for them; at that point, the client may choose to re-publish the messages.

After a channel is put into confirm mode, all subsequently published messages will be confirmed or nack'd once. No guarantees are made as to how soon a message is confirmed. No message will be both confirmed and nack'd.

`basic.nack` will only be delivered if an internal error occurs in the Erlang process responsible for a queue.

## When Will Published Messages Be Confirmed by the Broker?

For unroutable messages, the broker will issue a confirm once the exchange verifies a message won't route to any queue (returns an empty list of queues). If the message is also published as mandatory, the basic.return is sent to the client before `basic.ack`. The same is true for negative acknowledgements (`basic.nack`).

For routable messages, the `basic.ack` is sent when a message has been accepted by all the queues. For persistent messages routed to durable queues, this means persisting to disk. For mirrored queues, this means that all mirrors have accepted the message.

## What Happens with Unroutable Messages

It is possible to avoid the complete loss of a message. RabbitMQ handles unroutable messages in two ways based on the mandatory flag setting within the message header. The server either returns the message when the flag is set to true or silently drops the message when set to false.

Applications can log returned messages, but logging does not provide a mechanism for dealing with an unreachable exchange or queue.

## References

- [Batch ACK and NACK](https://stackoverflow.com/questions/38253380/rabbitmq-batch-ack)
- [Consumer Acknowledgements and Publisher Confirms](https://www.rabbitmq.com/confirms.html)
- [Collecting Unroutable Messages in a RabbitMQ Alternate Exchange](https://www.cloudamqp.com/blog/2020-09-25-collecting-unroutable-messages-in-a-rabbitmq-alternate-exchange.html)
