---
title: "What is kafka?"
seoTitle: "An introduction to kafka"
seoDescription: "Kafka in detailed !"
datePublished: Sat Nov 08 2025 07:25:01 GMT+0000 (Coordinated Universal Time)
cuid: cmhpym2oe000102l1a8y2cwi9
slug: what-is-kafka
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1762584549996/35f7daa2-4700-4501-a8b7-139b459aedeb.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1762586677971/7c54f72c-b3be-4cf3-b6d2-5c67c1d1d0b8.webp

---

# **Kafka in detailed !**

[https://kafka.apache.org/](https://kafka.apache.org/)

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fdd2015a1-5c5c-4ef3-91ad-2c6a8c92600c%2FScreenshot_2024-07-10_at_2.40.11_PM.png?table=block&id=ed871567-37d8-44cf-9634-1858ab454b54&cache=v2 align="left")

#### **What is distributed**

You can scale kafka horizontally by adding more nodes that run your kafka `brokers`

#### **Event streaming**

If you want to build a system where one process `produces` events that can be consumed by multiple `consumers`

#### **Examples of apps**

Payment notifications

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Feadf2285-35e0-4b6e-bdc6-6459d7ad2223%2FScreenshot_2024-07-10_at_2.47.40_PM.png?table=block&id=93fb3e9a-3e77-4670-a7d8-1762154c2f57&cache=v2 align="left")

# **Jargon**

#### **Cluster and broker**

A group of machines running kafka are known as a kafka cluster

Each individual machine is called a broker

#### **Producers**

As the name suggests, producers are used to `publish` data to a topic

#### **Consumers**

As the name suggests, consumers consume from a topic

#### **Topics**

A topic is a logical channel to which producers send messages and from which consumers read messages.

#### **Offsets**

Consumers keep track of their position in the topic by maintaining offsets, which represent the position of the last consumed message. Kafka can manage offsets automatically or allow consumers to manage them manually.

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fa98a741c-5ae0-41dc-b5cb-d8246ad491fb%2FScreenshot_2024-07-10_at_3.30.42_PM.png?table=block&id=ffeb8e21-320d-4b3c-b159-4a7bf83a35ed&cache=v2 align="left")

#### **Retention**

Kafka topics have configurable retention policies, determining how long data is stored before being deleted. This allows for both real-time processing and historical data replay.

#### **Partitions and Consumer groups (we will cover eventually)**

# **Start kafka locally**

Ref - [https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart#quickstart_createtopic)

#### **Using docker**

```bash
docker run -p 9092:9092 apache/kafka:3.7.1
```

#### **Get shell access to container**

```bash
docker ps
docker exec -it container_id /bin/bash
cd /opt/kafka/bin
```

#### **Create a topic**

```bash
./kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```

#### **Publish to the topic**

```bash
./kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
```

#### **Consuming from the topic**

```bash
./kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
```

##  **Kafka in a Node.js process**

Ref [https://www.npmjs.com/package/kafkajs](https://www.npmjs.com/package/kafkajs)

* Initialise project
    

```javascript
npm init -y
npx tsc --init
```

* Update package.json
    

```javascript
"rootDir": "./src",
"outDir": "./dist"
```

* Add `src/index.ts`
    

```javascript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "my-app",
  brokers: ["localhost:9092"]
})

const producer = kafka.producer();

const consumer = kafka.consumer({groupId: "my-app3"});


async function main() {
  await producer.connect();
  await producer.send({
    topic: "quickstart-events",
    messages: [{
      value: "hi there"
    }]
  })

  await consumer.connect();
  await consumer.subscribe({
    topic: "quickstart-events", fromBeginning: true
  })

  await consumer.run({
    eachMessage: async ({ topic, partition, message }) => {
      console.log({
        offset: message.offset,
        value: message?.value?.toString(),
      })
    },
  })
}


main();
```

* Update package.json
    

```javascript
"scripts": {
    "start": "tsc -b && node dist/index.js"
},
```

* Start the process
    

```javascript
npm run start
```

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F35ceceba-a3df-4d1a-b941-b01306d9d5ea%2FScreenshot_2024-07-10_at_4.17.21_PM.png?table=block&id=47e3b5d5-8e84-4c5c-b613-36350ad282da&cache=v2 align="left")

# **Breaking into prodcuer and consumer scripts**

Lets break our logic down into two saparate files

* producer.ts
    

```javascript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "my-app",
  brokers: ["localhost:9092"]
})

const producer = kafka.producer();

async function main() {
  await producer.connect();
  await producer.send({
    topic: "quickstart-events",
    messages: [{
      value: "hi there"
    }]
  });
}


main();
```

* consumer.ts
    

```javascript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "my-app",
  brokers: ["localhost:9092"]
})

const consumer = kafka.consumer({ groupId: "my-app3" });


async function main() {
  await consumer.connect();
  await consumer.subscribe({
    topic: "quickstart-events", fromBeginning: true
  })

  await consumer.run({
    eachMessage: async ({ topic, partition, message }) => {
      console.log({
        offset: message.offset,
        value: message?.value?.toString(),
      })
    },
  })
}


main();
```

* Update package.json
    

```javascript

  "scripts": {
    "start": "tsc -b && node dist/index.js",
    "produce": "tsc -b && node dist/producer.js",
    "consume": "tsc -b && node dist/consumer.js"    
  },
```

* Try starting multiple consumers, and see if each gets back a message for the messages produced
    

Notice we specified a `consumer group` (my-app3)

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fc0f866ab-9544-4ec7-bce6-c8818af57fed%2FScreenshot_2024-07-10_at_5.25.39_PM.png?table=block&id=6ddd921a-7a16-4347-9686-432df1b22f6c&cache=v2 align="left")

# **Consumer groups and partitions**

#### **Consumer group**

A consumer group is a group of consumers that coordinate to consume messages from a Kafka topic.

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F2551afde-b3b2-4499-8c0c-5ac5db8a65a5%2FScreenshot_2024-07-10_at_5.28.10_PM.png?table=block&id=f6a77bd2-5944-4d95-976c-8c1d2bb8f719&cache=v2 align="left")

**Purpose:**

* **Load Balancing:** Distribute the processing load among multiple consumers.
    

* **Fault Tolerance:** If one consumer fails, Kafka automatically redistributes the partitions that the failed consumer was handling to the remaining consumers in the group.
    

* **Parallel Processing:** Consumers in a group can process different partitions in parallel, improving throughput and scalability.
    

#### **Partitions**

Partitions are subdivisions of a Kafka topic. Each partition is an ordered, immutable sequence of messages that is appended to by producers. Partitions enable Kafka to scale horizontally and allow for parallel processing of messages.

#### **How is a partition decided?**

When a message is produced to a Kafka topic, it is assigned to a specific partition. This can be done using a round-robin method, a hash of the message key, or a custom partitioning strategy.

Usually you’ll take things like `user id` as the `message key` so all messages from the same user go to the same consumer (so a single user doesnt starve everyone lets say)

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F7fc0019b-0671-4b74-87b7-7c5e02f527dc%2FScreenshot_2024-07-10_at_5.34.47_PM.png?table=block&id=83a339f3-8a03-4162-943a-641838776f51&cache=v2 align="left")

#### **Multiple consumer groups**

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fd55d31bc-6733-4b44-b29f-c068def20edc%2FScreenshot_2024-07-10_at_5.36.09_PM.png?table=block&id=31961a0e-a0f8-4aef-b8e2-a146990ff7c6&cache=v2 align="left")

# **Three cases to discuss**

#### **Equal number of partitions and consumers**

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F75478041-1808-4a32-b5b1-6468d3c5cd6e%2FScreenshot_2024-07-10_at_5.58.22_PM.png?table=block&id=e21e3f03-27d8-424e-9e5c-92d79ff92f29&cache=v2 align="left")

#### **More partitions**

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F34cf697b-1d69-4106-897b-125a9939d7c7%2FScreenshot_2024-07-10_at_5.58.51_PM.png?table=block&id=63bb2037-c8bd-4825-a16f-7287673bf35c&cache=v2 align="left")

#### **More consumers**

![notion image](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Ff2060b34-80ea-41d5-9617-df65d189c86f%2FScreenshot_2024-07-10_at_5.59.05_PM.png?table=block&id=371bde0a-fac2-4618-beb5-371df0145ed7&cache=v2 align="left")

# **Partitioning strategy**

When producing messages, you can assign a key that uniquely identifies the event.

Kafka will hash this key and use the hash to determine the partition. This ensures that all messages with the same key (lets say for the same user) are sent to the same partition.

💡

Why would you want messages from the same user to go to the same partition? Lets say a single user has too many notifications, this way you can make sure they only choke a single partition and not all the partitions

* Create a new `producer-user.ts` file, pass in a `key` when producing the message
    

```javascript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "my-app",
  brokers: ["localhost:9092"]
})

const producer = kafka.producer();

async function main() {
  await producer.connect();
  await producer.send({
    topic: "payment-done",
    messages: [{
      value: "hi there",
      key: "user1"
    }]
  });
}

main();
```

* Add `produce:user` script
    

```javascript
"produce:user": "tsc -b && node dist/producer-user.js",
```

* Start 3 consumers and one producer. Notice all messages reach the same consumer
    

```javascript
npm run produce:user
```