# Machine Learning - build a classifier for image types to tell one animal from all others  

## Overview    

Your solution to this point has incorporated an array of cameras that are strategically positioned throughout the research region.  Your data is currently being ingested as live streaming data.  Now you need to work with this data. 

You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. 

In this lesson, you will learn about the tools necessary to gather, analyze, and respond correctly to the live data that is coming into your solution.   

## Lesson 3: Analyze Data with Stream Analytics and Azure Functions, then implement an Azure Cognitive Services Computer Vision solution  

In order to respond to the data in real-time, you need to implement stream analytics with a query that will aggregate and send images to a service for evaluation.  

The service will then respond with a degree of certainty as to the contents of the image.  Once information is validated, the correct responses will be taken to issue any alerts and/or record location data as expected.

## Azure Stream Analytics ##  

[Azure Stream Analytics (ASA)](https://azure.microsoft.com/services/stream-analytics/) is a cloud-based service for ingesting high-velocity data streaming from devices, sensors, applications, Web sites, and other data sources and analyzing that data in real time. ASA supports a [SQL-like query language](https://msdn.microsoft.com/library/azure/dn834998.aspx) that works over dynamic data streams and makes analyzing constantly changing data no more difficult than performing queries on static data stored in traditional databases. With ASA, you can set up jobs that analyze incoming data for anomalies or information of interest and record the results, present notifications on dashboards, or even fire off alerts to mobile devices. And all of it can be done at low cost and with a minimum of effort.  

Scenarios for the application of real-time data analytics are many, and include fraud detection, identity-theft protection, optimizing the allocation of resources (think of an Uber-like transportation service that sends drivers to areas of increasing demand *before* that demand peaks), click-stream analysis on Web sites, and countless others. Having the ability to process data *as it comes in* rather than waiting until after it has been aggregated offers a competitive advantage to businesses that are agile enough to make adjustments on the fly.  

## Stream Analytics Query Language ##  

The Stream Analytics Query Language will give you the tools you need to query your live streams for data based on specific points in time.  This will allow you to collect and aggregate data to determine if specific information is worthy of analysis.  By aggregating the data, you can filter out data that is not worthy of analysis and focus your efforts on the data that is likely to be important.  

### Built In Functions ###  

There are many built-in functions within Stream Analytics, and they span a number of categories.  The categories are:  

-   Aggregate Functions to return a single summarized value   

-   Analytic Functions to return a value based on defined constants  

-   Array Functions that get element information from Arrays  

-   GeoSpatial Functions that are useful for getting point data and distance between points.  

-   Input Metadata Functions for data properties  

-   Record Functions to return properties or values  

-   Windowing functions for information on events within a specified window of time  

-   Scalar Functions to operate on a single value and return a single value  

-   Conversion Functions to cast data from one format to another  

-   Date and Time Functions for operations based on DateTime data  

-   Mathematical Functions to do math against data and return a value  

-   String Functions for performing operations against Strings, getting things like length or substrings, or even matching to a regular expression  

### Data Types  ###

As with most programming languages and with T-SQL, the Stream Analytics Query Language has some defined data types, including:  

-   bigint  
-   float  
-   nvarchar(max)  
-   datetime  
-   bit  
-   record  
-   array  

### Query Language Elements ###  

The Stream Analytics Query Language provides elements to assist you when you are building out queries.  They are:  

-   APPLY &mdash; Invoke a table-valued function using CROSS APPLY or OUTER APPLY.  

-   CASE &mdash; Evaluate a list of conditions and return one result based on which case statement is true.  

-   COALESCE &mdash; Returns the first value that does not result in NULL  

-   CREATE TABLE &mdash; Define the schema for data on incoming events  

-   FROM &mdash; Used to define the location of data during a SELECT event or a step name in a WITH clause  

-   GROUP BY &mdash; Create groups of data to summarize values from columns and expressions  

-   HAVING &mdash; Used with SELECT to narrow results to a specific group or aggregate result  

-   INTO &mdash; Used with SELECT to place data into an output stream  

-   JOIN &mdash; Used to combine records from multiple input sources.  Joins must define a window of time from which to select data  

-   MATCH_RECOGNIZE &mdash; Query a stream to get a set of events  

-   OVER &mdash; Group rows before performing functions against data  

-   SELECT &mdash; Used to get data based on specified conditions  

-   UNION &mdash; Combine results from multiple queries into one result set  

-   WHERE &mdash; Defines narrowing conditions to limit data as specified  

-   WITH &mdash; Used with SELECT and FROM, defines a temporary data set for referencing within the query  

### Time Management ###  

Time management exists to get data within windows of time.  Events will have timestamps &mdash; System.Timestamp() &mdash; that can be leveraged to query the data, which can be spans of time or differences in time.  

Custom values can be used to gain information from messages that are received based on the time they were received, versus the time they were processed with the `TIMESTAMP BY` functionality. 

`Time Skew Policies` can be used to determine if events arrived out of sequence or at an unexpected time.  

When you need to perform calculations within a time window, you'll use the `Aggregate Functions`.  

Finally, DATEDIFF and Date and Time functions provide a number of ways to get data within specified time intervals.  

### Windowing Functions ###  

As you build out your queries, you will likely need to check for specific sequences of events or for messages that would be received within specified time intervals.  With Stream Analytics, you'll use Windowing functions to analyze your data operations over specific intervals of time.  Depending on your need, you will choose from one of the various Window Types:  

-   Tumbling Window &mdash; Consecutive timespans one after another for a pre-defined interval of time (30 seconds, for example).  

-   Hopping Window &mdash; Timespans that can overlap, defined to run for a specified interval, starting at a set interval.  For example, run for 30 seconds but start new windows every 15 seconds.  

-   Sliding Window &mdash; Windows of a pre-determined length are possible over any time interval within the defined timespan.  The system responds when an event happens within the sliding window.   For example, a result set from time a to time b that gives a 30 second sliding window with an aggregate of something occurring 5 times in that 30 seconds.  

-   Session window &mdash; Events are grouped based on when they happened, such as two events within 30 seconds of each other.  

-   Snapshot window &mdash; Events are grouped by the fact that they arrived at the same time for the same topic. 

-   [Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics)  

-   [MS Learn Stream Analytics](https://docs.microsoft.com/en-us/learn/paths/implement-data-streaming-with-asa/)  

## Azure Functions  ##  

Azure functions are a core service in the Azure ecosystem.  Azure Function apps provide the ability to host multiple functions in a serverless environment, which allows developers the ability to quickly and easily host numerous functions that are independent of traditional monolithic applications.  

Azure functions can be triggered by different events or via public https endpoints.  Functions can also be scheduled to run at specified intervals.  

With the ability to respond to events, Azure Functions are ideally positioned to quickly respond to streaming event data to perform further transformations and analysis, or to direct traffic to the next part of the solution as needed.  

Azure functions are highly scalable, so throughput and processing can scale to meet the demands of the system as required.  

-   [Azure Functions](https://azure.microsoft.com/en-us/services/functions/)  

-   [Getting started with Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-csharp)  

-   [MS Learn Azure Functions](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-core-tools/)  

## Azure Cognitive Services Computer Vision ##  

Computer vision in the Azure Cognitive Services suite gives you the ability to build a solution that can analyze and respond to content in images and video.  

The uses for this service are very widespread, and include, but are not limited to:  

-   Labelling content  

-   Extracting text with Optical Character Recognition  

-   Providing descriptions for images  

-   Moderating content  

Computer vision can be run from Azure or in Azure edge.  

One of the powerful features of computer vision is the ability to discern the actual contents of an image.  The system can then determine how to classify the images based on what exists in the analyzed image.  The Analyze Image API can be used to filter content based on many different potential characteristics.  

Images can be analyzed to determine if they contain adult material, brand images, if they are color or grayscale, and if objects or faces are present.  Additionally, image analysis can categorize and tag images based on pre-defined tags and categories depending on the content of the images.  

-   [Computer Vision](https://azure.microsoft.com/en-us/services/cognitive-services/computer-vision/)  

-   [What is Computer Vision](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/overview)  

-   [MS Learn Computer Vision](https://docs.microsoft.com/en-us/learn/paths/explore-computer-vision-microsoft-azure/)  

# Challenge # 

## Process and Predict  

Now that you have researched and evaluated the tools at Azure for working with Stream Analytics, Azure Functions, and Azure Cognitive Services (Vision), it is time to put things together with a practical challenge.  

To complete this lesson, you will continue building out the solution to track the polar bear populations.  You will start this part by creating an Azure Stream Analytics job and then you will connect it to the IoT hub you created in the previous exercise.  

Once you have connected your IoT hub to the stream analytics job, you will then write an Azure Function to receive the output, and use these two services together to analyze data streaming in from your previously assembled camera array.  

![](images/road-map-2.png)  

With the camera array and data processing in place, you will complete this lesson by creating a Custom Vision Service model. After creating the model, you will proceed to train it to differentiate between various types of Arctic wildlife. Finally, you will connect the Vision Service with a trained model to the Stream Analytics job you created in the previous lesson.  

![](images/road-map-3.png)  

## Objectives ##

In this exercise, you will learn how to:  

- Create a Stream Analytics job and test queries on sample data streams  

- Run a Stream Analytics job and perform queries on live data streams  

- Use an Azure IoT hub as a Stream Analytics input  

- Use an Azure Function as a Stream Analytics output  

Good Luck!  

<!-- ([Link to lab]()) -->