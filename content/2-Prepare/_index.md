+++
title = "Prepare"
type = ""
weight = 2
pre = "2. "
+++

#### **AWS Account and IAM User**
In order to get started working with AWS services, you will need an AWS account. You will also need an IAM user for this account that you can use to log into the AWS Management Console in order to provision and configure you resources.

[AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/)  enables you to manage access to AWS services and resources securely. Using IAM, you can create and manage AWS users and groups, and use permissions to allow and deny access to AWS resources.

#### Create an administrator user
In case you want to use your own account now or later, and you don't yet have an IAM user with administrator privileges, you need to create one.

1. If you don't already have an AWS account with Administrator access: [create one now by clicking here.](https://aws.amazon.com/free)

2. Once you have an AWS account, create a new IAM user for this workshop with administrator access to the AWS account: [Create a new IAM user to use for the workshop.](https://console.aws.amazon.com/iam/home?#/users$new)
   
3. Enter the user details: and select Access Type as Programmatic access.
![CreateAdminUser](../image/2.Prepare/002-1-CreateAdminUser.png)
![CreateAdminUser](../image/2.Prepare/002-2-CreateAdminUser.png)

4. Attach the AdministratorAccess IAM Policy, choose Administrator access or fine-grained access depending upon the Amazon services used for this lab. For this lab you can choose Administrator access → Click Next → Ignore Tags → Next Review.
![AttachThePolicy](../image/2.Prepare/002-3-CreateAdminUser.png)
![AttachThePolicy](../image/2.Prepare/002-4-CreateAdminUser.png)

5. Click to create the new user.
6. Successfully create Admin User.
![SuccessCreate](../image/2.Prepare/002-5-SuccessCreateAdimUser.png)

#### Congratulations on setting up!