# Data Transfer Cost on AWS
 
[中文](README.md) | English

**NOTE:** **The content of this repository is NOT an official interpretation. The repository is intended to help people understand the components of AWS data transfer costs and to provide an estimate of the costs that may be incurred. please refer to AWS billing for actual costs incurred.**

**All diagrams are just for the convenience of illustrating the components of traffic costs, and should NOT be used as a reference for actual network architecture design.**

For a quick search of how network transport costs are calculated on AWS in different scenarios, you may follow the catalog below. 

- [Amazon EC2](Compute/EC2/EC2.md): Transfer costs about EC2 to the Internet, EC2 to EC2, EC2 to AWS services (S3, CloudFront, etc.).
- [Amazon VPC](Networking/VPC/VPC.md): VPC Peering, NAT Gateway, and VPC Endpoint;
- [DX & VPN](Networking/Connection/Connection.md): DX, DX Gateway, SiteLink, Site-to-Site VPN;
- [AWS Transit Gateway](Networking/TGW/TGW.md): TGW transformation costs in cross AZ, cross Region, and cross Accounts;
- [Content Delivery](Networking/ContentDelivery/ContentDelivery.md)：CloudFront, Global Accelerator