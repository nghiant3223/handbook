# HTTP2

## HTTP Head of line blocking

With HTTP/1.0, if the client wants to make multiple parallel requests to improve performance, then multiple TCP connections must be used (see Using Multiple TCP Connections). This behavior is a direct consequence of the HTTP/1.x delivery model, which ensures that only one response can be delivered at a time (response queuing) per connection. Worse, this also results in head-of-line blocking and inefficient use of the underlying TCP connection.

In theory, the pipelining of HTTP/1.1 also offered a way around head-of-line blocking, but it was tricky and very error-prone to implement in practice. That has made it not get widely enabled on the web even up till today.

## How HTTP2 solves head-of-line blocking

The new binary framing layer in HTTP/2 removes these limitations, and enables full request and response multiplexing, by allowing the client and server to break down an HTTP message into independent frames, interleave them, and then reassemble them on the other end.

### Streams, messages, and frames

The introduction of the new binary framing mechanism changes how the data is exchanged between the client and server. To describe this process, let’s familiarize ourselves with the HTTP/2 terminology:

- Stream: A bidirectional flow of bytes within an established connection, which may carry one or more messages.
- Message: A complete sequence of frames that map to a logical request or response message.
- Frame: The smallest unit of communication in HTTP/2, each containing a frame header, which at a minimum identifies the stream to which the frame belongs.

The relation of these terms can be summarized as follows:

- All communication is performed over a single TCP connection that can carry any number of bidirectional streams.
- Each stream has a unique identifier and optional priority information that is used to carry bidirectional messages.
- Each message is a logical HTTP message, such as a request, or response, which consists of one or more frames.
- The frame is the smallest unit of communication that carries a specific type of data—e.g., HTTP headers, message payload, and so on. Frames from different streams may be interleaved and then reassembled via the embedded stream identifier in the header of each frame.

![Stream Message Frame](images/streams_messages_frames01.svg)

In short, HTTP/2 breaks down the HTTP protocol communication into an exchange of binary-encoded frames, which are then mapped to messages that belong to a particular stream, all of which are multiplexed within a single TCP connection. This is the foundation that enables all other features and performance optimizations provided by the HTTP/2 protocol.
