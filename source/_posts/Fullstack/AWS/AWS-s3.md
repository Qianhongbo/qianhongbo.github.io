---
title: AWS s3
date: 2022-07-05 15:05:43
categories: 
- Fullstack
tags:
- AWS
---

## Summary

While we could use a web server that we set up on Elastic Beanstalk to serve HTML files, there are other options like AWS S3 for this purpose. It is possible that you have already heard about S3 since it is a really popular service. Here are some of its strengths:

- Inexpensive
- Foundational service on AWS upon which many other services are built
- Global and available in all regions

S3 Stands for Simple Storage Service. It is AWS's file storage service. S3 is different from a hard drive. It can be referred to as **object-based storage**.

By object-based, we mean that the file name is a key and the value of that key is the actual content of the file. Metadata, which includes information about the object, such as owner, date created, and other important information, is also stored.

## Limitations and Strengths of S3

- **S3 can't run a file system:** S3 is just meant to serve files and cannot act as an operating system.
- **Fine-grained permission system:** We can control the access to the bucket with Access Control List (ACL) policy, which is a file written in JSON or yml.
- **Configurable for web hosting:** We can serve static files like HTML and CSS on S3.

## Create an S3 bucket

Here are the summarized steps:

1. Navigate to the [S3 dashboard](https://s3.console.aws.amazon.com/s3/home?region=us-east-1#), and click on the **Create bucket** button. It will launch a new wizard.

![S3 Service - Bucket Dashboard](https://video.udacity-data.com/topher/2020/November/5fbe37ba_screenshot-2020-11-25-at-4.06.17-pm/screenshot-2020-11-25-at-4.06.17-pm.png)

We create a bucket first, and later we upload files and folders to it.

| **Field**               | **Value**                                                    |
| :---------------------- | :----------------------------------------------------------- |
| Bucket name             | Choose a unique name, such as *myawsbucket-75139724085*      |
| AWS Region              | Default                                                      |
| Object Ownership        | ACLs enabled Bucket owner preferred to determine object access. |
| Block all public access | No Create a **public** S3 bucket                             |

## Configure the S3 bucket

### Configure the Bucket Properties

Once your bucket is created, go to the **Properties** tab and scroll down to the bottom of the page to find **Static website hosting** section.

Enable the static web hosting to host a static website. Also, mention the *index.html* as the default Index document, as shown in the snapshots below.

![Navigate to the **Properties >> Static website hosting** section to Enable it. ](https://video.udacity-data.com/topher/2022/June/629f3ef5_screenshot-2022-06-07-at-5.31.24-pm/screenshot-2022-06-07-at-5.31.24-pm.jpeg)

![Enable the static web hosting to host a static website. ](https://video.udacity-data.com/topher/2022/June/629f3f25_screenshot-2022-06-07-at-5.31.48-pm/screenshot-2022-06-07-at-5.31.48-pm.jpeg)

### Configure the Bucket Permissions

Go to the **Permissions** tab and add the following:

Add bucket policy allowing other AWS services to access the bucket contents. You can use the [policy generator](https://awspolicygen.s3.amazonaws.com/policygen.html) tool to generate such an IAM policy. Enter the following bucket policy replacing your-website [myBucketName] with the name of your bucket and click “Save”.

```json
{
 "Version": "2012-10-17",
 "Statement": [
     {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": [
             "s3:GetObject"
         ],
         "Resource": [
             "arn:aws:s3:::[myBucketName]/*"
         ]
     }
 ]
}
```

## Upload File/Folders to the Bucket

From the [S3 dashboard](https://learn.udacity.com/nanodegrees/nd0067/parts/cd0295/lessons/6f2afba3-3053-4b84-b155-a2887806965f/concepts/console.aws.amazon.com/s3/home), click on the name of the bucket you have created in the step above.

![Details of an existing bucket. Upload files/folders to this bucket.](https://video.udacity-data.com/topher/2020/November/5fbe3ad2_screenshot-2020-11-25-at-4.12.35-pm/screenshot-2020-11-25-at-4.12.35-pm.png)

Click on the **Upload** button to upload files and folders into the current bucket.

![A sample file in the bucket](https://video.udacity-data.com/topher/2020/November/5fbe3e6d_screenshot-2020-11-25-at-4.52.03-pm/screenshot-2020-11-25-at-4.52.03-pm.png)

Click on the file name to view the file-specific details, as shown below.

![Details of an individual file (object)](https://video.udacity-data.com/topher/2020/November/5fbe3ec1_screenshot-2020-11-25-at-4.53.35-pm/screenshot-2020-11-25-at-4.53.35-pm.png)
