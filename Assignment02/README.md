# 作业 2 - Add Dapr service-to-service invocation

在此作业中，您将把Dapr添加到混合中。您将使用 **Service-to-service 调用** 构建块.

## Dapr Service-to-service 调用构建块

在微服务应用程序中，重要的是能够与其他服务通信而不需要知道它们在哪里。尤其是当服务在Kubernetes（或某些其他编排平台）中运行时，可以随时移动服务并用新版本替换服务。这就是Dapr服务到服务构建块的作用所在。它的工作方式如下：


![](img/service-invocation.png)

1. 服务A进行了针对服务B的 http/gRPC调用.  这些调用通过本地的 Dapr sidecar.
2. Dapr 发现服务B的位置并将消息转发到服务B的 Dapr sidecar
3. 服务B的Dapr边车将请求转发给服务B。服务B执行其相应的业务逻辑.
4. 服务B发送对服务A的响应。该响应来自服务B的 sidecar. 
5. Dapr 转发响应到 服务A's Dapr sidecar.
6. 服务 A 收到响应.

对于本作业动手作业，这是您需要了解的所有关于此构建基块的信息。如果您想获取更多详细信息，请阅读Dapr文档中[introduction to this building-block](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md)

## 作业目标

为了完成此任务，必须达到以下目标 :

- 使用 Dapr 运行Government service .
- TrafficControl service 使用 Dapr .NET 客户端使用 Dapr service-to-service 调用  Government service 的GetVehicleInfo方法 

## DIY 说明

首先使用VS Code 打开此仓库中文件夹 `Assignment 2` . 然后打开 [Dapr documentation](https://github.com/dapr/docs) 并开始旅程. 确保 Government service 使用端口 `50001` 作为 dapr-grpc-port. 如果你需要提示, 请查看 step-by-step 部分.

## Step by step 说明

要获取实现 目标的 step-by-step 说明, 请打开 [step-by-step 说明](step-by-step.md).

## 下一个作业

在进行下一个作业之前，请确保停止所有正在运行的进程。

转到 [作业 3](../Assignment03/README.md).
