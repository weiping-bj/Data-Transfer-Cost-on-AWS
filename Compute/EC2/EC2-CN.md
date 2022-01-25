## Amazon EC2 Data Transfer Cost

### Summary

* [EC2 <----> 互联网](### EC2 <--> Internet)： 
	
数据流向 | 互联网 --> EC2 | EC2 --> 互联网
----|------|----
EC2 流量费 | 无流量费 | 数据流出费用

*  EC2 <----> Amazon S3（通过公共 IP 访问）：

相同区域 | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 流量费 | 无流量费 | 无流量费
S3 流量费 | 无流量费  | 无流量费

跨区域 | S3 --> EC2 | EC2 --> S3
----|------|----
EC2 流量费 | 无流量费 | 跨区域流量费
S3 流量费 | 跨区域流量费  | 无流量费

* [EC2 <----> EC2]



### EC2 <--> Internet
中文 ｜ [English](01.EC2-Internet-EN.md)

本节中所提到的“互联网”不包括 AWS 环境下的公共 IP。

从互联网流入 EC2 实例的所有流量均不产生费用，全球所有区域均遵从此标准。

从 EC2 传出至互联网的流量根据每 GB 收取流量费。
  
![EC2-Internet](png/01.ec2-inernet.png "EC2<-->Internet")

- 北京区域、宁夏区域，统一收费标准：0.933元/GB，[官网说明](https://www.amazonaws.cn/ec2/pricing/)；  
- 全球其它区域：以 10TB、40TB、100TB、150TB 提供阶梯计费方式，[官网说明](https://aws.amazon.com/cn/ec2/pricing/on-demand/)

数据自 Amazon EC2 传出至互联网 | 流量费用 
----|------
每月不超出 1GB 的部分 | 每 GB 0.00 USD
每月随后的 9.999TB | 每 GB 0.09 USD
每月随后的 40TB | 每 GB 0.085 USD
每月随后的 100TB | 每 GB 0.07 USD
每月超出 150TB 的部分 | 每 GB 0.05 USD
（*截止2021年3月31日，弗吉尼亚北部区域价格参考*）




[【返回目录】](../README-CN.md)