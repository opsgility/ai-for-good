# Problem Analysis

Understanding ecology though tracking animal populations.  Search out and aggregate animal population data 

### Overview

You are the leader of a group of climate scientists who are concerned about the dwindling polar-bear population in the Arctic. As such, your team has placed hundreds of motion-activated cameras at strategic locations throughout the region. Rather than manually examine each photograph to determine whether it contains a polar bear, you have been challenged to devise an automated system that processes data from these cameras in real time and displays an alert on a map when a polar bear is photographed. You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. And you need it fast, because climate change won't wait.

### Learning Objectives

Over the next few lessons you will learn to:

- Configure real-time stream ingestion using Azure IoT Hub
- Analyze real-time streaming data using Azure Stream Analytics
- Predict results using Azure Functions and Cognitive Services
- Visualize output in real-time using Azure SQL Database and Power BI


## Ingesting Streaming Data with IoT Hub ##

In this lesson, you will create a storage account and an IoT hub and write a [Node.js](https://nodejs.org/) app that connects them to a simulated camera array. Then you will test one of the virtual cameras by having it upload a photograph to blob storage and transmit an event containing the blob URL and other information to the IoT hub.

### Objectives ###

In this lesson, you will learn how to:

- Create an Azure storage account
- Create an Azure IoT hub
- Make authenticated calls to an Azure IoT hub
- Upload images to Azure blob storage from an app or device

### Data Storage in Azure ###

#### Blob Storage

Blob storage is a cloud based object store used for raw data storage.

#### Data Lake Storage

Data Lake Store is a hierarchical file system optimized for big data analytics workloads. 

#### Relational Databases

Relational databases store data in tables made up of rows and columns. Azure supports various types of relational databases such as Azure SQL Database and MySQL. Data stored in a relational database is considered structured data. Structured data is data that has a fixed schema. When working with structured data the developer must know the schema.

#### NoSQL Data Stores

NoSQL data stores are designed to store any data that is not structured. Data is commonly stored in a document, these documents are generally stored in a common format such as JSON. 

In Azure, the most common NoSQL database is Azure CosmosDB. CosmosDB is a multi-model database meaning that it can be configured to support 

### Data Ingestion ###

#### Batch Ingestion 

Storage Explorer

AzCopy

PowerShell and Azure CLI

Data Factory

#### Real-time Ingestion 

### Data Processing ###

#### Batch Processing 

Databases 

Synapse Analytics

Databricks

#### Real-time Processing

Stream Analytics

Databricks
