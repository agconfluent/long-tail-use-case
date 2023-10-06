<!--- This readme file can be used as template for github repo. Samples: 1.https://github.com/Gokuldev-PS/-RealTime_Fleet_Management/edit/main/README.md  2. https://github.com/Gokuldev-PS/Telemetry-Data-Streamprocessing/edit/main/README.md -->

<div align="center" padding=25px>
    <img src="https://assets.confluent.io/m/7cb7c770ce239add/original/20200122-PNG-confluent_logo-stacked-denim.png" width=50% height=50%>
</div>

# Use Case Title

Add one to two paragraphs about this use case. 

## Architecture Diagram


<!--- Add one paragraph about the architecture that you are going to deploy for the use case -->

<div align="center"> 
  <img src="path/filename.jpg" width =100% heigth=100%>   <!--- Replace the path with your arch image location -->
</div>



# Requirements

<!--- Add all the requirement that are needed for the demo. The below three mentioned are generally required for most of the use cases -->

In order to successfully complete this demo you need to install few tools before getting started.

- If you don't have a Confluent Cloud account, sign up for a free trial [here](https://www.confluent.io/confluent-cloud/tryfree).
- Install Confluent Cloud CLI by following the instructions [here](https://docs.confluent.io/confluent-cli/current/install.html).
- Please follow the instructions to install Terraform if it is not already installed on your system [here](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).
- Add here
- Add here  
  

## Prerequisites

<!--- Prerequisites for Confluent Cloud will remain same for most of the use cases. Just change the image location since Cloud API key is required for terraform script. -->

### Confluent Cloud 


1. Sign up for a Confluent Cloud account [here](https://www.confluent.io/get-started/).
1. After verifying your email address, access Confluent Cloud sign-in by navigating [here](https://confluent.cloud).
1. When provided with the _username_ and _password_ prompts, fill in your credentials.

   > **Note:** If you're logging in for the first time you will see a wizard that will walk you through the some tutorials. Minimize this as you will walk through these steps in this guide.

1. Create Confluent Cloud API keys by following the steps in UI. Click on the button that is present on the right top section and click on Cloud API Key.
<div align="center"> 
  <img src="path/filename.jpeg" width =100% height=100%>
</div>

 Now click Add Key to generate API keys and store it as we will be using that key in this demo.
<div align="center"> 
  <img src="path/filename.jpeg" width =100% height=100%>
</div>
    
   > **Note:** This is different than Kafka cluster API keys. 

## Any External System Prerequistes


<!--- Add one or two sentence about the prerequistes -->



## Setup

1. This demo uses Terraform to spin up resources that are needed.


<!--- If you are using terraform you might needed the user to add the keys on the variables file so please add those below.I have shared a sample below -->

2. Update the `terraform/variables.tf` file for the following variables with your Cloud API credentials as well your DB details

```
variable "confluent_cloud_api_key" {
  
  default = " Replace with your API Key created during pre-requsite"   
}

variable "confluent_cloud_api_secret" {
  default = "Replace with your API Key created during pre-requsite"   
}
variable "mongo_host" {
  default = " "    # Add your mongohost 
}

variable "mongo_username" {
  default = " "    # Add your username
}

variable "mongo_password" {
 default = " "    # Add your password
}
```
 ### Build your cloud infrastructure


<!--- This can be left as it is as it is common execution step for all the terraform script -->
1. Navigate to the repo's terraform directory.
   ```bash
   cd terraform
   ```

1. Initialize Terraform within the directory.
   ```
   terraform init
   ```

1. Apply the plan to create the infrastructure.

   ```
   terraform apply 
   ```

   > **Note:** Read the `main.tf` configuration file [to see what will be created](./terraform/main.tf).


 # Demo



## <header style="font-weight:normal">Enrich Data Streams with Stream Processing</header>

Now that you have data flowing through Confluent, you can now easily build stream processing applications using stream processing tools such as ksqlDB. You can continuously transform, enrich, join, and aggregate your data using simple SQL syntax. You can gain value from your data directly from Confluent in real time. Also, ksqlDB is a fully managed service within Confluent Cloud with a 99.9% uptime SLA. You can now focus on developing services and building streaming pipelines while letting Confluent manage your resources for you.

This Section invloves creation of KTable which provides us the real time postion of a fleet along with other fleet details for monitoring.

If you’re interested in learning more about ksqlDB and the differences between streams and tables, I recommend reading these two blogs [here](https://www.confluent.io/blog/kafka-streams-tables-part-3-event-processing-fundamentals/) and [here](https://www.confluent.io/blog/how-real-time-stream-processing-works-with-ksqldb/).

1. On the navigation menu click on **ksqlDB** and step into the cluster you created during setup.
   To write streaming queries against topics, you will need to register the topics with ksqlDB as a stream or table.

2. **VERY IMPORTANT** — at the bottom of the editor, set `auto.offset.reset` to `earliest`, or enter the statement:

   ```SQL
   SET 'auto.offset.reset' = 'earliest';
   ```

   If you use the default value of `latest`, then ksqlDB will read form the tail of the topics rather than the beginning, which means streams and tables won't have all the data you think they should.

<!--- Add all the KSQLDB queries that are reqiured. Below is one sample. Will include Flink here as well after GA. -->
3. Create a ksqlDB table from `Fleet_Location` topic.


 ```SQL
 CREATE TABLE latest_vehicle_locations_table (
 vehicle_id INT PRIMARY KEY,
 location STRUCT<latitude DOUBLE ,longitude DOUBLE>,
 ts BIGINT
 )  
  WITH (
    KAFKA_TOPIC='Fleet_Location',
    VALUE_FORMAT='JSON') ;

 ```
 
 

## Confluent Cloud Stream Governance

Confluent offers data governance tools such as Stream Quality, Stream Catalog, and Stream Lineage in a package called Stream Governance. These features ensure your data is high quality, observable and discoverable. Learn more about **Stream Governance** [here](https://www.confluent.io/product/stream-governance/) and refer to the [docs](https://docs.confluent.io/cloud/current/stream-governance/overview.html) page for detailed information.

1.  Navigate to https://confluent.cloud
2.  Use the left hand-side menu and click on **Stream Lineage**.
    Stream lineage provides a graphical UI of the end-to-end flow of your data. Both from a bird’s eye view and drill-down magnification for answering questions like:

    - Where did data come from?
    - Where is it going?
    - Where, when, and how was it transformed?

In our use case, Stream Lineage appears as follows: we utilize two datagen connectors to generate events that are sent to two topics. These events are then enriched using KSQLDB with the assistance of a KTable, where the Real-time Fleet information is calculated. This Calcuated real-time events are stored in "real_time_location" topic from where data is sinked to MongoDB with the help of sink connector.

<!--- Add Stream Lineage screenshot here for your use case -->
<div align="center"> 
  <img src="images/stream.jpeg" width=100% height=100%>
</div>

  
Confluent expands Stream Governance capabilities with **Data Portal**, allowing teams to easily find all the real-time data streams in an organization. 

<div align="center"> 
  <img src="https://images.ctfassets.net/8vofjvai1hpv/6c3enHkLHL2HUFsnnJy9QB/5f9191fb42f33fe121204dd743583870/data-portal-in-stream-governance.png" width=100% height=100%>
</div>
 
## Other Features in Confluent Cloud

<!--- Write about additional features in Confluent Cloud that are relevant for your use case. Examples below -->

- Bidirectional Cluster Linking
- Cloud Audit Logs
- Stream Sharing
- Custom Connectors
- RBAC at component level
- Encryption
- BYOK

## Congratulations

Congratulations on building [insert your use case] in Confluent Cloud! Your complete pipeline should resemble the following one.
<!--- Add screenshot -->
<div align="center"> 
  <img src="path/filename.jpeg" width=50% height=50%>
</div>


# Teardown

You want to delete any resources that were created during the demo so you don't incur additional charges.


## Infrastructure

1. Run the following command to delete all resources created by Terraform
   ```bash
   terraform apply -destroy
   
# References
<!--- Add links to relevant docs, product webpages, etc. Here are a few examples. -->
1. Connectors for Confluent Cloud [doc](https://docs.confluent.io/platform/current/connect/index.html)
2. ksqlDB [page](https://www.confluent.io/product/ksqldb/) and [use cases](https://developer.confluent.io/tutorials/#explore-top-use-cases)
3. Stream Governance [page](https://www.confluent.io/product/stream-governance/) and [doc](https://docs.confluent.io/cloud/current/stream-governance/overview.html)
4. MongoDB Atlas Sink Connector for Confluent Cloud [doc](https://docs.confluent.io/cloud/current/connectors/cc-mongo-db-sink.html)

  
