# Confluent Current 2023

Tuesday September 26, 2023

## Keynote

Streams are really like a superset of batch processing.
The same abstraction that streams are to batches, stream processing is to streams.

Jay is making a point about how business happens in real time, so we need tools that work
at the same pace that the business does. Which is a fair enough call.

Joe Foster -> NASA Cloud Computing Program Manager
[Nasa GCN](https://gcn.nasa.gov/) -> Alerts for all things astronomy that anyone can subscribe to.
They won streaming company of the year for this project.

The rest of the keynote is about where the streaming world is going next.
"Ubiquity"

Shaun Clowes - CPO @ Confluent
If you can make your data reusable, trustworthy, contextualized, and discoverable, your data becomes an actual asset.
The industry is referring to those assets you create as "data products". Instead of them being dead rows in a DB
somewhere that get queried occasionally, they become a living asset that you can mix and match to solve new problems
and create new business value.

4 key aspects of a data streaming platform that actually creates value
1. Stream -> Data is moving from where it is created to where it needs to be consumed
1. Connected -> connector ecosystem. (Makes sense)
1. Governed -> Data is pure and trustworthy (We probably need to get better at this part of it)
1. Processed -> Data is exponentially more valuable if you can mix it with other related data

TODO: Read this: [Stream governance docs](https://docs.confluent.io/cloud/current/stream-governance/index.html)

Julie Price - PM @ Confluent
"Kora" -> Confluent's Kafka engine for the cloud
Confluent's goal was to turn Kafka's cloud-native experience into what S3 did for NFS.
[Whitepaper on Kora](https://www.vldb.org/pvldb/vol16/p3822-povzner.pdf)
Kora is up to 4x faster than open-source Kafka. With their revamped replication it'll be more like 10x faster than
open-source.
Confluent is announcing a 20% reduction in storage costs for all new and existing customers.
Before today there were Basic, Standard, and Dedicated clusters.
Today they're adding an Enterprise cluster between Standard and Dedicated.

Mike ??? - VP of Product @ Confluent
Adding client-side field-level encryption. So you can have end-to-end encryption controlled by producer/consumer.
Adding a new Data Portal in the UI. Maybe a replacement for the stream lineage UI?
You can encrypt individual fields and make it so specific consumers can or can't read it.
Basically they're giving out some more granular control over governance, and being able to do things at the field level.
There's an "AI" assistant coming at the beginning of 2024

Konstantin Knauf - ??? @ Confluent
The stream processor plays the same role for data in motion that your RDBMS plays for data at rest.
Flink is emerging as the de-facto standard for stream processing, according to confluent.
Potentially because it plays so nicely with Kafka, which is also becoming the de-facto standard.
Flink needs the same set of things applied to it that S3 did to NFS and that they tried to do with Kafka to make it a
first-class cloud-native project.
Managed flink is "serverless"
- Don't worry about the runtime
- Don't worry about breaking changes to APIs
- Don't have to manage your own watermarking strategies
Kora is confluent's "storage" layer and flink is their compute layer
"Compute pool" is their resource you use to run actual flink jobs
He's literally referring to kafka topics as "tables". They've fully bought in to the analogy.
`INVOKE_OPENAI` is a tool they're adding to their flink stuff. Built in generational-AI.
Managed flink has entered public preview as of now.

Confluent is giving out $1Million in funding for startups that are leveraging streaming tools, and giving
startups their first year of confluent for free. Pitches / applications open until December.

## From 🐛 to 🦋: Data Pipelines Evolution from Batch to Streaming 

Basically talking about the paradigms of batch processing / ETL tools and how think about those paradigms in a
stream-processing paradigm.
If you're using a JDBC connector to pull data from your databases directly, you're still doing batch processing
even if you think that you're not.
Outbox pattern + CDC discussed as a better input to flink jobs that replicated tables, etc.
How to advance watermarks if you have infrequently-updated tables as inputs to your join.
- Use a flink config `table.exec.source.idle-timeout`
- Create arbitrary records/load in the source table (can use debezium heartbeats for this)
- Can use the `heartbeat.interval.ms` in debezium.
- BUT if you still need to do periodic heartbeats on infrequently-updated tables to make sure watermarks keep flowing,
aren't you really still just doing batching?
His only negative for the outbox pattern is additional DB load, but I would argue that the value you get is that you're
not doing all the joining / query logic which is WAY heavier than a single heartbeat.
One good point though is that all the sets of streaming paradigm ideas have different sets of constraints / costs
and you're always on the hook for picking which set of constraints are the least worst.

## 3 Flink Mistakes We Made So You Won't Have To

1. Data loss with Flink Exactly-Once Delivery to Kafka
It's implemented with a 2-phase commit protocol / kafka transactions.
It writes the kafka transaction ID into the checkpoint state, which is great unless you can't commit the
transaction to the kafka broker.
Make sure to implement some configs on your kafka broker:
  - `transaction.max.timeout.ms`
  - `transaction.id.expiration.ms`
If you restore from a checkpoint or savepoint that is over an hour old (which is what they're recommending you
reduce your transaction id expiration to) you can potentially have a transaction ID that has expired.

2. Inefficient Memory Configuration
Memory management in the JVM is hard. Flink auto-computes memory budgets based on the size of the process that you're
running.
Main memory consumers in flink
- Framework + Task heap
- RocksDB allocates memory off-heap
- Network stack consumes some memory
- JVM internal structures
So if you give your TaskManager 8GB of memory, don't be surprised to see it allocate in ways you're suprised by.
RocksDB is the largest memory consumer. And it's a good one to optimize for because it's more room for cacheing and
means you go to disk less often.
You can manually intervene in Flink's memory model to give more memory of your total 8GB (or whatever it is).
`taskmanager.memory.XXX` you can set mins/maxs for internals of flink.
Depends on your use case. Optimize for your actual job.

3. Inefficient Checkpointing Configuration
Make sure your job isn't spending ALL it's time checkpointing.
`execution.checkpointing.min-pause` <- Can make sure that your application has a minimum pause between checkpoints.
If you checkpoint every 10 seconds, but they take 11 seconds to do, it will ALWAYS be checkpointing.
Recommended to use incremental checkpoints (which we're already doing)
`state.backend.local-recovery: true` <- Makes it so if a node fails, only the node that fails has to retrieve state
from the network in S3. But the other nodes can retreive whatever data still lives locally in the cluster.
Put your RocksDB on the fastest disk available by your cloud provider. Local SSDs recommended.

Question Time:
- Ever seen memory-leak issues with RocksDB?
  - Yes, in scenarios where they're letting the deployment env manually allocate memory to RockDB
  - Also in scenarios where they are changing the parallelism of the job and the taskmanager doesn't know how to stop
  the replica's memory consumption.
- S3 costs on incremental checkpoints can end up being higher because of the more-frequent requests. So you're paying
for requests instead of storage (which you would pay for with whole-state checkpoints)

## Learnings of Running Kafka Tiered Storage at Scale

Kafka @ Uber is doing trillions of messages/day and petabytes worth of data per day
Challenges:
1. Replacing Failed Brokers
Any broker could be a leader for one set of brokers and a follower for another set of brokers.
In their case, there was so much volume that replacing a single leader broker could take 8-9 hours.
2. Cluster Rebalancing
3. Migrating from old hardware to new hardware

Kafka Tiered Storage expands the storage beyond the local disk. Separates the storage layer from the compute layer
which allows you to optimize them separately.
They just released this a few days ago. At this point, what is the value prop in using Pulsar over Kafka.
Pulsar _does_ still have support for the queueing semantic that Kafka doesn't.

## 6 Lessons Learned While Migrating From SQS to Kafka

When migrating queueing / streaming semantics from one tool to another, consider keeping the old tool/queue alive
so that you can failover to the old one that you already know works.
Maybe we do that with our flink jobs. Let the old ones on the old Scala version of the library stay alive to catch
cases where we miss messages on the new library, etc. Make consumers idempotent and just produce all the messages twice.

## Visualizing the Stream

Aside: this is kinda fun [Streaming concepts childrens book](http://www.gentlydownthe.stream/)
Imply Polaris is a neat visualization tool for streams.
Gives you similar insight to what you get out of Apache Druid without the effort of administering Druid.
Kind of gives me tableau vibes, but specified when the input is stream data.
Depending on the cost, it could be really interesting for our use case at Bill.
It's pretty cool how quickly it visualizes data.

## Flink Power Hour (Lighting Talks on Flink)

### The Flink Table API: Streaming Inspired by SQL, but Beyond and in Java - Timo Walther

Timo is one of the core architects of Flink SQL 
Flink is not a tool, it's a toolbox.
The query planner in Flink SQL is what actually creates and determines the operator topology.
You can have data flow arbitrarily through streams and tables.
[Examples to play around with](https://github.com/twalthr/flink-api-examples)
To run in the confluent cluster (or other managed clusters) they typically just accept a JAR if they support submitting
your own custom jobs.
Seeing all these examples just using the native flink tooling makes me wonder if we make a mistake in general when we
use our own custom flink wrapper library.
Because we can interact with Flink's "SQL" programatically you can go beyond the normal limits of actual SQL.
For example, making a table with 500 columns. Would suck in regualar SQL. Not a problem for flink.

### An Overview of Flink SQL Joins - David Anderson

The primary risk with streaming joins is unbounded state retention.
Best practice is to limit the bounded set of data you keep from one table.
We could be more intentional wish using updating tables vs insert-only tables. We should use insert-only tables for all
of our business events.
You need to think about the semantics of what you want with joins. Do you want to enrich with data as they were when
events occurred, or latest?
Temporal Joins - suitable when we want to enrich an event exactly once with the appropriate information with information
as it existed at the time the event occurred.
Temporal joins can be idle if there's no data. So be intentional with your watermarking strategy.
Can do things like this:
```SQL
for SYSTEM_TIME AS OF some_table.some_timestamp
```
And consider using a temporal join instead of unbounded regular joins.

### The AdaptiveScheduler, a New Default for Streaming - David Moravek

AdaptiveScheduler will become available with Flink 1.18
This guy was part of Immerok (the managed flink startup that Confluent acquired)
(Had to run a little early to meet with Dominic from Confluent)
