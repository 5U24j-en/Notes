# Project Uber

### Azure Infra

```json
{
	"Resource Group": "RG-uber-datapipeline-project",
	"Region": "East US ",
	"Resources": [
		{
			"resource":"Event Hub Namspace",
			"name": "RG-uber-events",
			"Event Hubs":[
				{
					"Topic": "ubertopic",
					"Policies": [
						{
							"name": "sendPolicy",
							"description": "The policy for the Producer to send the Data to the Topic"
						}
					]
				}
			]
		},
		{
			"resource": "Azure Data Factory",
			"name": "RG-adf-uber-dev"
		},
		{
			"resource": "Storage Account",
			"name": "rgadlsuber",
			""
		}
	]
}
```

### Architecture
![](../attachments/Pasted%20image%2020260625175013.png)

## Event Hub ( Azure Managed Kafka Service)

- #### Architecture
	- Pub - Sub / Publisher - Subscriber(Consumer) Architecture

- #### Keywords
	- **Event** - A log / record that needs to be transferred from 1 end to another
	- **Publisher** - Logs that are pushed to the even hub
	- **Event Hub** - A location where ordered Events are present 
	- **Consumer** - The consumer is the final destination of the data stream

- All events in the Event Hub are ordered based on the time they reach the Event Hub
- There can be as many **consumer** you can have
- Consumer will read the data in the order it is there in Event Hub
- **All Events in Event Hub has an Expiry Time**
- **You can tag the Expiry to an Event as Well, Like the consumer has only 1 Hour to consumer / process the data**
![](../attachments/Pasted%20image%2020260624212645.png)

- Hands On 
	- **Even Hub that is created in the Resource group is the Event Hub Namespace**
		- ![](../attachments/Pasted%20image%2020260624214528.png)
	
	- Under the Event Hub we have Can create multiple **Event Hubs** and these are the actual **Kafka Topics**
		- ![](../attachments/Pasted%20image%2020260624214852.png)
	- Creating a Even Hub ( Kafka Topic)
		- **Name :** "ubertopic"
		- ![](../attachments/Pasted%20image%2020260624215000.png)
		- **Partition Count**
			- A partition count helps with **Consumer (Downstream) Parallelism**
			- Partitions should be created on top of a category
		- **Retention**
			- The Expiry date of the Events that can be stored in **Event Hub**

## Policy

-  Our Upstream ( Producer Application ) should be **Authorized to send the events to our Event Hub**
- Policy is used in **Authorizing** Applications
- The Policy should be configured at the **Event Hub Level** **NOT AT** E**vent Hub Namespace** Level.
	- Reason: An Event Hub Namespace might have multiple Event Hubs ( kafka Topics ) and the access should be given to the **Topic** and NOT the **Namespace**
-  **FLOW: ->  `RG-uber-datapipeline-project` >> `Event Hub Namspace` >> `RG-uber-events` >> `ubertopic` >> `Shared access policies`**
- ![](../attachments/Pasted%20image%2020260624220311.png)
-  Access Shared access polices and then create a Policy that only allows sending data
- **sendPolicy** is for the  **Producer** ( Like an ID card to access the Topic )
	- **sendPolicy**
	- ![](../attachments/Pasted%20image%2020260624221037.png)
- **listenPolicy** is for the Consumer ( The consumer can read the Data)
	- **listPolicy**
	- ![](../attachments/Pasted%20image%2020260624221832.png)

## Source 1 - Producer Web App

- We will create a sample Producer web App that simulates passing the Sample Uber UI data 
- The Sample Data will be pushed to the Kafka Connection
- Code Repo Cloned from - https://github.com/anshlambagit/Uber_Data_Engineer_Project
- Code Structure
	- `connection.py` - Once you run the script it will create a JSON event of the Data
	- `data.py` - Sample data generation file
- Libraries
	- `from azure.eventhub import EventHubProducerClient, EventData`
	- Azure Event HUB SDK Required to setup a connection
- **Azure Variables**
	- Connection String
		- This is in the Policy as (connection string ) and is required for the Producer to send data to event hub
		- ![](../attachments/Pasted%20image%2020260624231055.png)
		- This is like a API key
	- Event Hub Name
		- This is the Event Hub topic name - `ubertopic`
```python
  CONNECTION_STRING = os.getenv("CONNECTION_STRING")
  EVENT_HUBNAME = os.getenv("EVENT_HUBNAME")
```
  - Initializing the Producer Object
```python
producer = EventHubProducerClient.from_connection_string(
				CONNECTION_STRING,
				eventhub_name=EVENT_HUBNAME
)
```
- Sample Code template to jump Data and send data
```python
# Prepare ride records
ride_json = json.dumps(ride_data)

# Create batch of events
event_batch = producer.create_batch()

# Create event with ride data
event = EventData(ride_json)

# Add event to batch
event_batch.add(event)
 
# Send batch to Event Hub
producer.send_batch(event_batch)
producer.close()
```
- **Sample Data Passed**
```json
{
  "ride_id": "d65dd45c-33c5-494f-b672-220350292a00",
  "confirmation_number": "du9-3971-rU28",
  "passenger_id": "5a88e62f-fff7-4458-a7ad-00d20adef5e7",
  "driver_id": "72222375-0c69-4f6b-b4b0-0baf10933744",
  "vehicle_id": "7fd129e5-cee6-4a1e-a64a-c27030d34a4b",
  "pickup_location_id": "fa52ceda-c75c-4a0b-92ea-e39c0258b37e",
  "dropoff_location_id": "15b9fa7c-59bc-4184-8087-d69a3a0d804d",
  "vehicle_type_id": 4,
  "vehicle_make_id": 6,
  "payment_method_id": 3,
  "ride_status_id": 2,
  "pickup_city_id": 9,
  "dropoff_city_id": 7,
  "cancellation_reason_id": 4,
  "passenger_name": "Anthony Martinez",
  "passenger_email": "westkim@example.net",
  "passenger_phone": "001-442-576-7364",
  "driver_name": "Tara Heath",
  "driver_rating": 4.59,
  "driver_phone": "(544)633-0266",
  "driver_license": "ow-iGA-3707172",
  "vehicle_model": "Sister",
  "vehicle_color": "Blue",
  "license_plate": "CLI-8632",
  "pickup_address": "5616 Bullock Burg, Taylormouth, WI 43385",
  "pickup_latitude": 20.077633,
  "pickup_longitude": 136.856657,
  "dropoff_address": "427 Ryan Manor Apt. 289, Kimborough, VI 74103",
  "dropoff_latitude": -24.871547,
  "dropoff_longitude": 145.250841,
  "distance_miles": 41.2,
  "duration_minutes": 113,
  "booking_timestamp": "2026-05-25T04:46:36.485348",
  "pickup_timestamp": "2026-05-25T04:52:36.485348",
  "dropoff_timestamp": "2026-05-25T06:45:36.485348",
  "base_fare": 2.5,
  "distance_fare": 72.1,
  "time_fare": 39.55,
  "surge_multiplier": 2.39,
  "subtotal": 272.82,
  "tip_amount": 2.69,
  "total_fare": 275.51,
  "rating": null
}
```

## Source 2 - Static Data

- ![](../attachments/Pasted%20image%2020260624231846.png)
## Storage Account - ( ADLS Gen 2 )

- We will now create a Storage account and a Data Lake ADLS Gen 2
- Name: `rgadlsuber`
	- ![](../attachments/Pasted%20image%2020260624233017.png)
- Enable Hierarchical Namespace as it is required for ADLS Gen2 
	- ![](../attachments/Pasted%20image%2020260624233440.png)
- 


## Azure Data Factory

- Azure Data Factory is used to create the actual Pipelines
- It can be used to create script to transfer and source Data
- ADF Resource Name - `RG-adf-uber-dev` 
	- ![](../attachments/Pasted%20image%2020260624232123.png)
- #### Pipeline
	- Pipeline is the canvas where the actual Data pipeline is created
	- Pipeline Name - HTTPtoADLS
	- ![](../attachments/Pasted%20image%2020260625154034.png)
- #### Activity ( Copy Data )
	- In Azure Data-factory , an function is called an Activity
	- Example, Copy data is a activity that is used to copy data from 1 point to another
	- Copy Data Activity : `HTTP_Ingestion`
	- It has a source and sink
	- ![](../attachments/Pasted%20image%2020260625154402.png)
	- ![](../attachments/Pasted%20image%2020260625011432.png)

- #### Linked Service
	- Linked Service in ADF are the connectors to the Data source/Loading source.
	- We create a Linked Service for Data Source which is Github
	- NOTE: For External Linked Service , we need to specify the Base URL
		 - For content files in Github we use base URL - ` https://raw.githubusercontent.com`, 
	- Linked Service Name - `ls_github`
	- ![](../attachments/Pasted%20image%2020260625011509.png)
	- ![](../attachments/Pasted%20image%2020260625011821.png)
	- Second Linked Service for the Destination which is ADLS gen 2
	- Name - `ls_datalake`
	- ![](../attachments/Pasted%20image%2020260625012039.png)

- #### Datasets
	- **Dataset** is a **metadata object** that describes the **data you want to work with**. It tells ADF **where the data is located** and **what format it is in**, but it **does not contain the data itself**.
	- **Analogy**
		- **Linked Service** → _How to connect_ to a data source.
		- **Dataset** → _What data_ to use from that source.
		- **Pipeline** → _What to do_ with that data.
		- **Activity** → The actual operation (Copy, Lookup, Data Flow, etc.).
	- ![](../attachments/Pasted%20image%2020260625012222.png)
	- Setting the Source Meta-data , this has the actual Path of the File
	- ![](../attachments/Pasted%20image%2020260625012352.png)

- #### Parameter Schema - Updating the file source and destination Dynamically

	- We should not Hard code the Dataset as the for each file we need to create that many Datasets
	- Instead we can use Parameters to Dynamically update the Endpoints , hence getting different files in the same source
	- **First we create a parameter `p_file`, set the type and the in connection update the Relative URL to dynamically add the parameter**
	- ![](../attachments/Pasted%20image%2020260625012852.png)
	- ![](../attachments/Pasted%20image%2020260625012914.png)
	- Now the Source connection is established

- #### Container ( ADLS gen 2)

	- The parameter `p_file` needs a source of parameters
	- So first we create a Container and then Create a JSON config file that has all the file names 
	- Under raw folder we will create a JSON file called - `files_array.json`
		- Content
			- ![](../attachments/Pasted%20image%2020260625165820.png)
	- ![](../attachments/Pasted%20image%2020260625022754.png)

- #### Activity - Lookup
	- We will create an Activity Lookup that will find the `files_array.json`
	- We need to create another Dataset for the file location, since this is a static config file , we don't need to add any dynamic feature
	- We create a New Dataset called `ds_file_array` , added the ADLS Gen 2 Linked service - `ls_datalake` and gave the fixed file path
	- We linked our Lookup Activity - `files_array` to our Dataset - `ds_files_array` which has the source file link in the service - `ls_datalake`
	- ![](../attachments/Pasted%20image%2020260625023412.png)
	- Testing the Connection
	- ![](../attachments/Pasted%20image%2020260625023933.png)
	- **OUTPUT:**
		- ![](../attachments/Pasted%20image%2020260625024017.png)

- #### Activity - ForEach
	- We need to iterate to the `files_array`  to get the file values like a FOR loop
	- An Activity for for loop is **ForEach**
	- Inside a For loop we need to add a Activity ( function )
	- That function is the actual Copy Paste Pipeline - `HTTP_Ingestion`
	- We first connect the Lookup to the ForEach Activity
	- In the ForEach Activity we configure what is the actual array to iterate or the Items
		- Anology - `for item in items`: --> the `items` is the Dictionary ( JSON ) / Iterator
	- **Configuring the items which is the output of the activity - `files_array`**
	- ![](../attachments/Pasted%20image%2020260625024858.png)
- #### Activity - Copy Data
	- `HTTP_Ingestion` - configuration
	- Each item of the JSON file needs to be set as the parameter 
	- **Updating the Source**
		- **NOTE CORRECTION BELOW - `@{item().file}.json` is required as the files end with `.json`**
		- ![](../attachments/Pasted%20image%2020260625025259.png)
	- **Updating the Sink Destination**
		- ![](../attachments/Pasted%20image%2020260625025607.png)
	- Dynamically allocating storage variables
	- Add Parameter
	- ![](../attachments/Pasted%20image%2020260625025834.png)
	- Setting the Connection in `ds_ingest` Dataset![](../attachments/Pasted%20image%2020260625025947.png)
	- Since we have Selection Output format as JSON while creating the Dataset , we don't need to add .json in the end
	- ![](../attachments/Pasted%20image%2020260625174540.png)
	- **For source we need to add .json**
	- ![](../attachments/Pasted%20image%2020260625030819.png)

- **Pipeline Succeeded**
- ![](../attachments/Pasted%20image%2020260625031900.png)
- Files now moved from Github to ADLS gen2
- ![](../attachments/Pasted%20image%2020260625174913.png)

## Event Hub to Data-bricks





![](../attachments/Pasted%20image%2020260625234015.png)

![](../attachments/Pasted%20image%2020260625234046.png)

- Token can be created at container level as well
![](../attachments/Pasted%20image%2020260625234149.png)


![](../attachments/Pasted%20image%2020260625234955.png)
- Pandas Dataframe created

```python

import pandas as pd
import json
import requests

config_url = "https://rgadlsuber.blob.core.windows.net/raw/files_array.json?sp=r&st=2026-06-25T18:19:28Z&se=2026-06-30T02:34:28Z&spr=https&sv=2026-02-06&sr=c&sig=Jf3R3HL9df5l9H%2FWsKoZSxWUS9rdHkCiUc9mGHFDQmQ%3D" 

response = requests.get(config_url)
file_array = response.json()

list_df = []

for item in file_array:

	url = f"https://rgadlsuber.blob.core.windows.net/raw/ingestion/{item['file']}.json?sp=r&st=2026-06-25T18:02:29Z&se=2026-06-30T02:17:29Z&spr=https&sv=2026-02-06&sr=c&sig=PmyQAXUSkZuvLOHil6Je8qKeqcvOX04xdqIhMp1a33M%3D"
	
	df = pd.read_json(url)
	df_spark = spark.createDataFrame(df)
	df_spark.write.format("delta").mode("overwrite").saveAsTable(f"uber.bronze_schema.{item.get("file", "error_empty.csv")}")
```

Calling from Event Hub - Documentation SDP - https://learn.microsoft.com/en-us/azure/databricks/ldp/event-hubs

Append Flow Documentation - https://learn.microsoft.com/en-us/azure/databricks/ldp/developer/ldp-python-ref-append-flow

```python
import pyspark.sql.function as F
from pyspark.sql.type import *
from pyspark import pipelines as dp

# Empty Streaming table
dp.create_streaming_table("stg_rides")

# Bulk Table
@dp.append_flow(
	target = "stg_rides"
)
def rides_bulk():
	df = spark.readStream.table("bulk_rides")
	return df

# Stream Table
@dp.append_flow(
	target = "stg_rides"
)
def rides_stream():
	df = spark.readStream.table("rides_raw")
	return df
```

- Dry Run output
	- ![](../attachments/Pasted%20image%2020260626010156.png)
- 