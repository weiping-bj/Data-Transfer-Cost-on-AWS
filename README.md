# Data Transfer Cost on AWS
 
中文 ｜ [English](README-EN.md)

本文用来分类说明在不同场景下 AWS 上网络流量的计算方式，您可以根据以下目录快速查找。  

>【注意】**本文内容非官方说明，实际费用请以 AWS 账单为准。**

# 目录
- [Data Transfer Cost on AWS](#data-transfer-cost-on-aws)
- [目录](#目录)
- [Compute](#compute)
  - [Amazon EC2](#amazon-ec2)
  - [AWS Lambda](#aws-lambda)
- [VPC](#vpc)



# Compute
## [Amazon EC2](Compute/EC2/EC2-CN.md) 

包含 EC2 到互联网、EC2 到 EC2、EC2 到 AWS 服务（S3、ECR、DynamoDB）之间的流量费用说明。
* EC2 <----> 互联网： 
	
数据流向 | 互联网 --> EC2 | EC2 --> 互联网
----|------|----
EC2 流量费 | 无流量费 | 数据流出费用

*  EC2 <----> Amazon S3（通过公共 IP 访问）]：

相同区域 | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 流量费 | 无流量费 | 无流量费
S3 流量费 | 无流量费  | 无流量费

跨区域 | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 流量费 | 无流量费 | 跨区域流量费
S3 流量费 | 跨区域流量费  | 无流量费

* [EC2 <----> EC2]
	
[返回顶部](#目录)
## AWS Lambda ##


# VPC 

#Storage
##S3



