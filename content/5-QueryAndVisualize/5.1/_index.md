+++
title = "Athena and QuickSight"
type = ""
weight = 1
pre = "5.1. "
+++

#### Steps
- Prerequisites
- Getting Started
- Query Data with Amazon Athena
- Build an Amazon QuickSight Dashboard
- Set up QuickSight
- Create QuickSight Charts
- Create QuickSight Parameters
- Create a QuickSight Filter

#### Prerequisites
> [!NOTE]
> [Ingestion with Glue](3-Ingestion/) and [Transforming data with Glue ETL](4-Transforming/) labs are prerequisites for this lab.

#### Getting Started
In this lab, you will complete the following tasks:

1. Query data and create a view with Amazon Athena
2. Athena Workgroups to Control Query Access and Costs
3. Build a dashboard with Amazon QuickSight

#### Query Data with Amazon Athena
1. In the AWS services console, search for **Athena**.
![image1](/image/5.AthenaAndQuicksight/001-AthenaService.png)

2. In the **Query Editor**, select your newly created database e.g., **"ticketdata”**.

3. Click the table named **"parquet_sporting_event_ticket"** to inspect the fields. **Note:** The type for fields **id, sporting_event_id** and **ticketholder_id** should be **(double)**.
![image2](/image/5.AthenaAndQuicksight/002-QueryEditorDB.png)
Next, we will query across tables parquet_sporting_event, parquet_sport_team, and parquet_sport location.

4. Copy the following SQL syntax into the Query 1 tab and click **Run**.
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT
    e.id AS event_id,
    e.sport_type_name AS sport,
    e.start_date_time AS event_date_time,
    h.name AS home_team,
    a.name AS away_team,
    l.name AS location,
    l.city
FROM parquet_sporting_event e,
    parquet_sport_team h,
    parquet_sport_team a,
    parquet_sport_location l
WHERE
    e.home_team_id = h.id
    AND e.away_team_id = a.id
    AND e.location_id = l.id;
{{< / highlight >}}

- The results appear beneath the query window.
![image2](/image/5.AthenaAndQuicksight/004-Query1.png)

5. As shown above Click **Create** and then select **View from query**

6. Name the view sporting_event_info and click **Create**
![image2](/image/5.AthenaAndQuicksight/005-Query1CreateView.png)
Your new view is created
![image2](/image/5.AthenaAndQuicksight/006-CreatedViewQuery1.png)

7. Copy the following SQL syntax into the **Query 3 tab**
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT t.id AS ticket_id,
    e.event_id,
    e.sport,
    e.event_date_time,
    e.home_team,
    e.away_team,
    e.location,
    e.city,
    t.seat_level,
    t.seat_section,
    t.seat_row,
    t.seat,
    t.ticket_price,
    p.full_name AS ticketholder
FROM sporting_event_info e,
    parquet_sporting_event_ticket t,
    parquet_person p
WHERE
    t.sporting_event_id = e.event_id
    AND t.ticketholder_id = p.id
{{< / highlight >}}

8. Click on **Save as** button Give this query a name: *create_view_sporting_event_ticket_info* and some description and then, click on **Save query**.
![image2](/image/5.AthenaAndQuicksight/007-CreateNewQuerySaveAs.png)
- Back to the query editor, you will see the query name changed. Now, click on **Run**
- The results appear beneath the query window.
![image2](/image/5.AthenaAndQuicksight/008-RunQuery2.png)

9. As shown above, click **View from query.**

10.  Name the view sporting_event_ticket_info and click **Create.**
![image2](/image/5.AthenaAndQuicksight/009-Query2CreateView.png)

11.  Copy the following SQL syntax into the Query 5 tab.
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT
    sport,
    count(distinct location) as locations,
    count(distinct event_id) as events,
    count(*) as tickets,
    avg(ticket_price) as avg_ticket_price
FROM sporting_event_ticket_info
GROUP BY 1
ORDER BY 1;
{{< / highlight >}}

- Click on **Save as** and give this query name: *analytics_sporting_event_ticket_info* and some description and then, click on **Save**.
![image2](/image/5.AthenaAndQuicksight/010-CreateNewQuery3SaveAs.png)

- The name of the New Query 5 will be changed to one assigned in previous step. Click on **Run**.
- You query returns two results in approximately five seconds. The query scans 25 MB of data, which prior to converting to parquet, would have been 1.59GB of CSV files.
![image2](/image/5.AthenaAndQuicksight/011-RunQuery3.png)

- The purpose of saving the queries is to have clear distinction between the results of the queries running on one view. Otherwise, your query results will be saved under “Unsaved” folder within the S3 bucket location provided to Athena to store query results. Please navigate to S3 bucket to observe these changes, as shown below:
![image2](/image/5.AthenaAndQuicksight/012-OutputAthenaS3Results.png)

#### Build an Amazon QuickSight Dashboard
##### Set up QuickSight
1. In the AWS services console, search for **QuickSight**.
![image2](/image/5.AthenaAndQuicksight/013-QuicksightService.png)
If this is the first time you have used QuickSight, you are prompted to create an account.

2. Click **Sign up for QuickSight**.
3. For account type, choose the default **Enterprise** Version.
4. Click **Continue**.
5. On the Create your QuickSight account page, select *No, Maybe Later* for the Paginated Report add-on. On the new screen, leave the default selection for authentication method.
6. Next choose the appropriate AWS region based on where you are running this workshop on and the check boxes to enable auto discovery, Amazon Athena, and Amazon S3. For QuickSight account name give a unique name (e.g., quicksight-lab-<initals>-<randomstring>) and email address.
7. Select your DMS bucket (e.g., "xxx-dmslabs3bucket-xxx"), Click **Finish**.
![image2](/image/5.AthenaAndQuicksight/014-SingUpQuicksight.png)
![image2](/image/5.AthenaAndQuicksight/015-SingUpQuicksight2.png)
![image2](/image/5.AthenaAndQuicksight/016-SelectedS3Bucket.png)
Please click **Finish**

8. On the top right corner, click **New analysis**.
![image2](/image/5.AthenaAndQuicksight/017-QuicksightStep1.png)

9. Click **New dataset**.
![image2](/image/5.AthenaAndQuicksight/018-QuicksightStep2.png)

10.  On the **Create a Dataset** page, select **Athena** as the data source.
![image2](/image/5.AthenaAndQuicksight/019-QuicksightStep3.png)

11.  For Data source name, type *ticketdata-qs* , then click **Validate connection**.
12.  Click **Create data source**.
![image2](/image/5.AthenaAndQuicksight/020-QuicksightStep4.png)

13.  In the Database drop-down list, select the database **ticketdata**.

14.  Choose the "**sporting_event_ticket_info**" table and click **Select**.
![image2](/image/5.AthenaAndQuicksight/021-QuicksightStep5.png)

15.  To finish data set creation, choose the option **Import to SPICE for quicker analytics** and click **Visualize**. If your SPICE has 0 bytes available, choose the second choice Directly query your data.
![image2](/image/5.AthenaAndQuicksight/022-QuicksightStep6.png)

##### Create QuickSight Charts
In this section we will take you through some of the different chart types.

1. In the Fields list, click the **ticket_price** column to populate the chart.

2. Click the **expand icon** in corner of "ticket_price" field, and select **Show as Currency** to show the number in dollar value.
![image2](/image/5.AthenaAndQuicksight/023-QSDashboard1.png)

3. You can **add visual** by clicking **Add button** from the Visuals pane of the screen.
- In the **Visual types** area, choose the **Vertical bar chart** icon.
- This layout requires a value for the X-axis. In Fields list, select the **event_date_time** field and you should see the visualization update.
- For Value **Y-axis**, select **“ticket_price”** from the Field list.
![image2](/image/5.AthenaAndQuicksight/024-QSDashboard2.png)

4. You can drag and move other visuals to adjust space in dashboard. In the Fields list, click and drag the **seat_level** field to the **Group/Color** box. You can also use the slider below the x axis to fit all of the data.
![image2](/image/5.AthenaAndQuicksight/025-QSDashboard3.png)
Let's build on this one step further by changing the chart type:

5. In the Visuals pane, click **Vertical bar chart** under change visual type and choose the **Clustered bar combo chart** icon.

6. In the Fields list, click and drag the **ticketholder** field to the Lines box.

7. In the Lines box, click the dropdown box and choose **Aggregate: Count Distinct** for Aggregate. You can then see the y-axis update on the right-hand side.
![image2](/image/5.AthenaAndQuicksight/026-QSDashboard4.png)

8. Click on insight icon on the Tool bar and explore insight information in simple English.
![image2](/image/5.AthenaAndQuicksight/027-QSDashboard5.png)
Feel free to experiment with other chart types and different fields to get a sense of the data.

##### Create QuickSight Parameters
In the next section we are going to create some parameters with controls for the dashboard, then assign these to a filter for all the visuals.
1. From the Tool bar, select **Parameters** icon.
![image2](/image/5.AthenaAndQuicksight/028-ParameterIcon.png)

2. Click **Add** to create a new parameter with a Name.

3. For Name, type *EventFrom*.

4. For Data type, choose **Datetime**.

5. For Time granularity, set **Hour**.

6. For Default value, select the value from calendar as start date available in your graph for **event_date_time**. For example, *2021-01-01 00:00*.

7. Click **Create**, and then **close** the Parameter Added dialog box.
![image2](/image/5.AthenaAndQuicksight/029-CreateEventFrom.png)

8. Create another parameter with the following attributes:
- **Name:** *EventTo*
- **Data type:** *Datetime*
- For **Time granularity**, set **Hour**.
- For Default value, select the value from calendar as end date available in your graph for **event_date_time**. For example, *2022-01-01 00:00*
- Click **Create**
![image2](/image/5.AthenaAndQuicksight/030-CreateEventTo.png)

9. In next window, you can select any option to perform any operation with the parameter. Alternatively, you can click the three dots for the **EventFrom parameter** and choose **Add control**.
![image2](/image/5.AthenaAndQuicksight/031-AddControl.png)

10. For Display name, specify **Event From** and click **Add**.
![image2](/image/5.AthenaAndQuicksight/032-AddDateControl.png)

11. Repeat the process to add a control for *EventTo* with display name *Event To*
![image2](/image/5.AthenaAndQuicksight/033-AddDateControl.png)
You should now be able to see and expand the Controls section above the chart.
![image2](/image/5.AthenaAndQuicksight/034-ParametersControls.png)

##### Create a QuickSight Filter
To complete the process, we will wire up a filter to these controls for all visuals.

1. From the Tool bar, choose **Filter**.

2. Click the plus icon (+) to add a filter for the field **event_date_time**.
![image2](/image/5.AthenaAndQuicksight/035-QSFilter.png)

3. Click this filter to **edit** the properties.

4. Select **All applicable visuals** in the **Applied To** dropdown

5. For Filter type, choose **Date & Time range** and Condition as **Between**.

6. Select option **Use Parameter**, click **Yes** to apply to all visual.

7. For **Start date parameter**, choose **EventFrom**.

8. For **End date parameter**, choose **EventTo**.
![image2](/image/5.AthenaAndQuicksight/036-EditFilter.png)

9. Click **Apply**.

You can now click on **Publish** on the top right corner of screen to publish your visuals. You can also, share it by clicking on the **Share** menu.

A dashboard is a read-only snapshot of an analysis that you can share with other Amazon QuickSight users for reporting purposes. In Dashboard other users can still play with visuals and data but that will not modify dataset.

You can share an analysis with one or more other users with whom you want to collaborate on creating visuals. Analysis provides other uses to write and modify data set.