## Exercise 5: Visualizing Data with Power BI

The goal of business intelligence is being able to quickly identify and respond to ever-changing trends in business and industry. Whether you’re a data analyst delivering reports and analytics to your organization or a stakeholder or decision-maker in need of critical insights, Power BI can organize and unify all of your organization's data to provide a clear view of the world around you.

[Microsoft Power BI](https://powerbi.microsoft.com) was created to address the data explosion in commercial and academic organizations, the need to analyze that data, and the need for rich, interactive visuals to represent the data and reveal key insights. It contains a suite of tools that assist in data analysis, from data discovery and collection to data transformation, aggregation, sharing, and collaboration. Moreover, it allows you to create rich visualizations and package them in interactive dashboards.

In this exercise, you will connect Microsoft Power BI to the Azure SQL database you created in the previous exercise to capture information emanating from the virtual camera array you deployed in the Arctic. Then you will use Power BI to build a report that shows in near real-time where polar bears are being spotted.

![](images/road-map-4.png)

### Objectives

In this exercise, you will learn how to:

- Connect Power BI to an Azure SQL database

- Use Power BI to visualize data written to the database

- Create a report that can be shared with colleagues

### Time Estimate

- 40 minutes

## Task 1: Connect Power BI to Azure SQL

In the previous exercise, you used the [Custom Vision Service](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) to train an image-classification model to differentiate between different types of Arctic wildlife, and modified the Azure Function you wrote to output the results to an Azure SQL database. The first step in using Microsoft Power BI to explore and visualize this data is connecting it to Power BI as a data source. In this task, you will connect Power BI to the Azure SQL database.

1. Launch **Power BI Desktop** via the desktop shortcut.

2. On the Power BI Desktop dialogue, click **Get data**.

	![](images/2019-10-20-20-08-13.png)

3. On the **Get Data** dialogue, select **Azure** then **Azure SQL database** and click **Connect**.

	![](images/2019-10-20-20-12-09.png)

4. Enter the server's host name (the server name you have noted with ".database.windows.net" added to the end since it's an Azure SQL database server that can be found in the Portal) and **PolarBearDB** for the database name. Select **DirectQuery** for the connectivity mode. Expand the **Advanced Options** section, then enter the below query into the **SQL Statement** box to select the 20 most recently added rows in the "PolarBears" table. This is the query that the report will use to pull information from the database. When you're done, click **OK**.

	```sql
	SELECT TOP 20 Id, CameraId, Latitude, Longitude, Url, Timestamp, FORMAT(Timestamp,'MM/dd/yyyy h:mm:ss tt') AS TimestampLabel, IsPolarBear FROM dbo.PolarBears ORDER BY Timestamp DESC
	```

    ![](images/2019-10-20-20-19-40.png)

5. On the SQL Server database dialog, select **Database** on the left. Enter **demouser** for the user name and **Demo@pass123** for the password. Then click **Connect**.

    ![](images/2019-10-20-20-23-55.png)

6. When prompted, click **Load** to load the data into Power BI.

After a short delay, Power BI will connect to the database and import a dataset using the query you provided. The next step is to create a report using that dataset.

## Task 2: Build a report in Power BI

Visualizations are the primary element that make up Power BI reports. In this task, you will use the Power BI report designer to create visuals from the database you connected to in the previous task, adjust filters and aggregates to refine the way the data is displayed, and format the visuals to produce compelling output.

1. On the Power BI Desktop canvas, click the **Map** icon in the **Visualizations** panel on the right to add a map visual to the report.
	
	![](images/2019-10-20-20-32-55.png)

2. Check the **Latitude** and **Longitude** boxes in the **Fields** panel on the right to include these fields in the visual.
	
	![](images/2019-10-20-20-33-48.png)

3. In the **Visualizations** panel, click the down arrow next to **Average of Latitude** and select **Don't summarize**. Then do the same for **Average of Longitude**.

	![](images/2019-10-20-20-35-03.png)

4. In the **Fields** panel, check the **IsPolarBear** box to add that field to the map. Then resize the map so that it looks something like this:

	![Resizing the map](images/map-visual.png)

	Note that the number and location of the "bubbles" in your map will probably be different than what's shown here.

5. Click in the empty area outside the map to deselect it. Then check the **CameraId**, **IsPolarBear**, and **TimestampLabel** boxes in the **Fields** panel to add a table visual containing those columns to the report.

	![Adding a table visual](images/table-visual-1.png)

6. Deselect the table visual. Then check **IsPolarBear** and **Latitude** in the **Fields** panel to add another table visual, and click the **Pie Chart** icon in the **Visualizations** panel to convert the table into a pie chart. 

	![](images/2019-10-20-20-38-21.png)

7. Click the down arrow next to **Average of Latitude** and select **Count** from the menu to configure the pie chart to show a count of sightings and the proportion of sightings in which polar bears were detected. 

	![Refining the pie-chart visual](images/pie-chart-visual.png)

8. Deselect the pie-chart visual and click the **Slicer** icon to add a slicer to the report. Slicers provide a convenient means for filtering information in a Power BI report by narrowing the data shown in other visuals.

	![](images/2019-10-20-20-50-36.png)

9. Check the **IsPolarBear** box in the **Fields** panel so the slicer shows checkboxes labeled "True" and "False." 

	![Refining the slicer](images/slicer-visual.png)

10. Now resize and reposition the visuals to achieve a layout similar to this:

	![Adjusting the layout](images/layout.png)

11. With the report structure in place, the next task is to use some of Power BI's rich formatting options to embellish the visuals. Start by selecting the map visual in the report designer. Then click the **Format** icon in the **Visualizations** panel.

	![](images/2019-10-20-20-51-43.png)

12. Use the formatting controls in the **Visualizations** panel to make the following changes to the map visual:

	- Under **Legend** set **Legend Name** to **Polar bear sighted?**
  
	- Under **Data colors** set the color for **False** to 00FF00 (pure green) by clicking **Custom color** and entering **00FF00**. Set the color for **True** to FF0000 (pure red) using the same procedure. 
  
	- Under **Bubbles** set the bubble size to **30**
  
	- Under **Map styles** set the theme to **Aerial**
  
	- Turn **Title** from on to **off**

	Confirm that the resulting map looks something like this:

	![Formatted map visual](images/formatted-map-visual.png)

13. Select the pie-chart visual and use the formatting controls in the **Visualizations** panel to make the following changes:

	- Under **Data colors** set **False** to 00FF00 and **True** to FF0000
  
	- Under **Detail  labels** change the label style to **Data value, percent of total**
  
	- Under **Title** change the title text to **Proportion of polar bear sightings**

	Confirm that the resulting pie-chart visual resembles this:

	![Formatted pie-chart visual](images/formatted-pie-chart-visual.png)

14. Select the table visual and use the formatting controls in the **Visualizations** panel to make the following changes:

	- Under **Style** change the style to **Alternating rows**
  
	- Turn **Title** on, and change the title text to **Camera activity** 

	Confirm that the resulting table looks like this:

	![Formatted table visual](images/formatted-table-visual.png)

15. Select the slicer visual and use the formatting controls in the **Visualizations** panel to make the following changes:

	- Under **Selection Controls** set **Single Select** to **off**
  
	- Turn **Slicer header** off
  
	- Turn **Title** on and set the title text to **Show sightings that are:**

16. Double-click **Page 1** in the bottom-left corner of the designer and change the report title to **Polar Bear Activity**.

17. Save the report by clicking the **Save** icon in the upper-left corner of the page. Save it to **C:\\SkillMeUp** and enter **Polar Bear Activity** for the name. 

The formatted report should resemble the one below. Feel free to embellish it further. You could, for example, add a title in a large font at the top of the report. Once you're satisfied with the layout and content, it's time to put it to work using a live data source.

![The formatted report](images/formatted-report.png)

## Task 3: Run the end-to-end solution

Now that the report is prepared in Power BI, your final task is to run the end-to-end solution that you built in this series of hands-on labs and check for polar bears!

1. Open the PolarBearDB database in the [Azure Portal](https://portal.azure.com) and open the query editor on the left. Execute the following query to delete all rows from the "PolarBears" table:

	```sql
	DELETE FROM dbo.PolarBears
	```

2. Open the Stream Analytics job that you created in Exercise 2 and start the job again by clicking **Start**, then **Start** again on the dialog that appears on the right.

3. Wait until the job is running. In the command prompt, run the following command to start running the virtual cameras. Make sure you are in the **C:\\SkillMeUp** directory. 

	```
	node run.js
	```

4. Return to the Power BI report click **Refresh** at the top of the page. Then refresh it again every 15 seconds or so. The report will refresh automatically every 15 minutes, but you can refresh it manually as often as you would like to update the visuals. 

	![](images/2019-10-20-20-58-35.png)

5. Confirm that red and green bubbles appear at various locations around the island. Red bubbles indicate the presence of polar bears, while green bubbles represent locations where photos were taken but no polar bears were detected. Locations that have a mixture of sightings will show red *and* green, as pictured below.

	![There be polar bears!](images/report-in-action.png)
	_There be polar bears!_

6. Suppose you *only* wanted to show locations where polar bears were detected. Check the **True** box in the slicer visual. What happens in the map?

7. Continue running for a few minutes and refreshing the report once or twice a minute. Confirm that there are polar bears active on the island!

8. When you're satisfied that the solution is working as intended, stop the cameras by hitting **CTRL + c** in the command prompt and stop the Stream Analytics job by clicking **Stop** on it's page in the Azure portal.

You now have a report that shows, in near real-time, polar-bear activity on the island. If you have a [Power BI Pro or Power BI Premium](https://powerbi.microsoft.com/blog/power-bi-pro-power-bi-premium-flexibility-to-choose-the-licensing-best-for-you-and-your-organization/) account, you can share the report with other Power BI users so they, too, can monitor polar-bear activity. For more information about sharing your work, see [Share Power BI Dashboards and Reports](https://docs.microsoft.com/en-us/power-bi/service-how-to-collaborate-distribute-dashboards-reports "Share Power BI Dashboards and Reports"). Reports can also be viewed in the [Power BI mobile apps](https://docs.microsoft.com/power-bi/mobile-apps-for-mobile-devices) so you can keep track of polar bears on your Windows, Android, or iOS device — even on an Apple watch!


## Summary

In this lab, you built a solution that feeds data from a simulated array of cameras into an [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/), uploads photographs to [Azure Storage](https://azure.microsoft.com/services/storage/?v=16.50), processes the data using [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), analyzes the photographs using the [Custom Vision Service](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/), and visualizes the output using [Microsoft Power BI](https://powerbi.microsoft.com/). You also got first-hand experience using [Azure Functions](https://azure.microsoft.com/services/functions/) and [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). It's a sophisticated solution, and one that has applications in the real world.

Once you're finished using the solution, you should delete all the Azure services you deployed so they no longer charge to your subscription. To delete them, simply go to the Azure Portal and delete the "streaminglab-rg" resource group. That's one of the many benefits of using resource groups: one simple action deletes the resource group and everything inside it. Once deleted, a resource group cannot be recovered, so make sure you're finished with it before deleting it.

---

Copyright 2018 Microsoft Corporation. All rights reserved. Except where otherwise noted, these materials are licensed under the terms of the MIT License. You may use them according to the license as is most appropriate for your project. The terms of this license can be found at https://opensource.org/licenses/MIT.
