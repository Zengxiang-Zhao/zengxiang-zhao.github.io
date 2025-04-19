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
