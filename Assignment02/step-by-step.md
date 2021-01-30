# 作业 2 - 添加 Dapr service-to-service 调用

## 作业目标

为了完成此任务，必须达到以下目标 :

- 使用 Dapr 运行Government service .
- TrafficControl service 使用 Dapr .NET 客户端使用 Dapr service-to-service 调用  Government service 的GetVehicleInfo方法 

### 步骤 1: 使用Dapr启动 government service 

您在作业1中使用`dotnet run` 启动了government service 。当您想通过Dapr Sidecar 处理该通信的行该服务时，您需要使用Dapr CLI启动它。启动服务时，需要指定以下几点：

- 该服务需要一个唯一的ID，Dapr可以使用它来找到它. 我们将使用 `governmentservice` 作为Id.
- API正在侦听的HTTP端口是6000。因此，您需要告知Dapr（以便它可以处理通信）
- Dapr使用gRPC与Dapr侧车进行通信。默认情况下，用于此通信的端口是`50001`。但是此端口稍后将由TrafficControl服务使用，因此我们需要为Government服务指定其他端口，以防止本地计算机上的端口冲突。我们将使用端口`50002`。
- 最后，您需要告诉Dapr如何启动该服务。这很简单，使用 `dotnet run`.

您将使用Dapr CLI的命令 `run`，并在命令行上指定上述所有选项:

1. 打开一个新的命令行窗口并转到仓库的文件夹 `Assignment02/src/GovernmentService` .

2. 输入以下命令以使用 Dapr sidecar运行服务:

   ```
   dapr run --app-id governmentservice --app-port 6000 --dapr-grpc-port 50002 dotnet run
   ```

3. 检查日志中是否有任何错误. 正如你所看到的, Dapr和应用程序日志记录都显示在控制台

就是这样，您现在正在使用Dapr Sidecar运行Government service。这意味着其他服务可以使用Dapr调用此服务。这是下一步要做的。

## 步骤 2: 使用 service-to-service 调用government service 

在此步骤中，您将更改TrafficControl服务的代码，以便它使用.NET的Dapr客户端来调用政府服务。

首先，您将添加对.NET的Dapr库的引用:

1. 使用 VS Code打开仓库的文件夹 `Assignment 2` 

1. 打开一个新的命令行窗口，然后转到该仓库中的文件夹 `Assignment02/src/TrafficControlService` .

1. 添加对Dapr ASP.NET Core集成库的引用:

   ```
   dotnet add package Dapr.AspNetCore -v 1.0.0-rc03
   ```

1. Restore 所有 references:

   ```
   dotnet restore
   ```

现在，您将使用Dapr 客户端调用 Government service:

6. 使用VS Code 打开文件 `Assignment02/src/TrafficControlService/Controllers/TrafficController.cs` .

7. 在此文件中添加using语句，以确保您可以使用Dapr客户端:

   ```csharp
   using Dapr.Client;
   ```

8. 将注入到`VehicleEntry` 方法中的客户端从IHttpClientFactory更改为DaprClient :

   ```csharp
   public async Task<ActionResult> VehicleEntry(VehicleRegistered msg, [FromServices] DaprClient daprClient)

   ```

9. 更改使用Dapr客户端获取车辆信息的部分:

   ```csharp
        // get vehicle details
        var httpRequest = daprClient.CreateInvokeMethodRequest(HttpMethod.Get,"governmentservice",$"rdw/vehicle/{msg.LicenseNumber}");
        var vehicleInfo = await daprClient.InvokeMethodAsync<VehicleInfo>(httpRequest);
   ```

现在，Dapr客户端用于直接调用Government service上的方法。Dapr将找出服务所在的位置并处理通信。

为了确保将Dapr客户端注入到VehicleEntry方法中，您需要在Startup类中注册它:

10. 使用VS Code打开 文件 `Assignment02/src/TrafficControlService/Startup.cs` 

11. 在此文件中添加using语句，以确保可以使用Dapr客户端和System.Text.Json序列化器 :

   ```csharp
   using Dapr.Client;
   using System.Text.Json;
   ```

12. 在Startup类的 `ConfigureServices` 方法末尾添加代码注册Dapr客户端的 :

   ```csharp
   services.AddDaprClient(builder => builder.UseJsonSerializationOptions(new JsonSerializerOptions()
   {
         PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
         PropertyNameCaseInsensitive = true
   }));
   ```

  如您所见，指定了一个构建器来创建Dapr客户端。Dapr客户端必须用于序列化和反序列化消息的JsonSerializer选项作为参数传递。 

## 步骤 3: 测试应用程序

现在，您将使用Dapr边车启动TrafficControl服务:

1. 确保 Government service 仍然在Dapr 边车上运行(与步骤1相同).

2. 打开一个新的命令行窗口，然后转到该仓库中的文件夹 `Assignment02/src/TrafficControlService` .

3. 通过构建代码来检查所有代码更改是否正确:

   ```
   dotnet build
   ```

   如果看到任何警告或错误，请查看前面的步骤以确保代码正确。

4. 使用Dapr side-car启动TrafficControl服务。与Dapr Sidecar通信的默认gRPC端口为50001。如果使用此端口运行边车，则在进行服务到服务的调用时无需在代码中指定端口。使用Dapr启动服务时，请指定此端口：

   ```
   dapr run --app-id trafficcontrolservice --app-port 5000 --dapr-grpc-port 50001 dotnet run
   ```

服务已启动并正在运行。现在，您将使用仿真对此进行测试.

5. 打开一个新的命令行窗口，然后转到该仓库中的文件夹 `Assignment02/src/Simulation` 

6. 启动模拟程序:

   ```
   dotnet run
   ```

运行应用程序时，您应该会看到与以前类似的日志记录.

## 步骤 4: 使用 Dapr 可观测性

那么，如何检查Dapr的Government service 调用是否已处理？好吧，Dapr内置了一些可观察性。您可以使用Zipkin查看Dapr流量:

1. 打开浏览器，然后转到以下URL: [http://localhost:9411/zipkin](http://localhost:9411/zipkin).

2. 单击屏幕右上方的望远镜图标以搜索痕迹.

3. 您应该会看到有调用打入Government service。您可以单击任何条目以获取更多详细信息 :

   ![](img/zipkin-traces.png)

4. If you click the dependencies button and search, you will see the two services and the traffic flowing between them:

   ![](img/zipkin-dependencies.png)

## Next assignment

Make sure you stop all running processes before proceeding to the next assignment.

Go to [assignment 3](../Assignment03/README.md).