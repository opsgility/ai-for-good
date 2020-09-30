## Exercise 2: Ingest Streaming Data with IoT Hub

In this exercise, you will create a storage account and an IoT hub and write a [Node.js](https://nodejs.org/) app that connects them to a simulated camera array. Then you will test one of the virtual cameras by having it upload a photograph to blob storage and transmit an event containing the blob URL and other information to the IoT hub.

![](images/road-map-1.png)

### Objectives

In this exercise, you will learn how to:

- Create an Azure storage account

- Create an Azure IoT hub

- Make authenticated calls to an Azure IoT hub

- Upload images to Azure blob storage from an app or device

### Time Estimate

- 40 minutes

### Task 1: Create a storage account

In this task, you will use the Azure CLI to create an Azure storage account in the cloud. This storage account will store as blobs photographs taken by the simulated cameras that you deploy. Note that you can also create storage accounts using the [Azure Portal](https://portal.azure.com). Whether to use the CLI or the portal is often a matter of personal preference.

1. Navigate to the following url and sign in with the credentials located in the cloud icon. When prompted, click **Bash** then **Create storage**. 

	```
	shell.azure.com
	```

2. First, you will create a resource group to hold the storage account and other Azure resources that make up the solution you're building. In the Azure CLI, run the following command to create a resource group named "streaminglab-rg" in Azure's South Central US region:

	```
	az group create --name streaminglab-rg --location southcentralus
	```

3. Now use the following command to create a general-purpose storage account in the "streaminglab-rg" resource group. Replace **ACCOUNT_NAME** with the name you wish to assign the storage account. Be sure to make note of it in Notepad. The account name must be unique within Azure, so if the command fails because the storage-account name is already in use, change the name and try again. In addition, storage-account names must be from 3 to 24 characters in length and can contain only numbers and lowercase letters.

	```
	az storage account create --name ACCOUNT_NAME --resource-group streaminglab-rg --location southcentralus --kind StorageV2 --sku Standard_LRS
	```

4. Before you can upload blobs to a storage account, you must create a container in which to store them. Use the following command to create a container named **photos** in the storage account, replacing ACCOUNT_NAME with the name you assigned to the storage account in the previous step:

	```
	az storage container create --name photos --account-name ACCOUNT_NAME
	```

You now have a storage account for storing photos taken by your simulated cameras, and a container to store them in. Now let's create an IoT hub to receive events transmitted by the cameras.

### Task 2: Create an IoT hub

Azure Stream Analytics supports several types of input, including input from [Azure IoT hubs](https://azure.microsoft.com/services/iot-hub/). In the IoT world, data is easily transmitted to IoT hubs through field gateways (for devices that are not IP-capable) or cloud gateways (for devices that *are* IP-capable), and a single Azure IoT hub can handle millions of events per second from devices spread throughout the world. IoT hubs support secure two-way communications with the devices connected to them using a variety of transport protocols.

In this task, you will create an Azure IoT hub to receive input from a simulated camera array.

1. Run the following command to create an IoT Hub in the same region as the storage account you created in the previous task and place it in the "streaminglab-rg" resource group. Replace HUB_NAME with an IoT hub name, which must be unique across Azure and conform to DNS naming conventions. Be sure to make note of the hub name in Notepad. 

	```
	az iot hub create --name HUB_NAME --resource-group streaminglab-rg --location southcentralus --sku F1 --partition-count 2
	```

	> **Note:** The ```--sku F1``` parameter configures the IoT hub to use the free F1 pricing tier, which supports up to 8,000 events per day. 

2. Run the following command to retrieve a connection string for the IoT hub, replacing HUB_NAME with the name you assigned to the IoT hub in the previous step:

	```
	az iot hub show-connection-string --name HUB_NAME
	```

3. Copy the connection-string value (Excluding the quotation marks ") from the output and paste it into Notepad so you can retrieve it later. That value will look like the following, where HUB_NAME is the name of your IoT hub, and KEY_VALUE is the hub's shared access key.

	```
	HostName=HUB_NAME.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=KEY_VALUE
	```

The connection string that you just retrieved is important, because it will enable the app that you build in the next exercise to connect to the IoT hub securely and register an array of virtual devices.

### Task 3: Deploy a simulated camera array

Devices that transmit events to an Azure IoT hub must first be registered with that IoT hub. Once registered, a device can send events to the IoT hub using one of several protocols, including HTTPS, [AMPQ](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf), and [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf). Calls must be authenticated, and IoT hubs support several forms of authentication as described in [Control access to IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). In this task, you will create a Node.js app that registers an array of simulated cameras with the IoT hub you created in the previous exercise.

1. Open a command prompt by clicking the Start menu, then enter **cmd** and click **Command Prompt** in the search results. 

2. Navigate to the **C:\\SkillMeUp** directory in the command prompt with the below command.

	```
	cd c:\SkillMeUp
	```

2. Execute the following commands in sequence to initialize the project directory to host a Node project and install a trio of packages that Node can use to communicate with Azure IoT hubs:

	```
	npm init -y
	```

	```
	npm install azure-iothub --save
	```

	```
	npm install azure-iot-device azure-iot-device-mqtt --save
	```

	The [azure-iothub](https://www.npmjs.com/package/azure-iothub) package provides APIs for registering devices with IoT hubs and managing device identities, while [azure-iot-device](https://www.npmjs.com/package/azure-iot-device) and [azure-iot-device-mqtt](https://www.npmjs.com/package/azure-iot-device-mqtt) enable devices to connect to IoT hubs and transmit events using the MQTT protocol.

3. Wait for the installs to finish. Then open **Visual Studio Code** from the Start menu, create a new file (**File** > **New File**), and paste in the following json code. Save the file to **C:\\SkillMeUp** (**File** > **Save**) and name it **devices.json**.

	```json
	[
	    {
	        "deviceId" : "polar_cam_0001",
	        "latitude" : 75.401451,
	        "longitude" : -95.722518,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0002",
	        "latitude" : 75.027715,
	        "longitude" : -96.041859,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0003",
	        "latitude" : 74.996653,
	        "longitude" : -96.601780,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0004",
	        "latitude" : 75.247701,
	        "longitude" : -96.074436,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0005",
	        "latitude" : 75.044926,
	        "longitude" : -93.651951,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0006",
	        "latitude" : 75.601571,
	        "longitude" : -95.294407,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0007",
	        "latitude" : 74.763102,
	        "longitude" : -95.091160,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0008",
	        "latitude" : 75.473988,
	        "longitude" : -94.069432,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0009",
	        "latitude" : 75.232307,
	        "longitude" : -96.277683,
	        "key" : ""
	    },
	    {
	        "deviceId" : "polar_cam_0010",
	        "latitude" : 74.658811,
	        "longitude" : -93.783787,
	        "key" : ""
	    }
	]
	```

	This file defines ten virtual cameras that will transmit events to the IoT hub. Each "camera" contains a device ID, a latitude and a longitude specifying the camera's location, and an access key for per-device authentication. The ```key``` values are empty for now, but that will change once the cameras are registered with the IoT hub.

	> The latitudes and longitudes are for points on the coast of Northern Canada's [Cornwallis Island](https://en.wikipedia.org/wiki/Cornwallis_Island_(Nunavut)), which is one of the best sites in Canada to spot polar bears. It is also adjacent to [Bathurst Island](https://en.wikipedia.org/wiki/Bathurst_Island_(Nunavut)), which is home to the [Polar Bear Pass National Wildlife Area](https://www.canada.ca/en/environment-climate-change/services/national-wildlife-areas/locations/polar-bear-pass.html).

4. Create another Visual Studio Code file. Enter the following JavaScript code. Save the file to **C:\\SkillMeUp** (**File** > **Save**) and name it **deploy.js**. 

	```javascript
	var fs = require('fs');
	var iothub = require('azure-iothub');
	var registry = iothub.Registry.fromConnectionString('CONNECTION_STRING');
	
	console.log('Reading devices.json...');
	var devices = JSON.parse(fs.readFileSync('devices.json', 'utf8'));
	
	console.log('Registering devices...');
	registry.addDevices(devices, (err, info, res) => {
	    registry.list((err, info, res) => {
	        info.forEach(device => {
	            devices.find(o => o.deviceId === device.deviceId).key = device.authentication.symmetricKey.primaryKey;          
	        });
	
	        console.log('Writing cameras.json...');
	        fs.writeFileSync('cameras.json', JSON.stringify(devices, null, 4), 'utf8');
	        console.log('Done');
	    });
	});
	```

	This code registers all the simulated devices defined in **devices.json** with the IoT hub that you created earlier. It also retrieves from the IoT hub the access key created for each device and creates a new file named **cameras.json** that contains the same information as **devices.json**, but with a value assigned to each device's ```key``` property. It is this key, which is transmitted in each request, that enables a device to authenticate to the IoT hub.

5. Replace **CONNECTION_STRING** on line 3 of **deploy.js** with the connection string of your IoT Hub that you noted in Notepad. Then save the file.

6. Return to the Command Prompt and execute the following command to run **deploy.js**. Confirm that the output looks like the below image. 

	```
	node deploy.js
	```

	![](images/DeployJSOutput.png)

7. Verify that a file named **cameras.json** was created in **C:\\SkillMeUp**. Open the file and view its contents in Visual Studio Code. Confirm that the ```key``` properties which are empty strings in **devices.json** have values in **cameras.json**.

	![](images/CameraKeyValues.png)

## Task 4: Test the simulated camera array

In this task, you will write code to test the camera array that you deployed in the previous task. That code will transmit an event from one of the virtual cameras to the IoT hub, and it will upload an image to the storage account that you created in Task 1.

1. Create a new folder named **photos** in **C:\\SkillMeUp**. Then copy all 30 JPG files from **C:\\SkillMeUp\\CameraSimImages** to the **photos** directory. These are the images that the simulated cameras will upload to blob storage, samples of which are shown below. Wildlife depicted in the images include Arctic foxes, polar bears, and walruses.

	![](images/wildlife-images.png)

2. In the Command Prompt with C:\\SkillMeUp as the current directory, execute the following command to install the [Microsoft Azure Storage SDK for Node.js](https://www.npmjs.com/package/azure-storage):

	```
	npm install azure-storage --save
	```

	This package provides a programmatic interface to Azure storage, including blob storage.

3. Create another Visual Studio Code file. Enter the following code and save it to **C:\\SkillMeUp** with the name **test.js**. 

	```javascript
	var iotHubName = 'HUB_NAME';
	var storageAccountName = 'ACCOUNT_NAME';
	var storageAccountKey = 'ACCOUNT_KEY';
	
	// Upload an image to blob storage
	var azure = require('azure-storage');
	var blobService = azure.createBlobService(storageAccountName, storageAccountKey);
	
	blobService.createBlockBlobFromLocalFile('photos', 'image_19.jpg', 'photos/image_19.jpg', (err, result, response) => {
	    if (err) {
	        console.log('Error uploading blob: ' + err);
	    }
	    else {
	        console.log("Blob uploaded");
	
	        // Get information about polar_cam_0003 from cameras.js
	        var fs = require('fs');
	        var cameras = JSON.parse(fs.readFileSync('cameras.json', 'utf8'));
	        var camera = cameras.find(o => o.deviceId === 'polar_cam_0003');
	
	        // Send an event to the IoT hub and include the blob's URL
	        var Message = require('azure-iot-device').Message;
	        var connectionString = 'HostName=' + iotHubName + '.azure-devices.net;DeviceId=' + camera.deviceId + ';SharedAccessKey=' + camera.key;
	        var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
	        var client = clientFromConnectionString(connectionString);
	
	        client.open(err => {
	            if (err) {
	                console.log('Error connecting to IoT hub: ' + err);
	            }
	            else {
	                var data = {
	                    'deviceId' : camera.deviceId,
	                    'latitude' : camera.latitude,
	                    'longitude' : camera.longitude,
	                    'url' : 'https://' + storageAccountName + '.blob.core.windows.net/photos/image_19.jpg',
	                    'timestamp' : new Date().toISOString()
	                };
	
	                var message = new Message(JSON.stringify(data));
	
	                client.sendEvent(message, (err, result) => {
	                    if (err) {
	                        console.log('Error sending event: ' + err);
	                    }
	                    else {
	                        console.log("Event transmitted");                
	                    }
	                });
	            }
	        });
	    }
	});
	```

	This code uploads the file named **image_19.jpg** from the current directory's "photos" subdirectory to the storage account's "photos" container. Then it opens a connection from ```polar_cam_0003``` to the IoT hub using ```polar_cam_0003```'s access key (which comes from **cameras.js**) and transmits a message containing a JSON payload over MQTT. That message includes a camera ID, a latitude and longitude, the URL of the blob that was uploaded, and the event time.

4. Replace HUB_NAME on line 1 of **test.js** with the name of the IoT hub you created in Task 2, and ACCOUNT_NAME on line 2 with the name of the storage account that you created in Task 1. If necessary, you can find these values by navigating to the **streaminglab-rg** resource group in the Azure Portal. Save the file. 

5. Return to the browser tab with the cloud shell (you may have to click **Reconnect**) and use the following command to list the access keys for the storage account, replacing ACCOUNT_NAME with the name of your storage account:

	```
	az storage account keys list --account-name ACCOUNT_NAME --resource-group streaminglab-rg
	```

	By default, blobs stored in an Azure storage account are private and are only accessible to persons who have access to the subscription under which the account was created. Access keys allow other parties, including apps, to access the contents of a storage account. You should treat access keys with great care and **never** give them to someone you don't trust.

6. Copy the ```value``` property of either of the two keys that is displayed in the command output to the clipboard. Make note of it in Notepad then replace ACCOUNT_KEY on line 3 of **test.js** with the access key you copied. That line should now look something like this:

	```javascript
	"value": "doPZd+uLueiDMY0JWtg...qWtWfmJLVkTe/huqlTliq8ruy8L1lzmDV9l6HkRw=="
	```

7. Save **test.js** then run it in the command prompt with the following command:

	```
	node test.js
	```

	Confirm that you see the following output indicating that the command completed successfully:

	![](images/TestUploadOutput.png)

8. Open a browser tab then navigate and log into the Azure Portal (https://portal.azure.com) with the credentials in the cloud icon if you haven't already. 

9. Expand the portal's left navigation by clicking **Show portal menu** in the top left then click **Resource groups**. Click the **streaminglab-rg** resource group to view its contents.

	![](images/open-resource-group.png)

10. Click the storage account that you created in Task 1.

	![](images/open-storage-account.png)

11. In the blade for the storage account, click **Containers** to view a list of blob containers.

    ![](images/open-blob-storage.png)

12. Click **photos** to open the "photos" container.

	![](images/open-container.png)

13. Click the blob named **image_19.jpg**.

	![](images/open-blob.png)

14. Click **Download** to download the blob. Open it at the download prompt and confirm that it contains a small (64x64) grayscale polar-bear image.

	![](images/download-blob.png)

15. Return to the **streaminglab-rg** resource group in the portal and click the IoT hub.

	![](images/open-iot-hub.png)

16. Scroll down and confirm that at least one message has been received by the IoT hub, and that it has 10 devices registered. These are the simulated cameras that you registered in the previous exercise. 

	![](images/2018-10-19-17-29-40.png)

If you would like to see a list of devices registered with the IoT hub, click **IoT Devices** in the menu on the left. If you then click one of the devices, you will find that individual devices can be enabled and disabled through the portal. Devices can also be enabled and disabled using the Azure CLI.

## Summary

In this exercise, you created an Azure IoT hub, registered an array of simulated devices, and sent a message to the IoT hub from one of the devices to confirm that everything was set up correctly. You also created an Azure storage account and a blob container inside it and demonstrated that a device simulated in software could upload blobs to it.

---

Copyright 2018 Microsoft Corporation. All rights reserved. Except where otherwise noted, these materials are licensed under the terms of the MIT License. You may use them according to the license as is most appropriate for your project. The terms of this license can be found at https://opensource.org/licenses/MIT.
