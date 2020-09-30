# LAB GUIDE

## Building Realtime AI Applications with Cognitive Services and Stream Analytics

### Overview

You are the leader of a group of climate scientists who are concerned about the dwindling polar-bear population in the Arctic. As such, your team has placed hundreds of motion-activated cameras at strategic locations throughout the region. Rather than manually examine each photograph to determine whether it contains a polar bear, you have been challenged to devise an automated system that processes data from these cameras in real time and displays an alert on a map when a polar bear is photographed. You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. And you need it fast, because climate change won't wait.

In this lab, you will build such a system using [Microsoft Azure](https://azure.microsoft.com/) and [Microsoft Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Specifically, you will use an [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/) to ingest streaming data from simulated cameras, [Azure Storage](https://azure.microsoft.com/services/storage/?v=16.50) to store photographs, [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) to process real-time data streams, [Azure Functions](https://azure.microsoft.com/services/functions/) to process output from Stream Analytics, Microsoft's [Custom Vision Service](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) to analyze photographs for polar pears, [Microsoft Power BI](https://powerbi.microsoft.com/) to build a dashboard for visualizing results, and [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) as a data source for Power BI.

### Learning Objectives

In this lab you will learn to:

- Configure real-time stream ingestion using Azure IoT Hub

- Analyze real-time streaming data using Azure Stream Analytics

- Predict results using Azure Functions and Cognitive Services

- Visualize output in real-time using Azure SQL Database and Power BI


### Accessing Microsoft Azure

Launch a browser from your lab virtual machine and navigate to the URL below and login using the Azure credentials given to you by your instructor.

```html
https://portal.azure.com
```


## Exercise 1: Install Node

In this exercise, you will install Node. 

### Time Estimate

- 10 minutes

### Task 1: Install Node

1. Open Chrome and navigate to the following url. 

    ```
    nodejs.org
    ```

2. Click the **Recommended For Most Users** option. 

    ![](Images/2019-10-18-11-04-54.png)

3. Click the .msi file that appears in the download prompt at the bottom of the browser window then step through the setup wizard. You can accept all the default settings. At the end of the setup, click **Finish**. 

    ![](Images/nodesetup.png)
