# Data Transfer Cost on AWS
 
Englisht | [中文](README-CN.md)

This repository is used to categorize how network traffic is calculated on AWS in different scenarios, and you can quickly find it according to the following table of contents.

**NOTE:** **The content of this repository is NOT an official description, please refer to AWS billing for actual costs.**

[toc]

# Compute #
## Amazon EC2 ##

* [EC2 <----> Internet](EC2-Others/01.EC2-Internet-EN.md)：

Traffic Direction | Internet --> EC2 | EC2 --> Internet
----|------|----
EC2 Data Transfer Cost | No Cost | Data Transfer Out Cost

*  [EC2 <----> Amazon S3 (via Public IP)](EC2/02.EC2-S3-CN.md)：

Same Region | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 Data Transfer Cost | No Cost | No Cost
S3 Data Transfer Cost | No Cost  | No Cost

Cross Region | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 Data Transfer Cost | No Cost | Cost
S3 Data Transfer Cost | Cost  | No Cost

# VPC #