---
title: AWS CLI
date: 2022-07-05 15:36:10
categories: 
- Fullstack
tags:
- AWS
---

## AWS console or the CLI

Often we are also faced with the decision of using **the AWS console or the CLI**. While in most cases we can accomplish the same with both options, we should prioritize the console when we are learning new services. On the other hand, we must prioritize the CLI when doing repetitive tasks and when writing scripts.

## Create an IAM user

1. Go to the IAM dashboard
2. Create a new user
3. Set user name
4. Set the permissions to the new user by attaching the AWS Managed **AdministratorAccess** policy from the list of existing policies.
5. After a user is created successfully, download the access key file (.csv) containing the *Access Key ID* and a *Secret Access Key*. You can even copy the keys and stay on the same page. **Don’t skip this step as this will be your only opportunity to download the secret access key file.**

AWS will generate an IAM access key when you create an IAM user. **IAM key** is the identity access management key that can identify your application (terminal) to the AWS CLI. The IAM key holds the associated permissions to access your account.

## Install AWS CLI (Mac)

```bash
brew install awscli
aws --version
```

## Configure the AWS CLI

Run the command below to configure the AWS CLI using the *Access Key ID* and a *Secret Access Key* generated in the previous step. If you have closed the web console that showed the access key, you can open the downloaded access key file (.csv) to copy the keys later.

```bash
aws configure 
```

| Prompt                | Value                                   |
| :-------------------- | :-------------------------------------- |
| AWS Access Key ID     | [Copy from the classroom]               |
| AWS Secret Access Key | [Copy from the classroom]               |
| Default region name   | us-east-2 OR us-east-1 (or your choice) |
| Default output format | json                                    |

```bash
# If you are using the Access key of an Admin IAM user, you should reset the `aws_session_token`
aws configure set aws_session_token "" 
# If you are using the Udacity generated Access key, you should set the `aws_session_token`
aws configure set aws_session_token "XXXXXXXX" 
```

where, `"XXXXXXXX"` is the session token copied from the classroom after clicking on the "OPEN CLOUD GATEWAY" button.

## Check

```bash
# View the current configuration
aws configure list 
# View all existing profile names
aws configure list-profiles
# In case, you want to change the region in a given profile
# aws configure set <parameter> <value>  --profile <profile-name>
aws configure set region us-east-1  
```

```bash
# If you've just one profile set locally
aws iam list-users
# If you've multiple profiles set locally
aws iam list-users --profile <profile-name>
```

## Create a Bucket using the CLI

### Create a public bucket in the us-east-1 region:

```bash
# Bucket names are unique across the Internet, just like DNS. 
# Replace the `033212455158` part with some other set of digits. 
aws s3api  create-bucket --bucket mybucket033212455158 --acl bucket-owner-full-control --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2
```

In the command above,

- `--bucket` option specifies the bucket name of your choice. It must be unique across all AWS accounts.
- `--acl` option specifies the accessibility level
- `--region` specifies the AWS region where you want to create this bucket.
- `--create-bucket-configuration`: If you want to create the bucket in the desired region outside of us-east-1, you will need something like `--region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2` which will set an appropriate LocationConstraint.

### Upload a sample file to your bucket. 

The **sample.html** file used in the command below is present at the bottom of this page.

```bash
aws s3api put-object --bucket mybucket033212455158 --key sample.html --body sample.html --content-type text/html
```

In the command above,

- `--key` option specifies the name you want to assign to your object in the bucket
- `--body` option specifies the file name (complete path) to upload from your local system
- `--content-type` specifies the standard MIME type describing the format of the contents.

### Verification

Verify the S3 bucket by going to the AWS web console. Alternatively, you can run `aws s3 ls` in the command line. This will show all of the S3 buckets in your account.

### Delete the bucket and its content

A bucket can only be deleted if it is empty. Therefore, first delete the `Sample.html`, and then delete the bucket, as follows:

```bash
aws s3api delete-object --bucket mybucket033212455158 --key sample.html
aws s3api delete-bucket --bucket mybucket033212455158
```

Navigate back to the S3 dashboard (AWS web console), and verify if the bucket has been deleted successfully.

## Using the Beanstalk CLI

While, AWS CLI can be used to perform almost any possible actions on the AWS platform, the commands to create and manage EB so are long.

Therefore, AWS created a dedicated Elastic Beanstalk (EB) CLI. The EB CLI is simple to use and provides a set of easy commands that let you control your application environment in a convenient way!

### Install EB CLI

```bash
brew install awsebcli
```

### Deploy a sample NodeJS application

- Let's create a directory. The next few command will deploy a sample NodeJS application to EB.

  ```bash
  mkdir testEB
  cd testEB
  ```

- Initialize an environment. Run this command in the root directory of the application you want to deploy. The [eb init](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-init.html) command will create ".elasticbeanstalk/config.yml" file in the current directory.

  ```bash
  # Use the node.js 14 or 16 and the default region as applicable to you
  eb init
  ```

- The [eb create](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-create.html) will bundle you application, if present in the current directory, and deploy to the EB. Otherwise, a sample application will be deployed. We can specify the `--sample` to be sure.

  ```bash
  eb create --sample --single --instance-types t2.small
  ```

  Provide you input for the prompts that appear, such as:

  - Enter Environment Name: Default
  - Enter DNS CNAME prefix: Default
  - Would you like to enable Spot Fleet requests for this environment? (y/N): N
  - Do you want to download the sample application into the current directory? (Y/n): Y

The command above will take upto 15 mins to create the following resources as part of the environment:

1. An EC2 instance (size: *t2.small*) to host your application, and without a load balancer because we have use the *--single* option.
2. A security group (firewall rules) for the EC2 instance
3. An S3 bucket to store the application artifacts
4. A CloudWatch alarm for logging and monitoring
5. A domain name

### Some more commands

- Check the health of the environment using the following command:

  ```bash
  eb health
  ```

- For troubleshooting, look into the logs from your terminal:

  ```bash
  eb logs
  ```

- Editing and redeploying the application. You can edit you local application, and commit your changes. It is important to commit the code changes before you`eb deploy` it to the beanstalk environment.

  ```bash
  git add -A
  git commit -m "change log"
  eb deploy
  ```

- If you have multiple environments running, you can associate the EB CLI with a particular one using:

  ```bash
  eb list
  eb use [env-name]
  ```

- Clean up

  Don't forget to delete your environment(s) if they are not in use:

  ```bash
  eb list
  eb terminate [env-name]
  ```

## S3 using the AWS CLI

### List the buckets

```bash
aws s3 ls
# Assuming the bucket name is: myawsbucket-751397240855
```

### Synchronize the content of the build folder into the S3 bucket

```bash
# Change the bucket name as applicable to you
aws s3 cp --recursive --acl public-read ./build s3://myawsbucket-751397240855/
```
