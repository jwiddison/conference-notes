# Confluent Current 2023

Wednesday September 27, 2023

## Keynote

Ismael Juma & Martin ???
In a stream processing paradigm, kafka is your raw storage layer like the file system is. Flink is your integrated
processing layer in the same way that a database is for data at rest.
What makes Flink stand out from other stream-processing tools/paradigms?
- Similar early adopters as Kafka. Similar graphs/shapes of adoption. Probably best whole solution to stream processing

New things in Kafka:
KRaft
- With Kafka 3.5 ZooKeeper mode is officially deprecated
- Kafka 4.0 (next year) will completely remove support for ZooKeeper
KIP-405
- Tiered storage for Kafka / separating the compute and storage layer
- Tiered storage doesn't require changes on the client side / is invisible to producers / consumers.

What's next for Kafka?
- Simplified Kafka Protocol. Currently there's too much work that has to be done on the client side. They're going to
pull some of that work that's currently client-side onto the server. Metadata tracking, subscription management, etc.)
Look at KIP-848
- KIP-939: Support participation in two-phase commit. So you can enable consistent atomic updates of Kafka and other
transactional stores.
- KIP-974/975: Docker image optimizations. Starting kafka in 100ms
- KIP-932: Queues for Kafka. Yup.
  - This is a final nail in the coffin for Pulsar.
  - Going to call them "share groups" instead of "consumer groups". Allows >1 consumer per partition.
  - Goal is to merge it in in 2024.

Long-term goals / future ideas for Kafka
- Directories. Hierarchical structure to better organize topics. If it's the storage like a file system, allow us to
organize topic names like we can with files in a file system.
- Autoscaling?????? or Partition-free topics. HOW WOULD THIS EVEN WORK.

Tobias ??? - Some Kafka Guy @ BMW
They're doing ~3 billion events per day
They're streaming events to robots in the factory that consume the events to know how to autonomously build parts of
the cars. That's pretty cool. If your consumer group lag builds up, it stops an entire production line.

Kafka & Flink have emerged as the de-facto standards for streaming and stream processing. Which is great to hear.
80% of Flink users also use Kafka.
Table/SQL APIs define the job graph for you. That's why you don't see `defineGraph` in those, but do when you're using
the stream API. Not sure how I never noticed this.
They're plugging their managed flink solution again. Calling it "cloud native" like S3.

What's coming for flink?
- A mixed execution mode. The same job will be able to self-switch execution modes.
- Extensions of teh SQL APIs to adopt more of the SQL spec.
- They're going to deprecate more of the "batch"-specific APIs so that everything uses the streaming API, and reinforces
that batch processing is just a special case of stream processing.

If you think about it, Flink's checkpointing is an example of a distributed transaction.
Flink is adding support for KIP-939 / tying into Kafka's ability to participate in a 2PC.
The claim is that with 2PC "immediate" consistency is going to be possible.
Pretty cool that this all community-sponsored open-source work.
The set of committers to Flink is larger than the set for Kafka.
Current will be in Austin, TX next year. September 17-18.

## Deeply Declarative Data Pipelines

"How can we build data pipelines with as little one-off code as possible?"
Debezium can work as a source connector in flink diretly. Without needing to go through kafka/kafka-connect.
This talk is mostly going through the k8s operator, and some cool custom stuff they've built on top of it.
It turns all their flink jobs into a .yml file with the Flink SQL inside, and abstracts everything else away.
Flink SQL is more than just the SQL you can write against "tables" you create with the table API.
It's an entire API unto itself. You can use it to define your sources and sinks as well.
[His open source project Hoptimator](https://github.com/linkedin/Hoptimator)

[Validio](https://validio.io/) is a really interesting tool worth looking into.

## A Trifecta of Real-Time Applications: Apache Kafka, Flink, and Druid

[Apache Druid](https://druid.apache.org/) is a tool for piping your data from kafka into to do analytics queries on it.
What is different about druid?
- Sub-second queries at massive scale (trillions of rows).
- High concurrency without having to throw more hardware to scale horizontally.
- Incorporating real-time AND historical data. Has it's own "connector" ecosystem to project data into Druid directly
from Kafka without needing to do the network hop to Kafka Connect.
Druid is able to scale with increasing topic partitions automatically. They can add arbitrarily many more Ingestion
Tasks. That's their "consumer" abstraction.
Druid is kind of built like Flink where real-time data is the primary use case and they treat batch work as a special
case of streaming.
Fills a SIMILAR but not identical niche to things like Snowflake.
