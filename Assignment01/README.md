# 作业 1 - 运行应用程序

在这个作业里, 您将运行该应用程序以确保一切正常。

> 如果要签出应用程序的代码，请在VS Code中的此存储库中打开文件夹 `Assignment 1`  

### 步骤 1. 运行 Government service

1. 打开一个新的命令行窗口

2. 转到文件夹 `Assignment01/src/GovernmentService`.

3. 使用命令 `dotnet run` 运行服务.

现在，您可以测试是否可以调用 Government service 的Web-API。您可以使用浏览器，CURL或其他HTTP客户端来执行此操作。但是有直接从VS Code测试RESTful API的便捷方法：

4. 安装VS Code 扩展 [**REST Client**](https://github.com/Huachao/vscode-restclient)

5. 在VS Code 里打开文件 `test.http` .

6. 在文件中点击 `Execute request` 向API发送一个请求. 结果将显示在右侧的单独窗口中:

   ![REST client](img/rest-client.png)

7. 在命令行窗口中检查日志记录。它看起来应该像这样:

   ![](img/logging-governmentservice.png)

### 步骤 2. 运行 TrafficControl service

1. 确保 Government service 正在运行 (参见步骤1).

2. 打开一个新的命令行窗口.

3. 转到文件夹 `Assignment01/src/TrafficControlService`.

4. 使用命令 `dotnet run` 运行服务.

5. 在VS Code中的文件夹中打开文件 `test.http` 
   
6. 在文件中点击 `Execute request` 向API发送请求.

7. 在命令行窗口中检查日志记录。它看起来应该像这样:

   TrafficControl service
   ![](img/logging-trafficcontrolservice.png)

   Government service:

   ![](img/logging-governmentservice2.png)

### 步骤 3. 运行 simulation

您已经使用REST客户端直接测试了API。现在，您将运行模拟，实际模拟高速公路上行驶的汽车。模拟将模拟3个入口和出口摄像机（每个车道一个）。

1. 确保 TrafficControl service 和 Government service 都在运行 (参见步骤 1 和 2).

2. 打开一个新的命令行窗口.

3. 转到文件夹 `Assignment01/src/Simulation`.

4. 使用命令启动服务 `dotnet run`.

5. 在所有三个命令行窗口中检查日志记录。您应该会看到与Government service和TrafficControl service 相似的输出。在模拟窗口中，您应该会看到以下内容： 
6. 
   ![](img/logging-simulation.png)

现在我们知道应用程序可以正常运行了。是时候开始将Dapr添加到应用程序了。

## 下一个作业

在进行下一个作业之前，请确保停止所有正在运行的进程

跳转到 [作业 2](../Assignment02/README.md).