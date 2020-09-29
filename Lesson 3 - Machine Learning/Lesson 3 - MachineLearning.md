# Machine Learning - build a classifier for image types to tell one animal from all others  

## Overview    

Your solution to this point has incorporated an array of cameras that are strategically positioned throughout the research region.  Your data is currently being ingested as live streaming data.  Now you need to work with this data. 

You need a solution that incorporates real-time stream processing to analyze raw data for potential sightings, and one that incorporates artificial intelligence (AI) and machine learning to determine with a high degree of accuracy whether a photo contains a polar bear. 

In this lesson, you will learn about the tools necessary to gather, analyze, and respond correctly to the live data that is coming into your solution.  

## Exercise 3: Analyze Data with Stream Analytics and Azure Functions  

In order to respond to the data in real-time, you need to implement stream analytics with a query that will aggregate and send images to a service for evaluation.  

The service will then respond with a degree of certainty as to the contents of the image.  Once information is validated, the correct responses will be taken to issue any alerts and/or record location data as expected.

## Azure Stream Analytics ##  

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) is a cloud-based service for ingesting high-velocity data streaming from devices, sensors, applications, Web sites, and other data sources and analyzing that data in real time. It supports a [SQL-like query language](https://msdn.microsoft.com/library/azure/dn834998.aspx) that works over dynamic data streams and makes analyzing constantly changing data no more difficult than performing queries on static data stored in traditional databases. With Azure Stream Analytics, you can set up jobs that analyze incoming data for anomalies or information of interest and record the results, present notifications on dashboards, or even fire off alerts to mobile devices. And all of it can be done at low cost and with a minimum of effort.  

Scenarios for the application of real-time data analytics are many and include fraud detection, identity-theft protection, optimizing the allocation of resources (think of an Uber-like transportation service that sends drivers to areas of increasing demand *before* that demand peaks), click-stream analysis on Web sites, and countless others. Having the ability to process data *as it comes in* rather than waiting until after it has been aggregated offers a competitive advantage to businesses that are agile enough to make adjustments on the fly.  

## Stream Analytics Query Language ##  

The Stream Analytics Query Language will give you the tools you need to query your live streams for data based on specific points in time.  This will allow you to collect and aggregate data to determine if specific information is worthy of analysis.  By aggregating the data, you can filter out data that is not worthy of analysis and focus your efforts on the data that is likely to be important.  





## Exercise 4: Predicting Results with Cognitive Services

[Microsoft Cognitive Services](https://azure.microsoft.com/en-us/services/cognitive-services/ "Microsoft Cognitive Services") is a suite of services and APIs backed by machine learning that enables developers to incorporate intelligent features such as facial recognition in photos and videos, sentiment analysis in text, and language understanding into their applications. Microsoft's [Custom Vision Service](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) is among the newest members of the Cognitive Services suite. Its purpose is to create image-classification models that "learn" from labeled images you provide. Want to know if a photo contains a picture of a flower? Train the Custom Vision Service with a collection of flower images, and it can tell you whether the next image includes a flower — or even what type of flower it is.

The Custom Vision Service exposes two APIs: the [Custom Vision Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d9a10a4a5f8549599f1ecafc435119fa/operations/58d5835bc8cb231380095be3) and the [Custom Vision Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/eb68250e4e954d9bae0c2650db79c653/operations/58acd3c1ef062f0344a42814). You can build, train, and test image-classification models using the [Custom Vision Service portal](https://www.customvision.ai/), or you can build, train, and test them using the Custom Vision Training API. Once a model is trained, you can use the Custom Vision Prediction API to build apps that utilize it. Both are REST APIs that can be called from a variety of programming languages.



### Objectives ###

In this exercise, you will learn how to:

- Use the Custom Vision Service to train an image-classification model
- Test a Custom Vision Service model
- Call the model from an app or service

-   [Azure Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics)  


-   [MS Learn Azure Functions](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-core-tools/)  


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