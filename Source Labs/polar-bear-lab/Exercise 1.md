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

## Accessing and ending the Lab Environment

SkillMeUp Real Time Labs use a virtual machine for all lab exercises. This allows you access to all of the tools and software needed to complete the lab without requiring you to install anything on your local computer. 

The virtual machine may take several minutes to fully provision due to software installation and supporting files to copy.

After you have completed all of the lab exercises ensure you click the End Lab button to get access to your certification of completion.

## Accessing Microsoft Azure

Launch **Chrome** from the virtual machine desktop and navigate to the URL below. Your Azure Credentials are available by clicking the Cloud Icon at the top of the Lab Player.

```
https://portal.azure.com
```

---

Copyright 2018 Microsoft Corporation. All rights reserved. Except where otherwise noted, these materials are licensed under the terms of the MIT License. You may use them according to the license as is most appropriate for your project. The terms of this license can be found at https://opensource.org/licenses/MIT.

### Task 1: Verify Node Installation

1. Verify your node install by running the following two commands from a command prompt. If they both return version numbers then you may skip the rest of this exercise and continue straight to the next exercise.

    ```
    node -v
    ```

    ```
    npm -v
    ```

### Task 2: Install Node

1. In a browser, navigate to **nodejs.org**

2. Click the **Recommended For Most Users** option. 

    ![](images/2019-10-18-11-04-54.png)

3. Step through the setup wizard. 

    ![](images/nodesetup.png)

4. Once Node finishes installing, **LABVM**
