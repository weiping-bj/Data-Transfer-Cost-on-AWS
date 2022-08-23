# Data Transfer Cost on AWS
 
[中文](README.md) | English

**NOTE:** **The content of this repository is NOT an official interpretation. The repository is intended to help people understand the components of AWS data transfer costs and to provide an estimate of the costs that may be incurred. please refer to AWS billing for actual costs incurred.**

**All diagrams are just for the convenience of illustrating the components of traffic costs, and should NOT be used as a reference for actual network architecture design.**

For a quick search of how network transport costs are calculated on AWS in different scenarios, you may follow the catalog below. 

- [Amazon EC2](Compute/EC2/EC2-EN.md): Transfer costs about EC2 to the Internet, EC2 to EC2, EC2 to AWS services (S3, CloudFront, etc.).
- [Amazon VPC](Networking/VPC/VPC-EN.md): VPC Peering, NAT Gateway, and VPC Endpoint;
- [DX & VPN](Networking/Connection/Connection-EN.md): DX, DX Gateway, SiteLink, Site-to-Site VPN;
- [AWS Transit Gateway](Networking/TGW/TGW-EN.md): TGW transformation costs in cross AZ, cross Region, and cross Accounts;
- [Content Delivery](Networking/ContentDelivery/ContentDelivery-EN.md)：CloudFront, Global Accelerator;
- [AWS Lambda](Compute/Lambda/Lambda-EN.md)：data transfer cost between Lambda and Internet/AWS Services/VPC;
- [EKS & ECS](Compute/Container/Container-EN.md): A brief introduction to the network communication method of EKS/ECS when using the CNI plug-in, and the data transfer costs for several different scenarios when pulling mirrors;
- [Amazon RDS](Database/RDBMS/RDBMS-EN.md): A brief introduction to RDS/Aurora. 
- [NOSQL Database](Database/NOSQL/NOSQL-EN.md): Including descriptions of data transfer costs for three NOSQL database products, Amazon DynamoDB, Amazon DocumentDB, Amazon Keyspaces. 