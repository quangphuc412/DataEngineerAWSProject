+++
title = "Athena and SageMaker (Optional)"
type = ""
weight = 2
pre = "5.2. "
+++

#### Introduction
[Amazon SageMaker](https://aws.amazon.com/sagemaker/) is an end-to-end machine learning platform that lets you build, train, and deploy machine learning models in AWS. It is a highly modular service that lets you use each of these components independently of each other.

You will Learn:
- How to use Jupyter notebooks in SageMaker to integrate with the data lake using Athena
- Populate data frames for data manipulation.

![image](/image/1.Introduction/001-AthenaSageMakerArchitecture.png)

This process to prepare the data to satisfy the needs of ML algorithms is iterative. To prepare the data, we will make the table definitions in Athena available in a Jupyter notebook in SageMaker.

Jupyter notebooks are popular among data scientists and used to visualize data, perform statistical analysis, complete data manipulations, and make the data ready for machine learning work.

> [!NOTE]
> **Prerequisites**
> 
> [Ingestion with Glue](3-Ingestion/) and [Transforming data with Glue ETL](4-Transforming/) labs are prerequisites for this lab.

#### Create Amazon SageMaker Notebook Instance
1. Go to the [Amazon SageMaker](https://aws.amazon.com/sagemaker/) from AWS console
![image](/image/5.AthenaAndQuicksight/039-SageMakerService.png)

2. In the Amazon SageMaker navigation pane, go to **Notebook instances** and click **Create notebook instance**.
![image](/image/5.AthenaAndQuicksight/040-CreateNotebooksInstances.png)

3. Enter following values to create the notebook instance
- Enter a name for the Notebook instance e.g. *datalake-Sagemaker*
- You can leave Notebook instance type as the default value **ml.t3.medium** for this lab
- Leave Elastic Inference as *none*. This is to add extra resources.
- Under **IAM role**, we need to choose a role for the notebook instance in Amazon SageMaker to interact with Amazon S3. As the role doesn’t exist, from the dropdown option select **Create a new role**.
- In the **Create an IAM role** pop up window, choose **Any S3 bucket** as shown below and click **Create role**.
![image](/image/5.AthenaAndQuicksight/041-CreateNewRole.png)

- After the IAM role is created, click on the link to the IAM role, this will open the IAM role in a new tab. We will add IAM permissions to allow access to Athena from SageMaker.
![image](/image/5.AthenaAndQuicksight/042-CreateNotebooksInstances.png)

- Click on **Add permissions** and select **Attach policies**.
- Filter policies by **“Athena”**, check **AmazonAthenaFullAccess** managed policy and click **Attach policies** button at the bottom of the screen.
![image](/image/5.AthenaAndQuicksight/043-AttachPolicy.png)

- Go back to the **SageMaker Create notebook instance** browser tab.
- Leave all other options with default values. Click **Create notebook instance**.

4. Wait for the notebook instance to be created and the Status to change to **InService**. Click the **Open Jupyter** link in the **Actions** column.
![image](/image/5.AthenaAndQuicksight/044-SuccessCreateNotebookInstances.png)

5. The notebook interface opens in a new tab.
![image](/image/5.AthenaAndQuicksight/045-JupyterNotebook.png)

#### Connect the SageMaker Jupyter notebook to Athena
1. In the Jupyter notebook tab, click **New** and select **conda_python3** for the kernel. This opens a notebook in a new tab.
![image](/image/5.AthenaAndQuicksight/046-CreateNewNotebook.png)
    > [!NOTE]
    > Amazon SageMaker provides several kernels for Jupyter, including support for Python 3, MXNet, TensorFlow, and PySpark. This exercise uses Python because it includes the pandas library.

2. Within the notebook, enter the following command into a cell and click **Run** to install the Athena JDBC driver. (PyAthena is a Python [DB API 2.0 (PEP 249)](https://www.python.org/dev/peps/pep-0249/) compliant client for the [Amazon Athena JDBC driver](http://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html).)
{{< highlight go "linenos=table,linenostart=1" >}}
!pip install PyAthena[SQLAlchemy]
{{< / highlight >}}

    ![image](/image/5.AthenaAndQuicksight/047-PipInstall.png)

    Check the output that PyAthena has been installed successfully. If you see errors related to awscli 1.x, they can be ignored.

#### Working in Pandas
After the Athena driver is installed, you can use the JDBC connection to connect to Athena and populate the pandas dataframes. For data scientists, working with data is typically divided into multiple stages: ingesting and cleaning data, analyzing and modeling data, then organizing the results of the analysis into a form suitable for plotting or tabular display. Pandas is the ideal tool for all of these tasks.

1. You can load Athena table data from the data lake into a pandas dataframe and apply machine learning. Copy the following code into your notebook cell and replace with your region, S3 bucket location and database name.

    Your region: run the following command in the notebook cell to get the current AWS region.
{{< highlight go "linenos=table,linenostart=1" >}}
!aws configure get region
{{< / highlight >}}

    Your Athena Database name, this is the Glue Database name which you created during the Glue lab e.g. **ticketdata**. Update the S3 bucket location whose name contains string *ingestion-data-skip-dms-lab* followed by **athena/**, so that it looks like s3://ingestion-data-skip-dms-lab/athena/.
{{< highlight go "linenos=table,linenostart=1" >}}
from sqlalchemy import create_engine
import pandas as pd

s3_staging_dir = "s3://ingestion-data-skip-dms-lab/athena/"
connection_string = f"awsathena+rest://:@athena.us-east-1.amazonaws.com:443/ticketdata?s3_staging_dir={s3_staging_dir}"

engine = create_engine(connection_string)

df = pd.read_sql('SELECT * FROM "ticketdata"."nfl_stadium_data" order by stadium limit 10;', engine)
df
{{< / highlight >}}

2. Click **Run** and dataframe will display query output.
![image](/image/5.AthenaAndQuicksight/048-RunQuery.png)
In this query, you are loading all nfl stadium information into a pandas dataframe from the table nfl_stadium_data.
    > [!NOTE]
    > If you get a SageMaker does not have Athena execution permissions error issue. You need to add Athena Access to the Sagemaker role as steps provide in previous section.

3. You can apply different ML algorithms in the populated pandas dataframes. For example, draw a plot of the data. Copy the following code into your notebook. In this query, you are loading all even ticket information into a pandas dataframe from the table *sporting_event_ticket_info*.
{{< highlight go "linenos=table,linenostart=1" >}}
df = pd.read_sql('SELECT sport, \
    event_date_time, \
    home_team,away_team, \
    city, \
    count(*) as tickets, \
    sum(ticket_price) as total_tickets_amt, \
    avg(ticket_price) as avg_ticket_price, \
    max(ticket_price) as max_ticket_price, \
    min(ticket_price) as min_ticket_price  \
    FROM "ticketdata"."sporting_event_ticket_info" \
    group by 1,2,3,4,5 \
    order by 1,2,3,4,5  limit 1000;', engine)
df
{{< / highlight >}}

4. Click **Run** and the data frame will display the query output.
![image](/image/5.AthenaAndQuicksight/049-RunQuery.png)

5. In a new execution cell, copy following code
{{< highlight go "linenos=table,linenostart=1" >}}
import matplotlib.pyplot as plt 
df.plot(x='event_date_time',y='avg_ticket_price')
{{< / highlight >}}

6. Click Run and you will see data plot which got draw using matplotlib library.
![image](/image/5.AthenaAndQuicksight/050-VisualizationData.png)

#### Cleanup
1. Go to [SageMaker Notebook Instances console](https://console.aws.amazon.com/sagemaker/home#/notebook-instances)
2. Select the notebook instance that you created e.g. *datalake-Sagemaker*.
3. Choose **Actions → Stop**, it will take a few minutes for the notebook instance to stop.
4. Once the notebook instance is stopped, choose **Actions → Delete** and choose **Delete** in the confirmation pop-up to delete the instance.