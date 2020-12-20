## Azure Services

* Azure has 4 main **Workflow** Systems:
  * Logic Apps
  * Microsoft Power Automate
  * WebJobs
  * Azure Functions
* Logic Apps:
  * has Designer view
    * can drag and drop inputs to ouputs / add conditions and run different actions
  * has code view
    * write JSON file in a specific structure to specify inputs/outputs/actions/conditions
  * has *Connectors*
    * component that provides interface to external service
      * eg. Twitter connector lets you send and retrieve tweets

* Microsoft Power Automate
  * even more non-techincal person friendly version of logic apps
  * 4 different flows you can create with this
    * **Automated** : flow thst is started by some event trigger
    * **Button**: on button click run some action
    * **Scheduled** : on timer/ run actions or flow
    * **Business process** : A flow that models a business process such as the stock ordering process or the complaints procedure. The flow process can have: notification to required people; with their approval recorded; calendar dates for steps; and recorded time of flow steps.
  * has easy to use drag and drop designer view
* WebJobs
  * service to run background processes on Azure App Service
  * 2 types of jobs
    * **Continuous**: these actions run in a loop (eg:  check a shared folder for a new photo.)
    * **Triggered**: jobs that run on trigger (manually or scheduled)
* Azure Functions
  * run small pieces of code in the cloud, without having to worry about the infrastructure required to host that code
  *  consumption plan option, you only pay for the time when the code runs. Azure automatically scales your function in response to the demand from users.
  * has triggers:
    * **HTTPTrigger**. Use this template when you want the code to execute in response to a request sent through the HTTP protocol.
    * **TimerTrigger**. Use this template when you want the code to execute according to a schedule.
    * **BlobTrigger**. Use this template when you want the code to execute when a new blob is added to an Azure Storage account.
    * **CosmosDBTrigger**. Use this template when you want the code to execute in response to new or updated documents in a NoSQL database

## Azure Functions

* event driven
* pay per function call only
* "serverless" == dont care about vm provisioning, etc
* drawbacks:
  * exectution time: 
    * default timeout of 5 minutes, configurable to 10 minutes
  * execution frequency:
    * if very freuqunt call,s azure app service or vm hosted solution might be cheaper
  * While scaling, only one function app instance can be created every 10 seconds, for up to 200 total instances
* **Durable Functions**:
  * perform long-lasting, stateful operations in Azure. Azure provides the infrastructure for maintaining state information
  * 3 different types:
    * Client: functions are the entry point for creating an instance of a Durable Functions orchestration
    * Orchestrator: unctions describe how actions are executed, and the order in which they are run. You write the orchestration logic in code (C# or JavaScript).
    * Activity:  functions are the basic units of work in a durable function orchestration. An activity function contains the actual work performed by the tasks being orchestrated.
  * Application Patterns:
    * Function chaining: 
      * execute sequence of functions in order. output of one is input of next
    * Fan out/ Fan in:
      * runs multiple functions in parallel and then waits for all the functions to finish. The results of the parallel executions can be aggregated or used to compute a final result.
    * Async HTTP APIs: 
      * coordinate state of long running tasks with clients: http call triggers long running action, then it redirects client to status endpoint, client can poll endpoint and get result/status of long running action
    * Monitor:
      * implements a recurring process in a workflow, possibly looking for a change in state. For example, you could use this pattern to poll until specific conditions are met.
      * similar to async, but longer
    * Human interaction:
      * process needs human approval as part of workflow
* 2 types of **Service Plans**:
  * Consumption service plan
    * provides automatic scaling and bills you when your functions are running
  * Azure App Service Plan:
    * allows you to avoid timeout periods by having your function run continuously on a VM that you define. 
    * When using an App Service plan, you are responsible for managing the app resources the function runs on, so this is technically not a serverless plan
    * may be a better choice if your functions are used continuously or if your functions require more processing power or execution time than the Consumption plan can provide.
* when function app created, **Storage account must be linked**
* The type of event that starts the function is called a **trigger**. You must configure a function with exactly one trigger.
* **Bindings** are a declarative way to connect data and services to your function. Bindings know how to talk to different services, which means you don't have to write code in your function to connect to data sources and manage connections.
  * example binding:

` {  "bindings": [    {      "name": "order",      "type": "queueTrigger",      "direction": "in",      "queueName": "myqueue-items",      "connection": "MY_STORAGE_ACCT_APP_SETTING"    },    {      "name": "$return",      "type": "table",      "direction": "out",      "tableName": "outTable",      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"    }  ] }`



## Azure Communication

* 2 types of communication:

  * **messages**
    * contains raw data
    * produced by sender
    * consumed by receiver
  * **events**
    * light weight notification
    * does not contain raw data
    * senders = *publishers*
    * receivers = *subscribers*
    * may reference where the data lives
    * sender has no expectations of receiving component

  ### Azure Queue storage

  * Service that uses Azure Storage to store large numbers of messages that can be securely accessed from anywhere in the world using a simple REST-based interface.
  * Queues can contain millions of messages, limited only by the capacity of the storage account that owns it.
  * can generate audit trail of all messages that pass through the queue
  * queue can exceed 80 GB in size
  * can track progress for processing a message inside of the queue

  ### Azure Service Bus Queues

  * [Service Bus](https://azure.microsoft.com/services/service-bus/) is a message broker system intended for enterprise applications.
  * often utilize multiple communication protocols, have different data contracts, higher security requirements
  * can include both cloud and on-premises services
  * FIFO guarantee
  * can group messages into transactions
  * can receive messages without polling the queue
  * can handle messages larger than 64 KB but less than 256 KB
  * queue size will not grow larger than 80 GB
  * can publish and consume batches of messages

  ### Azure Service Bus Topics

  * like queues, but can have multiple subscribers
  * **NOT SUPPORTED IN BASIC TIER**

* Benefits of queues

  * increased reliability
  * at times of high demand, messages can simply wait until a destination component is ready to process them.

* Message delivery guarantees

  * **At-Least-Once Delivery**: 
    * each message is guaranteed to be delivered to at least one of the components that retrieve messages from the queue
  * **At-Most-Once-Delivery**: 
    * each message is not guaranteed to be delivered
    * there is a very small chance that it may not arrive
    * there is no chance that the message will be delivered twice
      *  == automatic duplicate detection
  * **First-In-First-Out (FIFO)**: 
    * messages leave the queue in the same order in which they were added

* Transactional support

  * can group messages into transactions:
    * so that either all succeed or non
      * example: bank

  ### Azure Event Grids

  * Event Grid distributes *events* from different sources, such as Azure [Blob storage accounts](https://azure.microsoft.com/services/storage/blobs/) or Azure [Media Services](https://azure.microsoft.com/services/media-services/), to different handlers, such as Azure [Functions](https://azure.microsoft.com/services/functions/) or Webhooks
  * ![Diagram of various event sources sending messages as topics to the Event Grid which in turn sends messages to subscribing event handlers.](https://docs.microsoft.com/en-us/learn/modules/choose-a-messaging-model-in-azure-to-connect-your-services/media/4-event-grid.png)
  * Event: 
    * data messages passing through Event Grid that describe what has taken place.
    * self-contained
    * can be up to 64 KB
    *  contains several pieces of information based on a schema defined by Event Grid:
      * ` [  {    "topic": string,    "subject": string,    "id": string,    "eventType": string,    "eventTime": string,    "data":{      object-unique-to-each-publisher    },    "dataVersion": string,    "metadataVersion": string  } ]`
  * Event source:
    * responsible for sending events to Event Grid
      * example: Azure Storage is the event source for blob created events
    * same as publisher
  * Event Topic
    * categorize events into groups
    * represented by a public endpoint and are where the event source sends events *to*.
    * this is where subscribers can subscribe to
    * **2 types** of topics:
      * System Topics:
        * built-in topics provided by Azure services
      * Custom Topics
        * When you create or are assigned access to a custom topic, you see that custom topic in your subscription.
  * Event Subscription:
    * define which events on a topic an event handler wants to receive.
  * Event Handler
    * "subscriber"
    * component (application or resource) that can receive events from Event Grid
    * The following can receive and handle events from Event Grid:
      * **Azure Functions:** Custom code that runs in Azure, without the need for explicit configuration of a host virtual server or container. Use an Azure function as an event handler when you want to code a custom response to the event.
      * **Webhooks:** A webhook is a web API that implements a push architecture.
      * **Azure Logic Apps:** An Azure logic app hosts a business process as a workflow.
      * **Microsoft Power Automate:** Flow also hosts workflows, but it is easier for non-technical staff to use.
  * Should you use Event Grid?
    * **Simplicity:** It is straightforward to connect sources to subscribers in Event Grid.
    * **Advanced filtering:** Subscriptions have close control over the events they receive from a topic.
    * **Fan-out:** You can subscribe to an unlimited number of endpoints to the same events and topics.
    * **Reliability:** Event Grid retries event delivery for up to 24 hours for each subscription.
    * **Pay-per-event:** Pay only for the number of events that you transmit.

  ### Azure Event Hubs

  * same as Azure Event Grid
    * except for Big data

* two Azure features that include message queues

  * Service Bus
    * Supports larger messages sizes of 256 KB (standard tier) or 1MB (premium tier) per message versus 64 KB
    * Supports both at-most-once and at-least-once delivery
  * Azure Storage accounts.

 

## Azure Storage

* Structured Data:
  * *relational data*, is data that adheres to a strict schema, so all of the data has the same fields or properties
  * this data style perfect for applications such as CRM systems, reservations, and inventory management.
  * evolution of the data is more difficult as each record has to be updated to conform to the new structure.
* Semi-Structured Data:
  * Semi-structured data contains tags that make the organization and hierarchy of the data apparent - for example, key/value pairs
  * also referred to as non-relational or *NoSQL data*. 
  * The expression and structure of the data in this style is defined by a *serialization language*.
    * JSON, XML, YAML, etc.
* Unstructured Data:
  * organization of unstructured data is ambiguous
  * Unstructured data is often delivered in files, such as photos or videos.
* **Atomicity** means a transaction must execute exactly once and must be atomic; either all of the work is done, or none of it is. Operations within a transaction usually share a common intent and are interdependent.
* **Consistency** ensures that the data is consistent both before and after the transaction.
* **Isolation** ensures that one transaction is not impacted by another transaction.
* **Durability** means that the changes made due to the transaction are permanently saved in the system. Committed data is saved by the system so that even in the event of a failure and system restart, the data is available in its correct state.

* To determine what needs to be used, use these **4** categories

  * Data classification: (structured, unstructured, semi structured)
  * Operations: is it Read only? whta type of queries, etc?
  * Latency & throughput: where is latency and throughput expected in the application
  * Transactional support: does it need an ACID db?

  ### Azure Storage Account

  * is a container that groups a set of Azure Storage services together
  * A storage account is an Azure resource and is included in a resource group
  * Azure data services like Azure SQL and Azure Cosmos DB are managed as independent Azure resources and cannot be included in a storage account
* 4 types of Azure Storage:
    * *Azure Blobs*: massively scalable object store for text and binary data. Can include support for Azure Data Lake Storage Gen2.
    * *Azure Files*: Managed file shares for cloud or on-premises deployments.
    * *Azure Queues*: A messaging store for reliable messaging between application components.
    * *Azure Tables*: A NoSQL store for schemaless storage of structured data
  * ![Illustration of an Azure subscription showing some data services that cannot be placed in a storage account.](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-typical-subscription-organization.png)
  * A storage account defines a policy that applies to all the storage services in the account
    * example:
      *  you could specify that all the contained services will be stored in the West US datacenter, accessible only over https, and billed to the sales department's subscription.
  * Settings that are controller by a **storage account** are:
    * Subscription:
      * Azure subscription that will be billed to
    * Location:
      * the datacenter that will store the services in the accoun
    * Performance:
      * Determines the data services you can have in your storage account and the type of hardware disks used to store the data
      * **Standard** allows you to have any data service (Blob, File, Queue, Table) and uses magnetic disk drives.
      * **Premium** introduces additional services for storing data. 
        * For example, storing unstructured object data as block blobs or append blobs, and specialized file storage used to store and create premium file shares. These storage accounts use solid-state drives (SSD) for storage.
    * Replication:
      * Determines the strategy used to make copies of your data to protect against hardware failure or natural disaster
      * At a minimum, Azure will automatically maintain three copies of your data within the data center associated with the storage account
        * this is locally-redundant storage (LRS) and guards against hardware failure but does not protect you from an event that incapacitates the entire datacenter
        * You can upgrade to one of the other options such as geo-redundant storage (GRS) to get replication at different datacenters across the world.
    * Access tier:
      * Controls how quickly you will be able to access the blobs in this storage account
      * Hot gives quicker access than Cool, but at increased cost.
        * This applies only to blobs, and serves as the default value for new blobs.
    * Secure transfer required:
      * determines the supported protocols for access. Enabled requires HTTPs, while disabled allows HTTP.
    * Virtual networks:
      * security feature that allows inbound access requests only from the virtual network(s) you specify.
  * ![Illustration showing two storage accounts with different settings.](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-multiple-storage-accounts.png)
  * The **number of storage accounts** you need is typically determined by your **data diversity**, **cost sensitivity** and **tolerance for management overhead**.
    * Data Diversity
      * Do you have data that is specific to a country or region
      * You will need one storage account for each location
    * Cost sensitivity
      * A storage account by itself has no financial cost
      * the settings you choose for the account do influence the cost of services in the account
      * Geo-redundant storage costs more than locally-redundant storage
      * Premium performance and the Hot access tier increase the cost of blobs.
      * You can use multiple storage accounts to reduce costs
      * you could partition your data into critical and non-critical categories. You could place your critical data into a storage account with geo-redundant storage and put your non-critical data in a different storage account with locally-redundant storage.
    * Tolerance for management overhead
      * requires some time and attention from an administrator to create and maintain
      * increases complexity for anyone who adds data to your cloud storage; 
      * everyone in this role needs to understand the purpose of each storage account so they add new data to the correct account.
  * **3 main Account settings**:
    * **Name**: name must be globally unique within Azure, use only lowercase letters and digits and be between 3 and 24 characters.
    * **Deployment model**: the system Azure uses to organize your resources
      * model defines API you use to create, configure, and manage resources
      * Azure has **2 deployment models**:
        * Resource Manager: the current model that uses the Azure Resource Manager API
        * Classic: a legacy offering that uses the Azure Service Management API
    * **Account kind**: 3 kinds:
      * StorageV2 (general purpose v2): the current offering that supports all storage types and all of the latest features
      * Storage (general purpose v1): a legacy kind that supports all storage types but may not support all features
      * Blob storage: a legacy kind that allows only block blobs and append blobs
  * **A single Azure subscription can host up to 200 storage accounts, each of which can hold 500 TB of data.**