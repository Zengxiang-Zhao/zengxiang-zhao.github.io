---
layout: default
title: AWS
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## [aws commands]

1. Detach the policy from the role before deleting the role
2. 

## [aws structure based on my thought]

In all, AWS is build by the following parts:
1. IAM : provide security 
2. EC2 : is a service of AWS and as the base of aws other services like Lambda, RMD
3. Network: connect aws services and transport the information between services.


Security Group(SG) provide security for indivisual resource( e.g.: Lambda, EC2) in aws. It can control the network connectivity using ingress or outgress and port number.

The resource is an instance of service.

## [aws Cloud Develop Kit documentation](https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_rds/MysqlEngineVersion.html)

You can find the libary of aws CDK.



## Connect the EC2 instance using the CLI

```bash
aws ssm start-session --target $INSTANCE_ID
```

## [Concept: What's the CIDR(Classless Inter-Domain Routing)](https://www.youtube.com/watch?v=KiWXRL-2TnY)

From the Google Search:
>CIDR notation is a compact way to express an IP address and its associated network by following it with a slash and a number indicating how many bits are used for the network prefix
>
>The format is IP_ADDRESS/PREFIX_LENGTH.
>
>For example, in 192.168.1.0/24, 192.168.1.0 is the IP address, and /24 is the prefix length. 

The IP Address is 32 bits. There are four parts seperated by dot(.) and each part has 8 bits. So each part has 2**8=256 different numbers. As it starts from 0, so the range is 0- 255.

For example, 10.0.0.0/16 represents all IP addresses in 10.0.0.0 and 10.0.255.255. Why? 32 bits - 16 bits = 16 bits, so the first two parts (2*8 = 16) is prefix address. the left two parts can be used for the subnet address. so the IP addresses can be used from  10.0.0.0 to 10.0.255.255.

If the subnet is 10.0.0.0/24, then all IP addresses is in 10.0.0.0 and 10.0.0.255. Because only 8 bits left for the subnet.

## [AWS CLI to restore an Amazon S3 object from the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage class](https://repost.aws/knowledge-center/restore-s3-object-glacier-storage-class)

```bash
$ aws s3api restore-object --bucket awsexamplebucket --key dir1/example.obj --restore-request '{"Days":25,"GlacierJobParameters":{"Tier":"Standard"}}'

```

## Copy local file to AWS S3

When you transfer data to AWS S3, you need to use the following code
```bash
aws s3 cp "${pathLocalFile}" s3://"$bucketName/$bucketFolder/" --sse AES256 --no-progress
```
Here you need to pay attention to the following stuff:
- you need to add backslash at the end of the bucket folder or backet name `/`. So the command line know it's a folder and save the local file to the folder, otherwise it will be treated as a file name that you want to name the local file in the AWS S3
- it's safe to use double quote `"` to enclose the variable when using the command line in case the path of varialbe contains empty space which will make the command line volid
