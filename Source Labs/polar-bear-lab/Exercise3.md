## Exercise 3: Analyze Data with Stream Analytics and Azure Functions

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) is a cloud-based service for ingesting high-velocity data streaming from devices, sensors, applications, Web sites, and other data sources and analyzing that data in real time. It supports a [SQL-like query language](https://msdn.microsoft.com/library/azure/dn834998.aspx) that works over dynamic data streams and makes analyzing constantly changing data no more difficult than performing queries on static data stored in traditional databases. With Azure Stream Analytics, you can set up jobs that analyze incoming data for anomalies or information of interest and record the results, present notifications on dashboards, or even fire off alerts to mobile devices. And all of it can be done at low cost and with a minimum of effort.

Scenarios for the application of real-time data analytics are many and include fraud detection, identity-theft protection, optimizing the allocation of resources (think of an Uber-like transportation service that sends drivers to areas of increasing demand *before* that demand peaks), click-stream analysis on Web sites, and countless others. Having the ability to process data *as it comes in* rather than waiting until after it has been aggregated offers a competitive advantage to businesses that are agile enough to make adjustments on the fly.

In this lab, the second of four in a series, you will create an Azure Stream Analytics job and connect it to the IoT hub you created in the previous exercise. Then you will write an [Azure Function](https://azure.microsoft.com/services/functions/) to receive the output, and use the two of them together to analyze data streaming in from a simulated camera array. 

![](images/road-map-2.png)

### Objectives

In this exercise, you will learn how to:

- Create a Stream Analytics job and test queries on sample data streams

- Run a Stream Analytics job and perform queries on live data streams

- Use an Azure IoT hub as a Stream Analytics input

- Use an Azure Function as a Stream Analytics output

### Time Estimate

- 40 minutes

## Task 1: Create a Stream Analytics job

In this task, you will use the Azure Portal to create a Stream Analytics job and connect it to the IoT hub you created in the previous exercise.

1. Expand the portal's left navigation then click **+ Create a resource**. Search for and select **Stream Analytics job** then click **Create**. 

    ![](images/new-stream-analytics-job.png)

2. Name the job **polar-bear-analytics** and place it in the **streaminglab-rg** resource group that you created in the previous lab. Specify **South Central US** as the location. (That's important, because your IoT hub is in the same region, and while you are not charged for data that moves within a data center, you typically *are* charged for data that moves *between* data centers. In addition, locating services that are connected to each other in the same region reduces latency.) Make sure **Hosting environment** is set to **Cloud**, then click **Create**. Click **Go to resource** once creation completes. 

    ![](images/create-stream-analytics-job.png)

3. Click **Inputs** under **Job topology** on the left to add an input to the Stream Analytics job. Click **+ Add stream input**, then select **IoT Hub**.

    ![](images/2018-05-21-12-38-56.png)

4. In the dialog that opens on the right, enter **CameraInput** for the **Input alias** box. Make sure **Select IoT Hub from your subscriptions** is selected and that your IoT Hub is selected under **IoT Hub**. Leave the rest of the settings then click **Save** at the bottom of the blade.

    ![](images/2018-05-21-12-45-59.png)

After a few moments, the new input — "CameraInput" — appears in the list of inputs for the Stream Analytics job. This is the only input you will create, but be aware that you can add any number of inputs to a Stream Analytics job. In the [Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx), each input is treated as a separate data source similar to tables in a relational database. The query language is extremely expressive, even allowing input streams to be joined in a manner similar to joining database tables.

## Task 2: Prepare a query and test with sample data

The heart of a Stream Analytics job is the query that extracts information from the data stream. It is always advisable to test a query using sample data before deploying it against a live data stream, because with sample data, you can verify that a known set of inputs produces the expected outputs. In this task, you will enter a query into the Stream Analytics job you created in the previous task and test it with sample data.

1. Click **Query** under **Job topology** on the left.

    ![](images/2018-10-19-12-23-18.png)

2. Select the **CameraInput** input, then click **Upload sample input** on the **Input preview** tab at the bottom of the page.

    ![](images/2019-10-20-14-33-01.png)

3. In the dialog that appears on the right, click the **folder** icon and select the file named **C:\\SkillMeUp\\SampleData\\sample-data.json.** Then click **OK** to upload the file.

4. When the upload is complete, enter the following query into the query window. Then click **Test query** to execute it:

	```sql
	SELECT 
		deviceId,
    	latitude,
        longitude,
        url,
        timestamp,
        EventProcessedUtcTime,
        PartitionId,
        EventEnqueuedUtcTime
	FROM CameraInput
	```

	![](images/2019-10-20-14-32-25.png)

5. Confirm that you see the output pictured below. The test data contains 50 rows, each representing an event transmitted to the IoT hub by one of the cameras in the camera array. **deviceid** is the camera's device ID, **latitude** and **longitude** specify the camera's location, **url** is the URL of the blob containing the picture that was taken, and **timestamp** is the time at which the picture was taken. The other fields were added by Azure.

	![](images/2019-10-20-14-34-28.png)

6. One of the key features of the Stream Analytics Query Language is its ability to group results using windows of time whose length you specify. Windowing is enacted by using the keywords [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx), [HoppingWindow](https://msdn.microsoft.com/library/azure/dn835041.aspx), [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx) and [SessionWindow](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/session-window-azure-stream-analytics) in a GROUP BY clause. To demonstrate, enter the following query and click **Test query** to execute it. This will count the number of times the cameras were triggered each minute:

	```sql
	SELECT System.Timestamp as [Time Ending],
	    COUNT(*) AS [Times Triggered]
	FROM CameraInput TIMESTAMP BY timestamp
	GROUP BY TumblingWindow(n, 1)
	```
	> [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/mt573293.aspx) is an important element of the Stream Analytics Query Language. If it was omitted from the query above, you would be querying for the number of events that arrived *at the event hub* each minute rather than the number of events that occurred at the camera locations. TIMESTAMP BY allows you to specify a field in the input stream as the event time.

7. Confirm that you see the output shown below:

	![](images/2019-10-20-14-36-18.png)

8. Now it's time to check for two photos snapped by the same camera within 10 seconds. **This is the query that you will use against a live data stream**. The assumption is that since polar bears tend to move rather slowly, we will ignore pictures taken more than 10 seconds apart, but if the same camera snaps two pictures within 10 seconds, it is worth examining them to see if one of them contains a polar bear.

	Enter the following query and click **Test query** to execute it:

	```sql
	SELECT C1.deviceId, C1.latitude, C1.longitude, C1.url, C1.timestamp
	FROM CameraInput C1 TIMESTAMP BY timestamp
	JOIN CameraInput C2 TIMESTAMP BY timestamp
	ON C1.deviceId = C2.deviceId
	AND DATEDIFF(ss, C1, C2) BETWEEN 0 AND 10
	AND C1.Timestamp != C2.Timestamp
	```

9. This time the output should contain six rows, each representing two photographs taken by the same camera within 10 seconds and containing the URL of one of the pictures.

	> If you wanted to include *both* URLs in the output, how would you modify the query to do it?

	![](images/2019-10-20-14-38-46.png)

Finish up by clicking **Save query** at the top of the blade to save the query. This will be the query that's executed when you run the Stream Analytics job.

## Task 3: Direct output to an Azure Function

The query that you tested in the previous task employs simple logic: if the same camera snaps two pictures within 10 seconds, there *might* be a polar bear. But the ultimate goal is to determine with a great deal of confidence whether there really *is* a polar bear. That means supplementing Stream Analytics with machine learning.

One way to connect a Stream Analytics job to a machine-learning model running in the cloud is to use an [Azure Function](https://azure.microsoft.com/services/functions/) as a Stream Analytics output. The function, which is invoked each time Stream Analytics produces an output, can then call out to the machine-learning model. In this task, you will write an Azure Function, connect it to Stream Analytics, and stub it out so you can verify that it's being called.

1. Expand the portal's left navigation then click **+ Create a resource**, followed by **Function App**.

    ![](images/new-function-app.png)

2. Enter the following information and then click **Next: Hosting**: 

	- Subscription: *Accept the default*

	- Resource Group: **streaminglab-rg**

	- Function App name: ***unique name for your function***

	- Publish: **Code**
  
	- Runtime stack: **Node.js**
  
	- Region: *The region you are using for this lab*

	>**Note:** When you create an Azure Function App, you can choose from two hosting plans: Consumption plan or App Service plan. The former is cheaper because you only pay when the function executes. But with Consumption plan, the function might not execute for several minutes after it's called. With App Service plan, you pay more, but the function runs immediately.

	>**Note:** Make note of the function app name as it will be needed in a later step.
 
    ![](images/2020-01-29-23-44-23.png)

3. On the hosting tab, enter the following information then click **Review + create** then **Create**.

	- Storage account: *accept the default*
  
	- Operating system: **Windows**

	- Plan type: **App service plan**
  
	- Linux Plan: *accept the default*
  
	- Sku and size: **Standard S1**

4. Wait for your deployment to complete then click **Go to resource** to navigate to your function app.

5. Click **TLS/SSL settings** under **Settings** on the left. Change the minimum TLS version to **1.0**.

	![](images/2018-10-19-15-29-46.png)

6. Click **Functions** under **Functions** on the left then click **+ Add**. 

    ![](images/2018-10-19-17-55-33.png)

7. In the **Add function** dialog that appears on the right, select **Develop in portal** for the **Development environment** and select **HTTP trigger**. Click **Add**.

	![](images/2018-10-19-17-56-44.png)

8. In the new function page that appears, click **Code + Test** under **Developer** on the left. Replace the code shown in the code editor with the statements below. Then click **Save** then **Test/Run**. In the dialog that appears on the right, click **Run**. 

	```javascript
	module.exports = function (context, req) {
	    context.log(req.rawBody);
	    context.done();
	};
	```

	![](images/save-and-run.png)

9. The dialog should show a message of **200 OK** under **HTTP response code**. Now click **Function Keys** under **Developer** on the left. Click the **Hidden value...** button then copy the **default** function key value to notepad. 

	![](images/FunctionResponse.png)

	![](images/ManageKey.png)

10. Navigate to the Stream Analytics job in the portal and click **Outputs** under **Job topology** on the left.

	![](images/add-output-1.png)

11. Click **+ Add**, then choose **Azure Function** from the list.

	![](images/2018-03-30-13-53-12.png)

12. In the dialog that appears on the right, enter **FunctionOutput** for the **OutputAlias**. Choose **Provide azure function settings manually**. Enter the name of the function app you created and noted earlier for the **Function app**. Enter the name of the function created earlier, **HttpTrigger1**, for the **Function**. Paste in the function key copied earlier as well for the **Key**. Then click **Save**.

	![](images/2018-03-30-13-58-27.png)

	> Just as a Stream Analytics job will accept multiple inputs, it supports multiple outputs, too. In addition to passing the output to an Azure Function, you could easily add outputs to log the output from the job in an Azure SQL database, a Cosmos DB database, blob storage, and other locations.

12. Wait for the output to appear in the list of outputs, indicating that it has been successfully added to the Stream Analytics job. Then click **Query** under **Job topology** on the left and modify the query you wrote in the previous task to include an ```INTO``` clause (line 2 below) that directs query results to the output you just added:

	```sql
	SELECT C1.deviceId, C1.latitude, C1.longitude, C1.url, C1.timestamp
	INTO FunctionOutput
	FROM CameraInput C1 TIMESTAMP BY timestamp
	JOIN CameraInput C2 TIMESTAMP BY timestamp
	ON C1.deviceId = C2.deviceId
	AND DATEDIFF(ss, C1, C2) BETWEEN 0 AND 10
	AND C1.Timestamp != C2.Timestamp
	```

13. Click **Save query** to save the query. Then click **Overview** on the left blade and click **Start**.

    ![](images/start-stream-analytics-job-1.png)

14. Make sure **Job output start time** is set to **Now** then click **Start** to start the run.

    ![](images/start-stream-analytics-job-2.png)

The job will take a couple of minutes to start, but you don't have to wait. Proceed to the next task and start generating events for Stream Analytics to ingest.

## Task 4: Analyze a live data stream

In this task, you will use Node.js to stream events from the simulated camera array that you registered previously in the lab. Then you will check the log output from the Azure Function to verify that the events are being received by IoT hub, input to Stream Analytics, and output to the function. 

1. Return to Visual Studio Code and create a new file (**File** > **New File**). Paste the following code and save it to **C:\\SkillMeUp** as **run.js**.

	```javascript
	'use strict';

	var iotHubName = 'HUB_NAME';
	var storageAccountName = 'ACCOUNT_NAME';
	var storageAccountKey = 'ACCOUNT_KEY';
	
	class Camera {
	    constructor(id, latitude, longitude, key, files) {
	        this._id = id;
	        this._latitude = latitude;
	        this._longitude = longitude;
	        this._key = key;
	        this._files = files.slice(0);
	        this._ready = false;
	    }
	
	    get id() {
	        return this._id;
	    }
	
	    connect(iotHubName, storageAccountName, storageAccountKey, callback) {
	        // Connect to blob storage
	        var azure = require('azure-storage');
	        this._storageAccountName = storageAccountName;
	        this._blobService = azure.createBlobService(storageAccountName, storageAccountKey);
	
	        // Connect to the IoT hub
	        var connectionString = 'HostName=' + iotHubName + '.azure-devices.net;DeviceId=' + this._id + ';SharedAccessKey=' + this._key;
	        var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
	        this._client = clientFromConnectionString(connectionString);
	
	        this._client.open((err) => {
	            if (!err) {
	                this._ready = true;
	            }
	
	            callback(this._ready);
	        });
	    }
	
	    start() {
	        // Register first callback for 5 to 60 seconds
	        setTimeout(this.timer, (Math.random() * 30000) + 1000, this);
	    }
	
	    timer(self) {
	        if (self._ready === true) {
	            // "Trigger" the camera with a random photo
	            var index = Math.floor(Math.random() * self._files.length);
	            self.trigger(self._files[index], (err, result) => {});
	
	            // Register another callback for 5 to 60 seconds
	            setTimeout(self.timer, (Math.random() * 55000) + 5000, self);
	        }
	    }
	
	    trigger(imageFileName, callback) {
	        if (this._ready === true) {
	            // Upload the image to blob storage
	            this.upload(imageFileName, (err, result) => {
	                if (err) {
	                    callback(err, result);
	                }
	                else {
	                    // Send an event to the IoT hub
	                    this.send(imageFileName, (err, result) => {
	                        console.log(this._id + ': https://' + this._storageAccountName + '.blob.core.windows.net/photos/' + imageFileName);
	                        callback(err, result);
	                    });
	                }
	            });
	        }
	    }
	
	    upload(imageFileName, callback) {
	        this._blobService.createBlockBlobFromLocalFile('photos', imageFileName, 'photos/' + imageFileName, (err, result) => {
	            callback(err, result);
	        });
	    }
	        
	    send(imageFileName, callback) {
	        var Message = require('azure-iot-device').Message;
	
	        var data = {
	            'deviceId' : this._id,
	            'latitude' : this._latitude,
	            'longitude' : this._longitude,
	            'url' : 'https://' + this._storageAccountName + '.blob.core.windows.net/photos/' + imageFileName,
	            'timestamp' : new Date().toISOString()
	        };
	
	        var message = new Message(JSON.stringify(data));
	
	        this._client.sendEvent(message, (err, result) => {
	            callback(err, result);
	        });        
	    }
	}
	
	// Load image file names
	var fs = require('fs');
	
	fs.readdir('photos', (err, files) => {
	    // Create an array of cameras
	    var cameras = JSON.parse(fs.readFileSync('cameras.json', 'utf8')).map(
	        camera => new Camera(
	            camera.deviceId,
	            camera.latitude,
	            camera.longitude,
	            camera.key,
	            files
	        )
	    );
	
	    // Start the cameras
	    cameras.forEach(camera => {
	        camera.connect(iotHubName, storageAccountName, storageAccountKey, status => {
	            if (status === true) {
	                console.log(camera.id + ' connected');
	                camera.start();
	            }
	            else {
	                console.log(camera.id + ' failed to connect');
	            }
	        })
	    });
	});
	```

	This code uses the new [class support](http://es6-features.org/#ClassDefinition) in ECMAScript 6 (ES6) to define a class named ```Camera```. Then it creates 10 ```Camera``` instances and starts them running. Each camera object connects to the IoT hub securely using an access key obtained from **cameras.json**, and then uses a random timer to transmit events every 5 to 60 seconds. Each event that is transmitted includes the camera's ID, latitude, and longitude, as well as an image URL and a timestamp. The URL refers to an image that the camera uploaded to blob storage before firing the event. Images are randomly selected from the files in the project directory's "photos" subdirectory.

2. Replace HUB_NAME on line 3 with the name of the IoT hub that you created in the previous exercise, ACCOUNT_NAME on line 4 with the name of the storage account that you created in the same exercise, and ACCOUNT_KEY on line 5 with the storage account's access key (The value of the last **SharedAccessKey** part of the connection string you noted). Save the file.

3. In the command prompt (You may have to hit **CTRL + c** in the command prompt), use the following command to run **run.js**:

	```
	node run.js
	```

4. Confirm that you see output similar to the following, indicating that all 10 "cameras" are connected to the IoT hub:

	![](images/RunJSOutput.png)

	The order in which the cameras connect to the IoT hub will probably differ from what's shown here, and will also vary from one run to the next.

5. After a few seconds, additional output should appear. Each line corresponds to an event transmitted from a camera to the IoT hub. The output will look something like this:

	![](images/RunJSSecondOutput.png)

6. Confirm that the cameras are running and generating events as shown above. Then return to the Azure Function (**Your function app** > **Functions**) in the portal and check the output log by clicking **Monitor** under **Developer** on the left. Then click the **Logs** tab. Verify that over the course of a few minutes, the log shows several outputs from Stream Analytics. (The frequency will vary because the cameras use random timers to fire events.) Note that each output contains a JSON payload containing a device ID, latitude, longitude, blob URL, and timestamp.

	> Remember that the Stream Analytics job doesn't forward every event it receives to the function. It generates an output *only* when one camera snaps two photos within 10 seconds.

	![](images/2018-05-21-15-18-34.png)

7. Navigate to the Stream Analytics job in the portal and click **Stop** to stop it. Then click **Yes** when asked to confirm that you want to stop the job.

8. Return to the Command Prompt or terminal window in which **run.js** is running and press **Ctrl+C** (**Command-C** on a Mac) to stop it, and therefore stop the flow of events from the simulated cameras.

You have confirmed that Stream Analytics is receiving input from the IoT hub and that the Azure Function is receiving input from Stream Analytics. In the next exercise, you will build a machine-learning model and invoke it from the Azure Function.

## Summary

In this exercise, you created a Stream Analytics job, connected it to an IoT hub for input, and connected it to an Azure Function for output. You also learned how Stream Analytics queries are used to extract information from temporal data streams.

---

Copyright 2018 Microsoft Corporation. All rights reserved. Except where otherwise noted, these materials are licensed under the terms of the MIT License. You may use them according to the license as is most appropriate for your project. The terms of this license can be found at https://opensource.org/licenses/MIT.
