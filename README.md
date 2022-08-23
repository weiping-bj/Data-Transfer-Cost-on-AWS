# Data Transfer Cost on AWS
 
中文 ｜ [English](README-EN.md)  

**本文旨在帮助人们理解 AWS 数据传输成本组成，预估可能产生的费用，不能视为官方解读。实际产生的费用请以 AWS 账单为准。**

**所有图示只是为了方便说明流量费用的组成，不可作为实际网络架构设计的参考。**

对于在不同场景下 AWS 上网络传输成本的计算方式，您可以根据以下目录快速查找。  

- [Amazon EC2](Compute/EC2/EC2-CN.md) ：包含 EC2 到互联网、EC2 到 EC2、EC2 到 AWS 服务（S3、CloudFront 等）之间的传输成本说明；
- [Amazon VPC](Networking/VPC/VPC-CN.md)：包含 VPC Peering、NAT Gateway、VPC Endpoint 的传输成本说明；
- [DX & VPN](Networking/Connection/Connection-CN.md)：包含 DX、DX Gateway、SiteLink、Site-to-Site VPN 的传输成本说明；
- [AWS Transit Gateway](Networking/TGW/TGW-CN.md)：包含在跨 AZ、跨 Region、跨账号场景下的传输成本说明；
- [Content Delivery](Networking/ContentDelivery/ContentDelivery-CN.md)：包含 CloudFront、Global Accelerator 的传输成本说明；
- [AWS Lambda](Compute/Lambda/Lambda-CN.md)：包含 Lambda 服务访问公共互联网、通过公共 IP 访问 AWS 服务、访问 VPC 内部资源的传输成本说明；
- [EKS & ECS](Compute/Container/Container-CN.md)：对使用 CNI 插件时，EKS/ECS 的网络通信方式进行简要介绍，并对拉取镜像时几种不同场景下的数据传输成本进行了说明。
- [Amazon RDS](Database/RDBMS/RDBMS-CN.md)：针对 RDS/Aurora 的数据传输成本进行说明。
- [NOSQL 类数据库](Database/NOSQL/NOSQL-CN.md)：包含三种 NOSQL 数据库产品的数据传输成本说明，Amazon DynamoDB, Amazon DocumentDB, Amazon Keyspaces。