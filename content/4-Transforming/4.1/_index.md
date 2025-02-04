+++
title = "Data Validation and ETL With Glue"
type = ""
weight = 1
pre = "4.1. "
+++

#### PART A: Create Glue Crawler for initial full load data
1. Navigate to the [AWS Glue Console](https://console.aws.amazon.com/glue/home)
![image1](../../image/4.Transforming/001-GlueService.png)

2. On the AWS Glue menu, under 'Data Catalog', select **Crawlers**.
![image2](../../image/4.Transforming/002-GlueCreateCrawler.png)

3. Click Create crawler.
4. Enter ```glue-lab-crawler``` as the crawler name for initial data load.
5. Optionally, enter the description. This should also be descriptive and easily recognized and Click **Next**.
![image3](../../image/4.Transforming/003-GlueCreateCrawlerName.png)

6. Under **Choose data sources and classifiers** select **Not yet** and click **Add a data source**
![image4](../../image/4.Transforming/004-GlueCreateCrawlerStep2.png)

7.On the **Add a data store** page, make the following selections:
- For **Data source**, click the drop-down box and select **S3**.
- For **Location of S3 data**, select **In this account**
- For **S3 path**, browse to the target folder stored CSV files, e.g., **s3://ingestion-data-skip-dms-lab/tickets/**
- Leave all other parameters as default
- Click **Add an S3 data source**

![image5](../../image/4.Transforming/005-CrawlerAddS3BucketSource.png)

8. Click **Next**
![image6](../../image/4.Transforming/006-GlueCreateCrawlerStep2.png)

9. On the Configure security settings page, make the following selection:
- Under Existing IAM role select **AWSGlueServiceRoleDefault** which is pre-created for this lab.
- Information about **AWSGlueServiceRoleDefault**
![image8](../../image/4.Transforming/008-GlueRole.png)

10. Click **Next**
![image7](../../image/4.Transforming/007-AddGlueRole.png)

11. On the **Set output and scheduling**, under **Target database**, click **Add database** which pops-up another tab.
12. Enter ```ticketdata``` as your database name and click **Create database**
![image9](../../image/4.Transforming/009-CreateDB.png)

![image10](../../image/4.Transforming/010-Database.png)

13. Please get back to the **Set output and scheduling** page
- Select **Target database** as 'ticketdata'. Please use the refresh option right next to the dropdown to make sure that the new database we created is listing there.
- For **Prefix added to tables (optional)**, leave the field empty.
- Under **Crawler schedule** as select the frequency as 'On demand'
- Click **Next**
![image11](../../image/4.Transforming/011-AdvancedSettings.png)
![image12](../../image/4.Transforming/012-CrawlerSchedule.png)

14. Review the information and click **Create crawler**. The crawler is now ready to run.
![image13](../../image/4.Transforming/013-Reviews.png)

15. Execute the Crawler by clicking **Run crawler** button.
![image14](../../image/4.Transforming/014-SuccessCreateCrawler.png)
Crawler will change status from 'Running' to 'Stopping', wait until crawler comes back to 'Ready' state (the process will take a few minutes), you can see that it has created 15 tables.
![image15](../../image/4.Transforming/015-SuccessRunCrawler.png)

16. In the AWS Glue navigation pane, under **Data Catalog** click **Databases → Tables**. You can also click the **ticketdata database** to browse the tables.
![image16](../../image/4.Transforming/016-CheckDB.png)

#### PART B: Data Validation Exercise
1. Within the Tables section of your **ticketdata database**, click the **person** table.
- You may have noticed that some tables (such as **person**) have column headers such as col0,col1,col2,col3. In absence of headers or when the crawler cannot determine the header type, default column headers are specified.
- This exercise uses the **person** table in an example of how to resolve this issue.

2. Click **Edit Schema** option
![image17](../../image/4.Transforming/017-EditSchemaPersonTable.png)

3. In the **Edit Schema** section, select the row for **col0** (column name) and click **Edit**.
- Type id as the column name and click **Save**.
- Repeat the above steps to change the remaining column names to match those shown in the following figure: *full_name, last_name and first_name.*
![image18](../../image/4.Transforming/018-EditSchemaPersonTable.png)

4. Click **Save as new table version**.

#### PART C: Data ETL Exercise
Pre-requisite: To store processed data in parquet format, we need a new folder location for each table, eg. the full path for sport_team table look like this - s3://<s3_bucket_name>/tickets/dms_parquet/sport_team

1. In the left navigation pane, click **ETL jobs**.
2. Click **“Visual ETL”**
![image19](../../image/4.Transforming/019-ETLJob.png)

3. Select **Amazon S3** from the Sources list to add a **Data source - S3 bucket** node.
![image20](../../image/4.Transforming/020-ETLJobStep1.png)

4. Select the **Data source - S3 bucket** node to show the data source properties.
5. In the panel on the right under “Data source properties - S3”, select **Data Catalog table** and choose the ticketdata database from the drop down. For Table, select the sport_team table.
![image21](../../image/4.Transforming/021-ETLJobStep2.png)

6. Click on the + button and select Change Schema from the Transforms list to add a **Transform - Change Schema** node.
![image22](../../image/4.Transforming/022-ETLJobStep3.png)

7. Select the **Transform - Change Schema** node to show the properties. In the Transform panel on the right, change the data type of **id** column to *double* in the dropdown.
![image23](../../image/4.Transforming/023-ETLJobStep4.png)

8. Click on the + button and select **Amazon S3** from the Targets list to add a **Data target - S3 bucket** node.
![image24](../../image/4.Transforming/024-ETLJobStep5.png)

9. Select the **Data target - S3 bucket** node to show the properties. In the panel on the right, change the Format to **Parquet** in the dropdown. Under **Compression Type**, select *Uncompressed* from the dropdown.

10. Under “**S3 Target Location**”, select “**Browse S3**” browse to the “**ingestion-data-skip-dms-lab**” bucket, select “**tickets**” item and press “**Choose**”.

11. In the textbox, append **dms_parquet/sport_team/** to the S3 url. The path should look similar to s3://ingestion-data-skip-dms-lab/tickets/dms_parquet/sport_team/ - don’t forget the / at the end. The job will automatically create the folder.
![image25](../../image/4.Transforming/025-ETLJobStep6.png)

12. Finally, select the **Job details** tab at the top. Enter ```Glue-Lab-SportTeamParquet``` under Name.

13. For IAM Role, select the role named similar to AWSGlueServiceRoleDefault.
![image26](../../image/4.Transforming/026-ETLJobStep7.png)

14. Scroll down the page and under **Job bookmark**, select **Disable** in the drop down. You can try out the bookmark functionality later in this lab.

15. Press the **Save** button in the top right-hand corner to create the job.

16. Once you see the **Successfully created job** message in the banner, click the **Run** button to start the job.
    
17. Select **Jobs** from the navigation panel on the left-hand side to see a list of your jobs.

18. Select **Monitoring** from the navigation panel on the left-hand side to view your running jobs, success/failure rates and various other statistics.

19. Scroll down to the **Job runs** list to verify that the ETL job has completed successfully. This should take about 1 minute to complete.
![image27](../../image/4.Transforming/027-RunETLJob.png)

20. We need to repeat this process for an additional 4 jobs, to transform the **sport_location, sporting_event, sporting_event_ticket** and **person tables**.
- During this process, we will need to modify different column data types. We can either repeat the process above for each table, or we can clone the first job and update the details. The steps below describe how to clone the job - if creating manually each time, follow the above steps but make sure you use the updated values from the tables below.

21. Return to the **Jobs menu**, and select the **Glue-Lab-SportsTeamParquet** job by clicking the checkbox next to the name.
![image28](../../image/4.Transforming/028-CloneETLJob.png)

22. Under the **Actions** dropdown, select **Clone** job. Update the job as per the following tables, then **Save** and **Run**.

##### Sport_Location:
Create a **Glue-Lab-SportLocationParquet** job with the following attributes:
![imageSL](../../image/4.Transforming/Sport_Location.png)

##### Sporting_Event:
Create a **Glue-Lab-SportingEventParquet** job with the following attributes:
![imageSE](../../image/4.Transforming/Sporting_Event.png)

##### Sporting_Event_Ticket:
Create a **Glue-Lab-SportingEventTicketParquet** job with the following attributes:
![imageSE](../../image/4.Transforming/Sporting_Event.png)

![imageTable](../../image/4.Transforming/SETTable.png)

##### Person:
Create a **Glue-Lab-PersonParquet** job with the following attributes:
![imageP](../../image/4.Transforming/TbPerson.png)

23. Success run all ETL job
![image](../../image/4.Transforming/MonitoringRunJob.png)

24. S3 Bucket for Parquet file is created
![image](../../image/4.Transforming/S3Bucket_dms_parquet.png)

#### PART D: Create Glue Crawler for Parquet Files
1. In the **Glue navigation** menu, under **Data Catalog** select **Crawlers**. Click **Create crawler**.
![image29](../../image/4.Transforming/029-CreateNewCrawler.png)

2. For **Crawler name**, type ```glue-lab-parquet-crawler``` and Click **Next**.
![image30](../../image/4.Transforming/030-CreateNewCrawlerStep1.png)

3. In next screen Choose **data sources and classifiers**, select **Not yet** and click **Add a data source**
4. In the **Add data store** screen
- For **Choose a data store**, select “S3”.
- For **Location of S3 data**, select “**In this account**”.
- For **S3 path**, specify the S3 Path (Parent Parquet folder) that contains the nested parquet files e.g., s3://xxx-dmslabs3bucket-xxx/tickets/dms_parquet/
- Leave all other parameters as default
- Click **Add an S3 data source**.
![image31](../../image/4.Transforming/031-CreateNewCrawlerStep2.png)

5. In next screen Choose **data sources and classifiers**, click **Next**.
6. On the **Configure security settings** page, select **Choose an existing IAM role**.
- For IAM role, select the existing role “xxx-GlueLabRole-xxx” and
- Click **Next**.
7. On the **Set output and scheduling** page,
- Under Target database, choose your existing database which you created earlier e.g. ticketdata
- For the Prefix added to tables (optional), type parquet_
- For Crawler schedule select “On Demand” and Click Next.
![image32](../../image/4.Transforming/032-CreateNewCrawlerStep3.png)

8. Review the summary page and click **Create crawler**.
![image33](../../image/4.Transforming/033-SuccessCreateNewCrawler.png)

9. Click **Run Crawler**. Once your crawler has finished running, you should see that that tables were added, depending on how many parquet ETL conversions you set up in the previous section.
![image34](../../image/4.Transforming/034-SuccessRunNewCrawler.png)

**Confirm you can see the tables**

10. In the left navigation pane, click **Tables**.
11. Add the filter *parquet* for **Classification** to return the newly created tables.
![image35](../../image/4.Transforming/035-CheckDB.png)

**Congratulations!** You have completed the **Data Validation and ETL lab**