---
title: "Introduction to Apache Flink"
---


+ <a href="#continuous-processing-for-unbounded-datasets">Continuous Processing for Unbounded Datasets</a>
+ <a href="#features-why-apache-flink">Features: Why Apache Flink®?</a>
+ <a href="#the-how-flink-use-cases">The “How”: Flink Use Cases</a>
+ <a href="#flink-the-streaming-model-and-bounded-datasets">Flink, the streaming model, and bounded datasets</a>
+ <a href="#the-what-flink-from-the-bottom-up">The “What”: Flink from the bottom-up</a>
+ <a href="#the-big-picture-where-does-flink-fit-in-with-other-frameworks">The Big Picture: Where does Flink fit in with other frameworks?</a>
+ <a href="#key-takeaways-and-next-steps">Key Takeaways and Next Steps</a>



## Continuous Processing for Unbounded Datasets
Before we go into specifics of Apache Flink®, let’s review at a higher level the types of datasets you’re likely to encounter when processing data as well as types of execution models you can choose for processing. These two ideas are often conflated, and it’s useful to clearly separate them.

**First, 2 types of datasets**

+ Unbounded: Data that’s produced continuously without a defined end point
+ Bounded: Data that’s finite and should be considered complete

Many real-world datasets are, in fact, unbounded datasets.  This is true whether the data is stored in a sequence of directories on HDFS or in a log-based system like Apache Kafka.  Examples include but are not limited to:

+ End users interacting with mobile or web applications
+ Physical sensors providing measurements
+ Financial markets
+ Machine log data

**Second, 2 types of execution models**

+ Streaming: Processing that executes continuously as long as data is being produced
+ Batch: Processing that is executed and runs to completeness in a finite amount of time, releasing computing resources when finished

It’s possible, though not necessarily optimal, to process either type of dataset with either type of execution mode. For instance, batch execution has long been applied to unbounded datasets despite issues with state management and out-of-order data.

Flink relies on a streaming execution model, which is an intuitive fit for processing unbounded datasets: streaming execution is continuous processing on data that is continuously produced. And alignment between the type of dataset and the type of execution model brings with it many advantages regarding accuracy and performance.


## Features: Why Apache Flink®?

Flink is an open source framework for distributed stream processing that:

+ Provides results that are **accurate**, even in the case of out-of-order or late-arriving data
+ Is **stateful and fault-tolerant** and can seamlessly recover from failures while maintaining exactly-once application state
+ Performs at **production scale**, running on thousands and thousands of nodes without throughput or latency tradeoffs

Earlier we discussed aligning the type of dataset (bounded vs. unbounded) with the type of execution model (batch vs. streaming). Many of the features included below--state management, handling of out of order data, flexible windowing--are crucial for computing accurate results on unbounded datasets and are made possible by Flink:

+ Flink guarantees **exactly-once semantics for stateful computations**. By ‘stateful’, we mean that your applications can maintain some aggregation or summary of data that has been processed over time. Flink's checkpointing mechanism ensures exactly-once semantics for an application’s state in the event of a failure.

<img class="illu" src="{{ site.baseurl }}/img/exactly_once_state.png" alt="Exactly Once State" width="389px" height="193px"/>

+ Flink ensures **accurately-ordered data** by supporting stream processing and windowing with Event Time semantics. ‘Event time’ refers to the time that a given event actually occurred, not the time it arrives at your stream processor and makes it easy to compute over streams where events arrive out of order and where events may arrive with a delay.

<img class="illu" src="{{ site.baseurl }}/img/out_of_order_stream.png" alt="Out Of Order Stream" width="520px" height="130px"/>

+ Flink supports **flexible windowing** based on time, count, or sessions in addition to data-driven windows. Windows can be customized with flexible triggering conditions to support sophisticated streaming patterns. Said another way, Flink’s windowing makes it possible to model the reality of the environment in which data is created.

<img class="illu" src="{{ site.baseurl }}/img/windows.png" alt="Windows" width="520px" height="134px"/>

+ Flink’s **fault-tolerance is lightweight** and allows the system to maintain high throughput rates and provide exactly-once consistency guarantees at the same time. Flink recovers from failures with zero data loss while the tradeoff between reliability and latency is negligible.

<img class="illu" src="{{ site.baseurl }}/img/distributed_snapshots.png" alt="Snapshots" width="260px" height="306px"/>

+ Flink is capable of **high-throughput and low latency** (said another way: processing lots of data quickly). The charts below show the performance of Apache Flink and Apache Storm completing a distributed item counting task that requires streaming data shuffles.

<img class="illu" src="{{ site.baseurl }}/img/streaming_performance.png" alt="Performance" width="650px" height="232px"/>

+ Flink is designed to run on **large-scale clusters** with many thousands of nodes, and in addition to a standalone cluster mode, Flink provides support for YARN and Mesos.

<img class="illu" src="{{ site.baseurl }}/img/parallel_dataflows.png" alt="Parallel" width="695px" height="459px"/>


## The “How”: Flink Use Cases

To demonstrate how a stream processor like Flink is applied to unbounded datasets, here’s a selection of real-word Flink users and problems they’re solving with Flink.

+ **Optimization of e-commerce search results in real-time:** Alibaba’s search infrastructure team uses Flink to update product detail and inventory information in real-time, improving relevance for users.

	+ **Stream processing-as-a-service for data science teams:** King (the creators of Candy Crush Saga) makes real-time analytics available to its data scientists via a Flink-powered internal platform, dramatically shortening the time to insights from game data.

	+ **Network / sensor monitoring and error detection:** Bouygues Telecom, one of the largest telecom providers in France, uses Flink to monitor its wired and wireless networks, enabling a rapid response to outages throughout the country.

	+ **ETL for business intelligence infrastructure:** Zalando uses Flink to transform data for easier loading into its data warehouse, converting complex payloads into relatively simple ones and ensuring that analytics end users have faster access to data.


We can tease out common threads from these use cases. Among other things, Flink is well-suited for:

1. **Data that comes from a variety of (occasionally unreliable) sources:** When data is being generated by millions of different users or devices, it’s safe to assume that some events will arrive out of the order they actually occurred--and in the case of more severe upstream failures, some events might come hours later than they’re supposed to. This data still needs to be managed so that results are accurate.

2. **Applications with state:** When applications become more complex than simple filtering or enhancing of single data records, managing the state that these applications keep (e.g., counters, windows of past data, state machines, embedded databases) becomes hard. Flink provides the tools to keep state efficient, fault tolerant, and manageable from the outside so you don’t have to build these capabilities yourself.

3. **Data that is processed quickly:** There is a focus in these use cases on real-time or near-real-time scenarios, where insights from data should be available at almost the same moment that the data is generated. Flink is fully capable of meeting these latency requirements when necessary.

4. **Data in huge volume:** These programs would need to be distributed across many nodes (in some cases, thousands) to support the required scale. When necessary, Flink can run on large clusters just as seamlessly as it runs on small ones.

If you’d like to see more user stories, you can visit the <a href="{{ site.baseurl }}/poweredby.html">Powered by Flink</a> page or review some of the sessions from <a href="http://flink-forward.org/program/sessions/">Flink Forward 2016</a>, the annual Flink user conference.

## Flink, the streaming model, and bounded datasets

If you’ve reviewed Flink’s documentation, you probably saw that there’s both a DataStream API for working with unbounded data as well as a DataSet API for working with bounded data.

Earlier in this write-up, we introduced the streaming execution model (“processing that executes continuously, an event-at-a-time”) as an intuitive fit for unbounded datasets. So how does a bounded dataset relate to the stream processing paradigm?

In Flink’s case, the relationship is quite natural. A bounded dataset can be treated simply as a special case of an unbounded one--it just happens to have a clearly-defined end--so it’s possible to apply all of the same streaming concepts that we’ve laid out above to finite data.

This is exactly what Flink does in its DataSet API. A bounded dataset is handled inside of Flink as a “finite stream”, with only a few minor differences in how Flink manages bounded vs. unbounded datasets.

And so it’s possible to use Flink to process both bounded and unbounded data, with both APIs running on the same distributed streaming execution--a simple yet powerful architecture.


## The “What”: Flink from the bottom-up

**Deployment modes:** Flink can run in the cloud or on premise and on a standalone cluster or on a cluster managed by YARN and Mesos.

**Runtime:** Flink’s core is a distributed streaming dataflow, meaning that data is processed an event-at-a-time rather than as a series of batches--an important distinction, as this is what enables many of Flink’s resilience and performance features that are detailed above.

**APIs (available in Java and Scala):**

+ Flink’s <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/datastream_api.html" target="_blank">DataStream API</a> is for programs that implement transformations on data streams (e.g., filtering, updating state, defining windows, aggregating).
+ The <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/batch/index.html" target="_blank">DataSet API</a> is for programs that implement transformations on data sets (e.g., filtering, mapping, joining, grouping).
+ The <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/table_api.html" target="_blank">Table API</a> is a SQL-like expression language for relational stream and batch processing that can be easily embedded in Flink’s DataSet and DataStream APIs (Java and Scala).
+ <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.1/apis/table.html#sql" target="_blank">Streaming SQL</a> enables SQL queries to be executed on streaming and batch tables. The syntax is based on <a href="https://calcite.apache.org/docs/stream.html">Apache Calcite™.</a>

**Libraries:** Flink also includes special-purpose libraries for <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/libs/cep.html" target="_blank">complex event processing</a>, <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/libs/storm_compatibility.html" target="_blank">Apache Storm compatibility</a>, <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/libs/gelly/index.html" target="_blank">graph processing</a>, and <a href="https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/libs/ml/index.html" target="_blank">machine learning</a>.

## The Big Picture: Where does Flink fit in with other frameworks?

At the most basic level, a Flink program is made up of:

+ **Data source:** The incoming data that Flink processes
+ **Transformations:** The processing step, when Flink modifies incoming data
+ **Data Sink:** Where Flink sends data after processing

<img class="illu" src="{{ site.baseurl }}/img/source-transform-sink-update.png" alt="Source" width="596px" height="110px"/>

A well-developed ecosystem is required for the efficient movement of data in and out of a Flink program, and Flink supports a wide range of connectors to third-party systems for data sources and sinks.

If you’re interested in learning more, we’ve collected [information about the Flink ecosystem here]({{ site.baseurl }}/ecosystem.html).

## Key Takeaways and Next Steps

In summary, Apache Flink is a stream processing framework that eliminates the ‘performance vs. reliability’ tradeoff often associated with open source streaming engines and meets demanding SLAs in both categories. Following this introduction, we recommend you try our quickstart, download our most recent stable version, or review the documentation.

And we encourage you to join the Flink user mailing list and to share your questions with the community. We’re here to help you get the most out of Flink.
