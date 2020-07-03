# Problem Analysis

Understanding ecology though tracking animal populations.  Search out and aggregate animal population data 

### Overview

You are the leader of a group of climate scientists who are concerned about the dwindling polar-bear population in the Arctic. As such, your team has placed hundreds of motion-activated cameras at strategic locations throughout the region. Rather than manually examine each photograph to determine whether it contains a polar bear, you have been challenged to devise an automated system that processes data from these cameras in real time and displays an alert on a map when a polar bear is photographed. You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. And you need it fast, because climate change won't wait.

### Learning Objectives

In this lab you will learn to:

- Configure real-time stream ingestion using Azure IoT Hub
- Analyze real-time streaming data using Azure Stream Analytics
- Predict results using Azure Functions and Cognitive Services
- Visualize output in real-time using Azure SQL Database and Power BI


## Exercise 2: Ingest Streaming Data with IoT Hub ##

In this exercise, you will create a storage account and an IoT hub and write a [Node.js](https://nodejs.org/) app that connects them to a simulated camera array. Then you will test one of the virtual cameras by having it upload a photograph to blob storage and transmit an event containing the blob URL and other information to the IoT hub.

### Objectives ###

In this hands-on lab, you will learn how to:

- Create an Azure storage account
- Create an Azure IoT hub
- Make authenticated calls to an Azure IoT hub
- Upload images to Azure blob storage from an app or device

