---
layout: post
title: Basics of High-Level S3 Commands
tags: [Amazon, S3, AWS]
categories: AWS
---

Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance.

It's an Object storage built to store and retrieve any amount of data from anywhere.

Let's talk, ```Basics of High-Level (S3) Commands``` with the AWS CLI,

<img src="{{ site.url }}/public/images/amazon-s3.png"/>

## ```Configuring the AWS CLI```
aws configure command is the fastest way to set up your AWS CLI installation.

The information in the default profile is used any time you run an AWS CLI command that doesn't explicitly specify a profile to use.

```console

$ aws configure
AWS Access Key ID [None]: AKIALAHOORADNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUerFEMI/LAHURANG/bPyRSiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json

```

The AWS Access Key ID and AWS Secret Access Key are your AWS credentials. 

#### Creating Multiple Profiles

simply by providing below option

```console
$ aws configure --profile produser
```

When you run a command, you can omit the ```--profile``` option and use the credentials and settings stored in the default profile.

```console
$ aws s3 ls
```
Or you can specify a ```--profile``` profilename

```console
$ aws s3 ls --profile produser
```
To update any of your settings, simply run aws configure again (with or without the ```--profile``` parameter, depending on which profile you want to update) and enter new values as appropriate.


##```Manage Amazon S3 Buckets and Objects```

AWS S3 commands support common bucket operations, such as creating, listing, and deleting buckets.

#### Create a Bucket -

Bucket names must be globally unique, Bucket names can contain lowercase letters, numbers, hyphens, and periods. Bucket names can start and end only with a letter or number.

Use the S3 mb command to create a bucket.

```console
$ aws s3 mb s3://bucket-name 
```

#### List Your Buckets -
    $ aws s3 ls

Lists all objects and folders (referred to in S3 as 'prefixes') in a bucket.

```console
$ aws s3 ls s3://bucket-name
```

You can filter the output to a specific prefix by including it in the command. 

```console
$ aws s3 ls s3://bucket-name/path/
```

#### Delete a Bucket -
Remove a bucket, use the S3 rb command,

```console
$ aws s3 rb s3://bucket-name --force
```

#### Remove object from S3 -

```console
$ aws s3 rm s3://bucket-name/doc/1.png --recursive
$ aws s3 rm s3://incentivenetworks-dev/purchase-receipts/ --recursive
```

#### Remove objects from S3 -
```console
$ aws s3 rm s3://bucket-name/doc --recursive
```
