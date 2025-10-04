# About Erlang

==Erlang== is a general-purpose, concurrent, functional programming language and a runtime system designed for building scalable, fault-tolerant, and highly available systems. It was originally developed by ==Ericsson== in the late 1980s to support large-scale, real-time, distributed applications in telecommunications.

- **Telecommunications systems** (original use case)
- **Messaging and chat applications** (e.g., WhatsApp used Erlang to handle millions of concurrent users)
- **Distributed databases** (e.g., Riak, CouchDB)
- **Web servers and APIs** (especially with Elixir/Phoenix)
- **Financial systems** requiring high reliability
- **IoT and real-time systems**

---
# About RabbitMQ

Developed by ==Rabbit Technologies==.
An open-source message broker that facilitates communication between different systems or components through a process called message queueing.
RabbitMQ lets one part of your application say, “Here’s a task,” and another part pick it up and say, “I’ll do it,” ==without directly talking to each other==.

Message queueing enables systems to send and receive messages asynchronously, making it easier to handle a large volume of data to build scalable applications and ensure fault tolerance.

*It acts as middleware that sits b/w application components, allowing them to exchange data in the form of messages. These messages are placed in queues, and consumers can retrieve them at a later time. This helps decouple different parts of an application, improving flexibility and reliability.*

- Decouples components
- Provides asynchronous messaging: One component can send the message and move on without waiting for an immediate response.
- Enhances scalability
- Ensures fault tolerance
- Supports Multiple Protocols

## Challenges before RabbitMQ

1. Tightly coupled systems (monolithic applications)
2. Asynchronous communication
3. Reliability issues

## Similar Technologies used before RMQ

1. JMS
2. ActiveMQ
3. ZeroMQ
4. IBM MQ

| Feature                | Description                                                           |
| :--------------------- | :-------------------------------------------------------------------- |
| **Asynchronous**       | Services don’t need to wait for each other.                           |
| **Reliable delivery**  | Acknowledgments, durable queues, and retries built-in.                |
| **Flexible routing**   | Exchanges (direct, fanout, topic) support smart message distribution. |
| **Decoupling**         | Producers and consumers don’t need to know about each other.          |
| **Scalability**        | Can scale horizontally with clustering and federation.                |
| **Supports plugins**   | Like monitoring, HTTP API, management UI.                             |
| **Multiple protocols** | AMQP (default), MQTT, STOMP, HTTP (via plugins).                      |

## Common use-cases

- 🛒 Order processing in e-commerce systems
- 📧 Email or notification queues
- 🧮 Task queues (e.g., image processing, PDF generation)
- 📊 Log and metrics pipelines
- 🧵 Microservices communication
- 🔄 Retry mechanisms for failed operations

---

## What problems does it solve?

1. Message queuing
2. Message routing
3. Guaranteed Message Delivery
4. Load balancing
5. Real-time communication.

---

# Message Broker

A software component that enables communication between different applications, services, or components in a system by receiving, storing, and forwarding messages.

It acts as an intermediary, ensuring that messages are passed from one part of the system to another, often in an asynchronous manner.

## Key functions of a Message Broker

1. Message queuing
2. Message routing
3. Message transformation
4. Handling message delivery

## Why does it exist?

By using a message broker, systems or services don't need to directly know about each other; they only interact with the broker, making the system more flexible and easier to scale or modify (decoupling).

---

# RMQ Concepts

#### 1. Producer (Message Sender)
- A producer is a program or service that ==sends messages== to RabbitMQ.
- It never sends directly to a ==queue==, but to an ==exchange==.
- It attaches a ==routing key== to the message.
> [!Analogy] 
> A customer (producer) mails a letter to a post office (exchange), labeled with a destination (routing key).

#### 2. Exchange (Router)

An ==exchange== is where a producer (sender) publishes a message. It receives messages from producers and routes ==them to queues== based on rules based on:
- The ==type of exchange== (routing logic)
- The ==routing key==
- The ==bindings== (rules that link queues to exchanges)

**Types of Exchanges:**

| Exchange Type | Description                                                          | Example                                       |
| :------------ | :------------------------------------------------------------------- | :-------------------------------------------- |
| **Direct**    | Routes to the queue with ==exact match== on routing key.             | key = `order.created` routes to `order_queue` |
| **Fanout**    | ==Broadcasts== message to ==all bound queues==, ignores routing key. | Logging, pub/sub                              |
| **Topic**     | Routes messages using ==wildcards== in the routing key.              | key = `user.signup.us` matches `user.*.*`     |
| **Headers**   | Routes based on ==message header values==, not routing key.          | More complex routing scenarios                |

> [!Analogy]
> The exchange is a sorting office that decides where to send your mail based on zip code (routing key) or tags (headers).

#### 3. Queue (Message Holding Area)

- A ==queue== stores messages until they are delivered to a ==consumer==.
- You can think of it like a ==buffer== or ==waiting line==.
- Each message is ==only delivered once per queue== unless requeued.

Queues can be:

- **Durable** – survives broker restarts
- **Exclusive** – used by only one connection, deleted after disconnect
- **Auto-delete** – deleted when last consumer unsubscribes

#### 4. Binding (Routing Rule)

- A ==binding== connects an exchange to a queue and defines how messages are ==routed==.
- It tells the exchange:
 "Send messages to this queue if they ==match this criteria== (e.g., routing key or pattern)."

> [!Example] 
> A binding might say
>"Messages with routing key `order.created` go to `order_queue`."

#### 5. Routing Key

- A ==tag== on the message that helps the exchange determine where to send it.
- It's like a ==zip code== or topic name.

- [n]  Direct exchange: exact match

Topic exchange: pattern match using `*` and `#`
- `*` matches a single word
- `#` matches zero or more words
    E.g., `user.#` matches `user.login`, `user.signup.us`, etc.

#### 6. 👨‍🏭 Consumer (Message Receiver)

- A ==consumer== is a program or service that connects to a queue and pulls or ==receives messages==.
- Once it processes a message, it ==sends back an acknowledgment (ACK)== to RabbitMQ.

> If it fails or crashes, the message can be ==requeued== or ==dead-lettered== (sent to a DLX).

#### 7. Messages

It consists of two parts:
1. Body
2. Header

#### 8. Acknowledgment (Ack)

- RabbitMQ waits for the consumer to say “==I’m done==” before removing the message from the queue.
- This guarantees ==at least one delivery==.
- If the consumer fails before acknowledging, RabbitMQ can ==re-deliver== the message.
 
#### 9. Durability & Persistence

To prevent message loss:

- Set `durable=true` on the ==queue== → queue survives broker restart.
- Set `delivery_mode=2` on the ==message== → message is persisted to disk.
- The exchange also must be durable.

> 💡 All 3 must be durable to ensure full persistence!

#### 9. Dead Letter Exchange (DLX)

- Messages that are:
    - Rejected (`nack`) without requeue,
    - Expired (TTL),
    - Failed to deliver after retries
- …can be routed to a ==DLX== (Dead Letter Exchange).

Used for:
- Debugging failures
- Retry mechanisms
- Alerting on persistent issues

#### 10. TTL (Time-To-Live)

You can set TTL on:
- Messages: expire after X ms if not consumed
- Queues: delete queue if unused after X ms

#### 11. Prefetch Count (Fair Dispatch)

- Limits the number of unacknowledged messages a consumer can have.
- Helps with ==fair load distribution== and avoids overloading slow consumers.

```bash
channel.basic_qos(prefetch_count=1)
```


#### 12. Virtual Host:

A logical separation within the RabbitMQ broker.
#### 13. Clustering:

Allows you to distribute the workloads among the servers. 

> [!How Everything Connects: Full Example]
> 1. **Producer** sends a message:
>Exchange: `orders_exchange` (direct)
>Routing key: `order.created`
>Message: `{ orderId: 101 }`
>2. Exchange uses **binding**:
 >`order.created → order_queue`
 >3. The message is routed to `order_queue`.
 >4. **Consumer** reads from `order_queue`.
 >5. After processing, the consumer sends an `ACK`.

### 🧠 Summary

| Concept     | Role in the Flow             |
| :---------- | :--------------------------- |
| Producer    | Sends messages               |
| Exchange    | Routes messages              |
| Queue       | Stores messages              |
| Consumer    | Processes messages           |
| Binding     | Connects exchange to queue   |
| Routing Key | Determines message route     |
| Ack         | Confirms successful handling |
| DLX         | Handles failed messages      |

---

# AMQP and Other Protocols

*Advanced Message Queuing Protocol* is an ==open standard for messaging== and is the primary protocol used by RabbitMQ.

It ==defines the structure== of messages and the ==interaction== between the brokers, producers, and consumers in a messaging system.

- [f] pub-sub model

## Key features:

1. **Reliable delivery**: message delivery with features like ack, durability, and persistent messages.
2. **Queuing**: messages are temporarily stored in ques until they're consumed.
3. **Routing**: sophisticated routing mechanisms (routing keys and bindings to enssure messgaes are delivered to the right queues.
4. **Security**: authentication, authorisation, and encryption.
5. **Flow control**: It allows back-pressure mechanisms to control message flow between producers and consumers, ensuring systems don't get overwhelmed.

It provides ==Interoperability==. Interoperability refers to the ability of different systems, devices, or applications to work together seamlessly and efficiently. This concept is essential across various fields, particularly in technology and healthcare, as it enables efficient data exchange and enhances user experience. Interoperability ensures that systems can communicate and utilize shared data effectively, promoting collaboration and improving outcomes.

## Why it is important?

1. Interoperability.
2. Scalability.
3. Flexibility, Durability, Reliability.
4. Standardied Messaging.

## Other Protocols it supports

1. MQTT
	- A ==lightweight==, publish-subscribe messaging protocol.
	- Designed for ==low-bandwidth, high-latency networks==.
	- Ideal for ==IoT (Internet of Things)== and mobile devices.
2. STOMP: Simple Text Oriented Messaging Protocol
	 - A ==text-based==, simple protocol for working with ==message-oriented middleware== (like RabbitMQ, ActiveMQ).
	 - Designed to provide an ==interoperable wire format== so any client can communicate with any message broker
3. HTTP: Hyper Text Transfer Protocol![[Screenshot 2025-08-24 at 02.55.56.png]]

## Why is AMQP better?

- Advanced Routing.
- Reliability.
- Extensibility.

---

# Understanding the AMQP Message

>[!Note]
>An AMQP message is more than data.
>It consists of the payload, metadata, and together they allow RabbitMQ to route, store, prioritize, and deliver messages reliably across the distributed system.

An AMQP message is the fundamental unit of communication in an AMQP-based messaging system like RabbitMQ.
- It is a packet of information sent from a producer to a consumer through message brokers.
- Used for communication between distributed applications, decoupling producers or consumers.

It has 2 main parts:
1. *Message Properties*: Metadata
2. *Message Body*: Actual message

## Structure

### Message Properties

Provides metadata about the message. These properties give the message context and allow both the producer and consumer to manage and process it more efficiently.
#### Message Properties:

1. **Delivery Mode**: Transient/persistent.
2. **Message ID:** Unique identifier for the message.
3. **Timestamp:** Helps track when the message was sent or to implement time-based logic.
4. **Priority**
5. **Content-type:** Helps consumers understand how to decode and process the message body.
6. **Content-Encoding:** How the message body was encoded (base65/ gunzip)
7. **Correlation-ID:** Unique ID used to correlate requests and responses in req-res messaging patterns.
8. **Reply-to:** It is the queue to which the reply message should be sent.
9. **Expiration**
10. **User-defined Headers:** Application-specific metadata.

#### Message Bodies:

This is the actual data or payload being sent through RabbitMQ. The body can contain:
1. **Text**
2. **Binary data**
3. **Serialized Objects**

## How is the AMQP Message used in RabbitMQ?

![[Pasted image 20250824112337.png]]

The combination of these components allows RabbitMQ to provide powerful features such as:

-  **Message Routing**:  The routing keys, binding rules, and exchange types determine how the message is routed. But the message properties, such as Correlation-ID or priority, can influence this routing and message processing.	
- **Message Filtering**: Properties like Headers or the Routing Key allow consumers to filter messages they are interested in.
-  **Message Acknowledgement**: The message is only removed from the queue when this acknowledgement is received.
- **Dead Lettering**: If a message cannot be processed, Eg, it expires or the consumer fails, it can be sent to a Dead Letter queue where it can be logged, or inspected, or retried.

## How do message properties impact message delivery?

The message properties play a crucial role in controlling how RabbitMQ handles the message.
1. Persistent: By setting the delivery mode to ensure that the message persists even if the RabbitMQ crashes.
2. Priority
3. TTL
4. Correlation ID

## AMQP Message and Consumer

When a message is consumed by the consumer, it often does the following:
1. **Process the message**: Consumer reads the body of the message and processes it. 
2. **Acknowledge the message**
3. **Error handling**: If there is an issue with processing the message, the consumer can send a negative ack(NAK), or it may not ack the message, which may cause RabbitMQ to re-queue the message for retry or dead letter it.

---

# Queues

![[Pasted image 20250824144320.png]]

Queues act as a buffer, where messages wait until they are consumed, ensuring reliable delivery and decouple producers and consumers.

- [f] FIFO

## Why are Queues important?

RabbitMQ is designed for asynchronous messages, where messages are processed in the background, allowing decoupling between the producers and consumers.
Queues play a central role in temporarily storing messages until consumers are ready to process them.

- Asynchronous
- Decoupling
- Reliability and Durability
- Load Balancing and Scalability
- Guaranteed delivery

## Types

1. Simple
2. Circular
3. Priority
4. Double-Ended (Deque): Elements can be added or removed from both ends.

$Eg$: Job Scheduling, I/O Buffers
![[Screenshot 2025-08-24 at 15.43.27.png]]

In RabbitMQ, every queue is typically processed by an Erlang process.![[Screenshot 2025-08-24 at 16.08.05.png]]

---

# Producers and Consumers

## Characteristics of a Producer

1. Sends a message.
2. Does not process the message.
3. Does not know about the consumer.

## Characteristics of Consumers

1. Receives and processes a message.
2. Acknowledges message receipt.
3. Does not know about the producer.

---

# RabbitMQ Architecture

RabbitMQ Architecture is based on a broker-based model

##### 1. Exchange
Responsible for accepting messages from producers and routing them to the queue. It decides how to route the messages based on their type and rules defined in its binding.
- *Direct Exchange:* Routes messages with a specific routing key to queues bound to that routing key.
- *Fanout Exchange:* Broadcasts messages to all queues bound to it, ignoring routing keys.
- *Topic Exchange:* Routes messages to queues based on the wildcard matching of the routing key.
- *Headers Exchange:* Routes messages based on header attributes instead of routing keys.

##### 2. Queues
It is a storage buffer that holds messages until they are processed by the consumers. 
Messages in the queues are processed by ==FIFO==.
- *Queue Durability:* Can survive broker restart.
- *Queue Persistent* 
- *Exclusive Queues:* Only used by the connection that created them. Once the connection closes, the queue is deleted.

##### 3. Binding
- It is a rule that connects the Exchange to a queue. It defines how messages from the exchange are routed to the queue.
- A Binding typically includes a routing key, which the exchange uses to decide which queue a message should be sent to.

>[!Example]
>A queue might be bound to a direct exchange with the routing key ordered or created, meaning it will only receive messages with that routing key.

##### 4. Producers and Consumers
- Producers are application or services that sends messages to RabbitMQ.
- Producers send messages to the Exchange, which then routes them to the appropriate queue.

- Consumers are application or services that receives and process messages from queues.
- RabbitMQ supports multiple consumers for each queue, enabling parallel processing.

##### 5. Channels
- A channel is a virtual connection inside a single TCP connection. It is a lightweight mechanism for communication with the broker.
- Channels allow Producers and Consumers to send and receive messages without the overhead of establishing a separate TCP connection for each message.

##### 6. Virtual Hosts
- Used to segregate different messaging environments in a single RabbitMQ instance.

##### 7. Connections
- Connections in RabbitMQ are established over TCP/IP to the broker.
- Connections are relatively heavyweight, and the best practice is to minimize the number of open connections.

- A Connection is a TCP connection between the client (producer or consumer) and RabbitMQ.
- Multiple channels can be opened with a single connection.

---

##### Dead Letter Exchange 
It is used to handle messages that cannot be processed. When a message is dead-lettered it is sent to a different exchange, which is configured to handle such messages.