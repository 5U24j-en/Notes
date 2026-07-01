
#### Streaming 

- The continuous processing and analysis of data records as they are generated

![](../attachments/Pasted%20image%2020260510114132.png)

## Kafka

![](../attachments/Pasted%20image%2020260510115924.png)

 - #### **Events / Message**
	 - A data record is called as **Event** **OR Message**, in terms of Data engineering

 - ####  Producer
	 - Producers create / generate messages.

- #### Kafka Broker
	- Holds the messages in the topics
	- Topics are similar to tables

- #### Consumer
	- Consumer subscribe to the topic and read messages from the topic
	- Consumers pull data


- Kafka Server receives data data every second and receives a real time stream of data
- Apache Kafka is a highly scalable, and distributed platform for creating and processing streams in real-time.
- Kafka Application is a message broker
- #### Before
	- ![](../attachments/Pasted%20image%2020260602150959.png)
- #### After
	- ![](../attachments/Pasted%20image%2020260602151017.png)

### Kafka Components

- Kafka Broker
- Kafka Connect
- Kafka Client
- Kafka Streams
- KSQL ( not opensource )

## Kafka Concepts

- ### Producer
	- A producer is an application that sends Data / message
	- The data is stored in Kafka consumer

- ### Consumer
	- An application that receives data
	- The consumer must request and receive message

- ![](../attachments/Pasted%20image%2020260602151621.png)

- ### Broker
	- A **Broker** is the Kafka server

- ### Cluster
	- A group of computers having instance of the Brokers

- ### Kafka Topic
	- A Kafka topic is a unique name for a Data stream
	- 
	- ![](../attachments/Pasted%20image%2020260602152031.png)

- ### Topic Partitions
	- Kafka is a distributed system
	- Kafka will break the topic into smaller parts
	- ![](../attachments/Pasted%20image%2020260602152152.png)
	- Number of partitions are designed by the Architect

### Partition Offset

- Unique sequence id of a message in the partition
- The sequence id is automatically assigned by the broker based on arrival
- These offsets are local within the partitions
- To locate a message
	- Topic name -> Partition number -> Offset number
- ![](../attachments/Pasted%20image%2020260602152343.png)

### Consumer Group
- A group / cluster of Consumers
- ![](../attachments/Pasted%20image%2020260602153439.png)

### Kafka Connect

- ![](../attachments/Pasted%20image%2020260602154532.png)
- #### Ways to collect a Data
	- **1st** Modify the source application to publish directly to Kafka
		- Add a Kafka Producer client/library to the application source code.
		- Example:
			- Java Spring Boot application publishes events directly to Kafka.
			- Python application uses `confluent-kafka` to send messages.
			- This is often called **application-level integration**.

	- **2nd** Use an external ingestion tool without modifying the application
		- The application remains unchanged.
		- Examples:
			- Debezium for CDC
			- Kafka Connect JDBC Source Connector
			- Python ETL script
			- Fluent Bit
	
- ![](../attachments/Pasted%20image%2020260602154659.png)


- In the Second way we can use the Kafka Connect
	- ![](../attachments/Pasted%20image%2020260602155004.png)
	- #### Kafka Connect Framework
		- 1) Source Connector
			- SourceConnector
			- SourceTask
		- 2) Sink Connector 
			- SinkConnector
			- SinkTask
	- Kafka Connect itself is a cluster
		- ![](../attachments/Pasted%20image%2020260602155623.png)
		- ![](../attachments/Pasted%20image%2020260602155649.png)

- Kafka Connect has Single Message Transformation - SMT
	- Add a new filed in your records using static data or metadata
	- Filter or Rename Fields
	- Mask some fields with a Null Value
	- Change the record key
	- Routing the record to a different Kafka Topic