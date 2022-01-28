# Amazon EC2 Data Transfer Cost

## Summary

本章内容总结了 EC2 几种典型应用场景下，数据传输所产生的费用计算方式。包含如下场景：

- 1. [EC2 <--> Internet](#1-ec2----internet)
- 2. [EC2 <--> S3（不使用 S3 终端节点）](#2-ec2----s3不使用-s3-终端节点)
  - 2.1 [EC2 和 S3 属于相同区域](#21-ec2-和-s3-属于相同区域)
    - 2.1.1 [EC2 和 S3 来自相同账号](#211-ec2-和-s3-来自相同账号)
    - 2.1.2  [EC2 和 S3 来自不同账号](#212-ec2-和-s3-来自不同账号)
  - 2.2 [EC2 和 S3 属于不同区域](#22-ec2-和-s3-属于不同区域)
    - 2.2.1 [EC2 和 S3 来自相同账号](#221-ec2-和-s3-来自相同账号)
    - 2.2.2 [EC2 和 S3 来自不同账号](#222-ec2-和-s3-来自不同账号)
  - 2.3 [EC2 <--> S3（使用 S3 终端节点）](#23-ec2----s3使用-s3-终端节点)
- 3. [EC2 <--> EC2](#3-ec2----ec2)
  - 3.1 [当 EC2 实例处于相同可用区](#31-当-ec2-实例处于相同可用区)
  - 3.2 [当 EC2 实例处于相同区域的不同可用区](#32-当-ec2-实例处于相同区域的不同可用区)
  - 3.3 [当 EC2 实例处于不同区域时](#33-当-ec2-实例处于不同区域时)

本章参考了官网中以下费用计算说明：

- EC2 计费说明：[全球区域](https://aws.amazon.com/cn/ec2/pricing/on-demand/)，[宁夏、北京区域](https://www.amazonaws.cn/ec2/pricing/)；
- S3 计费说明：[全球区域](https://aws.amazon.com/cn/s3/pricing/?nc=sn&loc=4)，[宁夏、北京区域](https://www.amazonaws.cn/s3/pricing/)；

## 1. EC2 <--> Internet

本节中所提到的“互联网”不包括 AWS 环境下的公共 IP。

从互联网流入 EC2 实例的所有流量均不产生费用，全球所有区域均遵从此标准。

从 EC2 实例传出至互联网的流量根据每 GB 收取流量费。
  
![EC2-Internet](png/01.ec2-inernet.png "EC2<-->Internet")

- 北京区域、宁夏区域，统一收费标准：0.933元/GB，[官网说明](https://www.amazonaws.cn/ec2/pricing/)；  
- 全球其它区域：以 10TB、40TB、100TB、150TB 提供阶梯计费方式，[官网说明](https://aws.amazon.com/cn/ec2/pricing/on-demand/)

[返回目录](#summary)
## 2. EC2 <--> S3（不使用 S3 终端节点）

本小节内容仅针对 EC2 和 S3 之间的数据访问，不涉及到 NAT Gateway、VPC Endpoint 等其它服务。

### 2.1 EC2 和 S3 属于相同区域
当 EC2 实例和 S3 存储桶处于相同区域时，无论 EC2 实例和 S3 存储桶是否在同一个账号下，都不会产生数据传输的流量费用。全球所有区域（含北京、宁夏）均遵循此规则。  

#### 2.1.1 EC2 和 S3 来自相同账号
EC2 与 S3 之间无数据流量费。  
![single Account and Single Region](png/02.01-ec2-s3-1region-1account.png)  

对于 EC2 到 S3 的流量费用，[官方文档](https://aws.amazon.com/cn/ec2/pricing/on-demand/) （参见“数据传输”部分）说明如下：

>在同一 AWS 区域中的 Amazon S3、Amazon Glacier、Amazon DynamoDB、Amazon SES、Amazon SQS、Amazon Kinesis、Amazon ECR、Amazon SNS、Amazon SimpleDB 和 Amazon EC2 实例之间传输数据是免费的

#### 2.1.2 EC2 和 S3 来自不同账号
EC2 与 S3 之间无数据流量费。  
![single Account and Single Region](png/02.02-ec2-s3-1region-2accounts.png)  

EC2 文档中未明确说明跨账号时的情况。基于个人 2021 年 3 月 23 日测试结果，当处于不同账号时，相同区域内的 EC2 实例向 S3 存储桶传输数据时不产生流量费用。

对于 S3 到 EC2 的流量费用，[官方文档](https://aws.amazon.com/cn/s3/pricing/)（参见“数据传输”部分）说明如下情形下不收取流量费用：

>传出到 Amazon Elastic Compute Cloud (Amazon EC2) 实例的数据，当实例与 S3 存储桶位于同一 AWS 区域时（包括传输到位于相同 AWS 区域的不同账户时）。

### 2.2 EC2 和 S3 属于不同区域
跨区域的数据传输会产生跨区域流量费用。数据传入区域时不收费，传出区域时产生跨区域费用：
 - 全球各区域间（除北京、宁夏区域）流量费用：0.02 USD/GB，详细说明参见 [EC2 全球定价页面](https://aws.amazon.com/cn/ec2/pricing/on-demand/)  
 - 北京与宁夏区域间流量费用：0.6003 RMB/GB，详细说明参见 [EC2 中国大陆地区定价页面](https://www.amazonaws.cn/ec2/pricing/)  

以下内容以北弗吉尼亚区域（us-east-1）和伦敦区域（eu-west-2）的传输为例进行说明，传输数据为 1 GB。

#### 2.2.1 EC2 和 S3 来自相同账号
![EC2-S3 in different regions](png/02.03-ec2-s3-2regions-1account.png)  

当 EC2 向 S3 中上传 1 GB 数据时，会收取从 us-east-1 向 eu-west-2 的流量费 0.02 USD。  
当 EC2 从 S3 中下载 1 GB 数据时，会收取从 eu-west-2 向 us-east-1 的流量费 0.02 USD。

#### 2.2.2 EC2 和 S3 来自不同账号 
![EC2 and S3 in different regsion/accounts](png/02.04-ec2-s3-2regions-2account.png)  

账户 | 场景 | 产生费用的服务 |  流量方向 | 费用
----|----|----|----|----
Account-1 | 向 S3 上传文件 | EC2 | 从 us-east-1 向 eu-west-2 | 0.02 USD
Account-2 | 从 S3 下载文件 | S3 | 从 eu-west-2 向 us-east-1 | 0.02 USD

S3 还提供 [请求者付款](https://docs.aws.amazon.com/zh_cn/AmazonS3/latest/userguide/RequesterPaysBuckets.html) 功能。在存储桶上激活该功能后，因下载而产生的数据流量费用由请求下载方（而不是 S3 存储桶的所有者）承担。但在该模式下，S3 存储桶将不允许匿名下载。

以下图为例。Account-1 的 S3 存储桶位于北弗吉尼亚区域（us-east-1），Account-2 的 EC2 实例位于伦敦区域（eu-west-2）。此外，账号 2 还有一台笔记本配置了 IAM User 的 AKSK，该 User 拥有访问 S3 存储桶所需权限。Account-1 开启了“请求者付款”功能。  

![Requester Pays buckets for different regsion/accounts](png/02.05-ec2-s3-xaccount.png)  

从 S3 存储桶中下载对象所产生的流量费用全部由 Account-2 承担，包括：
- 使用 EC2 实例下载时，产生 0.02 USD/GB 跨区域流量费；
- 使用配置了 AKSK 的笔记本下载时，产生 0.09 USD/GB 的 S3 向互联网传输数据流量费；
- 使用未配置 AKSK 的笔记本下载时，任务失败。

[返回目录](#Summary)

### 2.3 EC2 <--> S3（使用 S3 终端节点）
目前，在 VPC 内可为 S3 创建两类终端节点：网关类，接口类。

- 网关类终端节点不产生使用费用：  
[AWS PrivateLink 用户手册](https://docs.aws.amazon.com/zh_cn/vpc/latest/privatelink/vpc-endpoints.html) 中明确指出：使用网关类型 VPC 终端节点不会产生数据处理费用或按小时计算的费用。如下图示意：  

![No cost with Gateway endpoint](png/02.06-ec2-s3-endpoint.png)  

- 接口类网关节点产生端口使用费和数据处理费：  
使用接口类网关节点时，将会因为 **端口使用时间**、**数据处理量**、**跨可用区数据流量** 而产生费用。前两项为接口类终端节点的标准计费维度，可以从 [AWS PrivateLink定价](https://aws.amazon.com/cn/privatelink/pricing/?nc1=h_ls) 页面中看到全球各区域的详细定价说明（北京区域和宁夏区域参见 [这里](https://www.amazonaws.cn/privatelink/pricing/)）。由于接口类网关节点基于可用区创建，如果在使用过程中出现跨可用区访问，还会产生上述第三项费用。  
下图示例中包含两个 EC2 实例，一个 Interface Endpoint。其中一个 EC2 实例与 Interface Endpoint 不在同一可用区内。两个实例分别上传 1 GB 数据，并下载 2 GB 数据。  

![Cost items with Interface endpoint](png/02.07-ec2-s3-privatelink.png)  

上述例子中，假设数据的上传、下载在 1 小时内可以完成，那么 1 小时内流量相关的成本计算如下。

Instance-1 产生的费用：  
  * instance endpoint 流量处理费 = 0.01 x 3GB（上传、下载均计费） = 0.03 USD；  
  * instance endpoint 时长费用 = 0.01 x 1 = 0.01 USD；  
合计：0.04 USD

Instance-2 产生的费用：  
  * instance endpoint 流量处理费 = 0.01 x 3GB（上传、下载均计费） = 0.03 USD；  
  * instance endpoint 时长费用 = 0.01 x 1 = 0.01 USD；  
  * 跨可用区间流量处理费用 = 0.01 x 3GB（任意方向流量均计算在内） = 0.03 USD；  
合计：0.07 USD  

[返回目录](#Summary)
## 3. EC2 <--> EC2
### 3.1 当 EC2 实例处于相同可用区

在使用私有 IP 进行通信时，处于相同 AZ 的两个 EC2 实例之间，不产生流量费用。

在使用公共 IP 进行通信时，传入、传出的流量均会收取费用，全球区域定价为 0.01 USD/GB，位于中国大陆地区的两个区域的定价为 0.067 RMB/GB。  
在使用 IPv6 进行通信时，如果两个 EC2 实例分别属于不同 VPC，即使它们处在相同可用区内，也会收取双方向 0.01 USD/GB 流量费用。

[diagram]
### 3.2 当 EC2 实例处于相同区域的不同可用区

无论 EC2 实例间使用哪种 IP 地址进行通信，均需要为数据的传入、传出进行付费，收费标准为 0.01 USD/GB，位于中国大陆地区的两个区域的定价为 0.067 RMB/GB。 

[diagram]

### 3.3 当 EC2 实例处于不同区域时

需要根据 EC2 实例的数据流出量收取区域间流量费，收费标准因源区域、目标区域而不同。当数据需要在全球区域和中国大陆地区的两个区域内进行传输时，参考互联网数据传输收费标准。

[diagram]

[【返回 README】](../../README.md)