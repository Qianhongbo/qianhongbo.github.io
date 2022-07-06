---
title: AWS Elastic Beanstalk
date: 2022-07-05 14:45:26
categories: 
- Fullstack
tags:
- AWS
---

## Elastic Beanstalk Overview

AWS Elastic Beanstalk is a service (platform as a service) that allows you to run your web application on the AWS cloud without worrying about scaling or configuring the underlying virtual machines (web servers).

AWS Elastic Beanstalk supports Java, .NET, PHP, Node.js, Python, Ruby, and Go platforms. You just need to upload an application zip file to the AWS Elastic Beanstalk and configure some settings to make the app run. We can either upload the zip file using the AWS UI (web console) or use the explicit commands in the local terminal.

After uploading the application zip file, Elastic Beanstalk will handle deploying the application to the (right-sized) EC2 VMs, load balancing, auto-scaling, and application health monitoring.

Elastic Beanstalk offers the following advantages:

- **Free**: You only pay for the servers that elastic beanstalk uses. The extra tools are free of charge.
- **Pre-built Environments**: Most major programming languages are supported out of the box.
- **Simple Server Management**: Security updates and system upgrades are done for you.
- **Easy Scaling**: If you need to provision extra servers, you can quickly change your configuration.

## What does Elastic Beanstalk use?

- **Elastic Compute Cloud (EC2)**: Used for hosting servers.
- **Simple Storage Service (S3)**: Used for storing application code and sending it to other servers.
- **Simple Notification Service (SNS)**: Provides a way to notify you of events inside the environment.

## Deployment process

### Step 1. Prepare the Application Code

Fork and clone the [Github repository](https://github.com/udacity/cd0295-reactnd-contacts-server) and follow the steps mentioned in the README.

```bash
git clone https://github.com/udacity/cd0295-reactnd-contacts-server.git
cd cd0295-reactnd-contacts-server

# Create the files for Archive.zip
npm run build
# Create an Archive.zip in the exercise root directory
npm run zip  
```

### Step 2. Create Elastic Beanstalk Node.js Environment

There are two ways to create an Elastic Beanstalk environment - using the EB CLI or AWS console. Let's use the AWS console to create a Node.js environment. Navigate to the Elastic Beanstalk service in the AWS console, and start creating an environment. Use the following values during the configuration:

| Field                                          | Value                                                        |
| :--------------------------------------------- | :----------------------------------------------------------- |
| Environment tier                               | Web server environment                                       |
| **Application information**  Application name  | *demo-app*                                                   |
| **Environment information**  Environment name  | Default                                                      |
| **Managed platform**  Platform Platform branch | Node.js Node.js 14 running on 64bit Amazon Linux 2 (Node.js 12 is deprecated now) |
| **Application code**                           | Upload your application code (**Archive.zip** file)          |

Leave the remaining fields as default, and finish creating the environment. It will take upto 15 mins to create the following resources as part of the environment:

1. An EC2 instance to host your application, and a load balancer
2. A security group (firewall rules) for the EC2 instance
3. An S3 bucket to store the application artifacts
4. A CloudWatch alarm for logging and monitoring
5. A domain name

Once you application is deployed to the Elastic Beanstalk, it will automatically run these comamnds on your behalf:

```bash
npm install --production
npm start
```

## Step 3. Save the Environment Variable

Specify an environment variable in the **Elastic Beanstalk environment >> Configuration >> Software settings >> Environment Properties** section. Save a value against the `FIRST_NAME` variable, as shown in the snapshot below.

![Set the environment variable for your application](https://video.udacity-data.com/topher/2022/June/629f2183_screenshot-2022-06-07-at-3.28.49-pm/screenshot-2022-06-07-at-3.28.49-pm.jpeg)

## Step 4.

Once the environment is deployed and the environment variable created, navigate to the deployed URL and ensure that the server responds.

### Known Issue

Chorme has a security feature that does not allow the cross-platform request to pass. As a result, it generates:

> Access to fetch at 'http://localhost:4000/contacts' from origin '[http://demoapp-env.eba-2ph6pbkm.us-east-1.elasticbeanstalk.com'](http://demoapp-env.eba-2ph6pbkm.us-east-1.elasticbeanstalk.com'/) has been blocked by CORS policy: The request client is not a secure context and the resource is in more-private address space `local`.

The workaround if to use an alternative browser, such as Safari or Firefox.
