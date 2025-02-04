+++
title = "Data Ingestion With AWS S3"
type = ""
weight = 1
pre = "3.1. "
+++

### **Steps**
1. Create S3 bucket.
2. Open AWS CloudShell.
3. Copy the data from the staging Amazon S3 bucket to your S3 bucket.
4. Verify the data.

#### Step 1: Create S3 bucket
Create S3 bucket to store data.
- Bucket name: ```ingestion-data-skip-dms-lab```

![CreateBucket](/image/3.Ingestion/001-CreateS3Bucket.png)
![CreateBucket](/image/3.Ingestion/002-CreateS3Bucket.png)
![CreateBucket](/image/3.Ingestion/003-CreateS3Bucket.png)
![CreateBucket](/image/3.Ingestion/004-CreateS3Bucket.png)

- Success to create S3 bucket

![CreateBucket](/image/3.Ingestion/005-SuccessCreateS3Bucket.png)

#### Step 2: Open AWS CloudShell
Open [AWS CloudShell](https://console.aws.amazon.com/cloudshell/home?region=us-east-1). It will open a terminal window in the browser. (If there is a pop-up, close it)
> [!NOTE]
> CloudShell is available in specific regions and you shall find the supported regions in [AWS Regional Services List](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/). By executing the following command you will be copying the data to the correct S3 bucket in whatever region it belongs (It can also be across region)

#### Step 3: Copy the data from the staging Amazon S3 bucket to your S3 bucket
Issue the following command in the terminal, and replace the bucket name with your own one.
```aws s3 cp --recursive --copy-props none s3://aws-dataengineering-day.workshop.aws/data/ s3://<YourBucketName>/tickets/```

![CloudShell](/image/3.Ingestion/006-CloudShell-Copy-Data-S3.png)

#### Step 4: Verify the data
1. Open the S3 console and view the data that was copied through CloudShell terminal.
2. Your S3 bucket name will look like below: BucketName/bucket_folder_name/schema_name/table_name/objects/
3. In our lab example this becomes: /'BucketName'/tickets/dms_sample  with a separate path for each table_name

![S3Bucket](/image/3.Ingestion/007-IngestionDataS3Bucket.png)

### **Next Steps**
In the next part of this lab, we will complete the following tasks:
- [Extract, Transform and Load Data Lake with AWS Glue](4-Transforming/)