# AWS Lambda Data Transfer Cost

中文 ｜ [English](Container-EN.md)

## Summary

AWS 提供两类托管的容器服务：Amazon Elastic Container Serivce（ECS），Amazon Elastic Kubernetes Service（EKS）。本章内容首先简要介绍这两类容器服务的网络相关知识，对本章内容适用的前提条件进行说明。随后总结了在几种典型应用场景下，数据传输所产生的费用计算方式。包含如下几个部分：  

- 1. [Networking Concept for EKS & ECS](#1-networking-concept-for-eks--ecs)
- 2. [Service Access Data Transfer](#2-service-access-data-transfer)
- 3. [Pull Image from ECR](#3-pull-image-from-ecr)

本章参考了官网中以下费用计算说明：

- EC2 计费说明：[全球区域](https://aws.amazon.com/cn/ec2/pricing/on-demand/)，[宁夏、北京区域](https://www.amazonaws.cn/ec2/pricing/)；
- AWS Fargate 计费说明：[全球区域](https://aws.amazon.com/cn/fargate/pricing/)，[宁夏、北京区域](https://www.amazonaws.cn/fargate/pricing/)；
- Amazon ECR 计费说明：[全球区域](https://aws.amazon.com/cn/ecr/pricing/)，[宁夏、北京区域](https://www.amazonaws.cn/ecr/pricing/)；
- PrivateLink 计费说明：[全球区域](https://aws.amazon.com/cn/privatelink/pricing/)，[宁夏、北京区域](https://www.amazonaws.cn/privatelink/pricing/)；
- NAT 网关定价说明：[全球区域](https://aws.amazon.com/cn/vpc/pricing/)，[宁夏、北京区域](https://www.amazonaws.cn/vpc/pricing/)

## 1. Networking Concept for EKS & ECS

Amazon ECS 和 Amazon EKS 是 AWS 推出的完全托管式容器编排服务。在计算平面的资源提供方式上，ECS 和 EKS 都支持两类方式：通过 EC2 实例提供计算资源，通过 Fargate 提供计算资源。

在使用 EC2 实例提供计算资源时，容器服务会在客户自己的 VPC 内创建 EC2 实例，此时用户可以看到、并访问这些 EC2 实例。

在使用 [AWS Fargate](https://aws.amazon.com/cn/fargate/) 方式提供计算资源时，所需的 EC2 实例将创建在 Fargate VPC 内，由 Fargate 服务进行管理。这些 EC2 实例对用户不可见，用户也不能访问这些 EC2 实例，不能部署 DaemonSet 资源。Fargate 服务会在客户 VPC 中创建 ENI 资源，容器会通过这些 ENI 对外进行通信。

逻辑示意如下图：

![EC2 vs Fargate](png/01.01-General.png)

针对 EKS 和 ECS 服务，AWS 都提供了专门容器网络接口（Container Network Interface, CNI）插件。通过 CNI，容器可以直接使用 VPC 网络进行通信，获得 VPC 分配的 IP 地址，而不是容器 overlay 层的 IP 地址。这种方式提升了通信效率。具体到 EKS 和 ECS 服务，CNI 提供通信的方式略有不同。

### 1.1 EKS CNI Concept

在 EKS 中，使用 EC2 实例提供计算资源时，[每个 pod 将会获得一个独立的私有 IP 地址](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/pod-networking.html)：

>Amazon EKS 支持通过适用于 Kubernetes 的 Amazon VPC 容器网络接口 (CNI) 插件进行本机 VPC 联网。此插件会从您的 VPC 为每个容器分配一个私有 IPv4 或 IPv6 地址。

使用 Fargate 方式提供计算资源时，[每个 pod 将会获得一个独立的 ENI](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/fargate.html)，且只能位于私有子网：

>在 Fargate 上运行的每组容器都有自己的隔离边界。其不与其他组的容器共享底层内核、CPU 资源、内存资源或弹性网络接口。
>
>在 Fargate 上运行的 Pod 仅在私有子网上受支持（对AWS服务具有 NAT 网关访问权限，但没有到互联网网关的直接路由），因此您的集群的 VPC 必须具有可用的私有子网。

EKS CNI 插件的工作示意图如下：

![CNI for EKS](png/01.01-General.png)

### 1.2 ECS CNI Concept

尽管 ECS 的 CNI 插件同样是实现 Container 可以使用 VPC 的网络进行通信（创建 ECS Task 时选择 awsvpc 模式），但其实现的方式和 EKS CNI 略有不同。

在使用 EC2 实例提供计算资源时，ECS CNI [为每个 Task 分配一个独立的 ENI](https://docs.aws.amazon.com/zh_cn/AmazonECS/latest/developerguide/task-networking-awsvpc.html)（EKS CNI 则是为每个 pod 分配一个独立的 private IP），并通过在 Task 中创建一个附加的 pause 容器来管理 Task 内的网络栈：

>每个任务都有自己的 elastic network interface (ENI)。

>Amazon ECS 容器代理会为每个任务创建一个附加的 pause 容器，然后在任务定义中启动该容器。然后，它通过运行 [amazon-ecs-cni-plugins](https://github.com/aws/amazon-ecs-cni-plugins) CNI 插件来配置 pause 容器的网络命名空间。然后，该代理会启动任务中的其余容器，以使其共享 pause 容器的网络堆栈。

在使用 Fargate 模式提供计算资源时，ECS CNI [为每个 Task 分配一个独立的 ENI](https://docs.aws.amazon.com/zh_cn/AmazonECS/latest/developerguide/AWS_Fargate.html)，且 [可以位于公有子网或私有子网](https://docs.aws.amazon.com/zh_cn/AmazonECS/latest/userguide/fargate-task-networking.html)：

>Fargate 的 Amazon ECS 任务定义需要将网络模式设置为 awsvpc。awsvpc 网络模式会为每个任务提供它自己的弹性网络接口。

>使用公有子网时，您可以向任务 ENI 分配公有 IP 地址。

>使用私有子网时，子网可以连接一个 NAT 网关。

ECS CNI 插件的工作示意图如下：

![CNI for ECS](png/01.03-CNI-ECS.png)

[返回顶部](#summary)

## 2. Service Access Data Transfer

基于第一部分中的介绍可知，在使用 EKS/ECS 对外提供服务时，与 EC2 实例对外提供服务时的计费逻辑。可以通过 Public IP 直接访问互联网，或通过 ELB 对外提供服务。

无论是使用 EC2 模式还是 Fargate 模式，本质上都是通过在客户 VPC 中的 ENI 处理网络传输流量。因此，在容器对外提供服务的场景下，可参考本系列文章中 [EC2 数据传输费用](../EC2/EC2-CN.md) 的说明。

[返回顶部](#Summary)

## 3. Pull Image from ECR

在使用容器时，另一个常见的数据传输场景是镜像的拉取。在使用 EKS 或 ECS 时，可以使用 Amazon Elastic Container Registry（ECR）作为镜像库。下图中展示了在几种典型场景下，ECS/EKS 上运行的容器从 ECR 中拉取镜像时所产生的流量费用。

**注意：**

- 为简化表示，下图中根据 Container 使用的 ENI 所在子网来确定图示中 Container 的位置，不再详细区分计算资源来自于 EC2 实例还是 Fargate；
- 图中综合表示了多种镜像获取场景，并不代表实际的网络设计。

![Pull Image from ECR](png/01.01-General.png)

几种拉取镜像的场景说明如下：

**1. ECR-IAD -> ECR-PDX**

ECR 支持在两个区域间复制镜像，[其产生的流量费为标准的 ECR 数据传出费用](https://aws.amazon.com/blogs/containers/cross-region-replication-in-amazon-ecr-has-landed/)。

>Data transferred when copying images across regions incur ECR data transfer out charges based on the source repository’s Region.

图中源区域为北弗吉尼亚区域（us-east-1），ECR 数据传出时计费标准为 0.09 $/GB。

而且，当 ECR 跨区域访问其它区域时，将会 [产生标准的传到互联网的数据传出费用（Data Transfer Out)](https://aws.amazon.com/cn/ecr/pricing/)，而不是遵循区域间流量传输费用的计费标准。

>不同区域中， Amazon ECR 和其他服务之间的数据传输，传输双方均按 Internet 数据传输费率收费。

**2.  ECR-IAD -> Container-1**

位于公共子网内的容器从相同区域内的 ECR 镜像仓库中拉取镜像时，不产生流量费用。

>在 Amazon ECR 和同一区域内的其他服务（例如 Amazon EC2、AWS Lambda、AWS App Runner 或 AWS Fargate）之间传输数据免费（即每 GB 0.00 美元）。

**3.  ECR-PDX -> Container-2**

如 ECR-IAD -> ECR-PDX 场景中所阐述的，ECR 在跨区域数据传输时，参考 Internet 数据传输费率收费。

因此当位于 us-east-1 的 Container-2 从位于 us-west-2 的 ECR-PDX 中拉取镜像时，需要按照 us-west-2 的费率收取互联网传输费。

**4.  ECR-IAD -> Container-3**

对位于私有子网中的容器来说，获取 ECR 中的镜像有两种方式：通过 NAT Gateway 获取，通过 ECR Interface Endpoint 获取。

Container-3 通过 NAT Gateway 拉取镜像。由于 Container-3 和 ECR-IAD 在同一区域，因此 ECR 不产生流量传输费。但由于所有流量都会经过 NAT Gateway，因此在 NAT Gateway 上会产生数据处理费用。所有经过 NAT Gateway 的流量都会收取数据处理费，不区分流入还是流出方向。以 us-east-1 区域为例，NAT Gateway 的数据处理按照 0.045$/GB 进行收取。（NAT Gateway 还会按照 0.045$/h 收取时长费）

**5.  ECR-IAD -> Container-4**

如果一个子网中的资源不能通过 NAT Gateway 访问互联网，也可以通过 ECR Endpoint 访问相同区域内的 ECR 镜像库。图中 Container-4 采用的就是这种方式。ECR Endpoint 是接口类终端节点（Interface Endpoint），会收取使用时长费和数据处理费。

因此当 Container-4 从 ECR-IAD 中拉取镜像时，ECR-IAD 不会产生流量费，但 ECR Interface Endpoint 上会产生数据处理费。以 us-east-1 为例，收费标准为 0.01$/GB。

（Interface endpoint 还会收取时长费，例如在北弗吉尼亚区域按照 0.01$/h 收取。）

**6. Container-3 <--> S3-IAD**

位于私有子网中的资源可以通过 S3 Endpoint 访问相同区域内的 S3 资源。如果创建的 S3 Endpoint 为网管类终端节点（Gateway Endpoint），不会产生任何费用。

**7. ECR-PDX -> Container-4**

VPC 内的终端节点不支持跨区域资源的访问，因此 Container-4 不能通过 ECR Interface Endpoint 获取位于 us-west-2 的 ECR-PDX 中的镜像。

[返回顶部](#Summary)

[【返回 README】](../../README.md)