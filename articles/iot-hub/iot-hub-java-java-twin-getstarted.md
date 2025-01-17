---
title: Azure IoT 中心设备孪生入门 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用适用于 Java 的 Azure IoT 设备 SDK 实现设备应用，并使用适用于 Java 的 Azure IoT 服务 SDK 实现可添加标记并运行 IoT 中心查询的服务应用。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: 6d2d0540786f1aa4bec35cf4bec26212cb7df7ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404210"
---
# <a name="get-started-with-device-twins-java"></a>设备孪生入门 (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程中，你将创建两个 Java 控制台应用：

* **add-tags-query**：一个 Java 后端应用，用于添加标记并查询设备孪生。
* **simulated-device**：Java 设备应用，它连接到 IoT 中心，并使用报告的属性报告其连接状态。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文介绍了可用于构建设备和后端应用的 Azure IoT SDK。

要完成本教程，需要：

* 最新的 [Java SE 开发工具包 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>创建服务应用

本部分将创建一个 Java 应用，用于将位置元数据作为标记添加到 IoT 中心内与 **myDeviceId** 关联的设备孪生。 该应用首先在 IoT 中心查询位于美国的设备，然后查询报告已建立移动电话网络连接的设备。

1. 在开发计算机上，创建名为 `iot-java-twin-getstarted` 的空文件夹。

2. 在 `iot-java-twin-getstarted` 文件夹中，通过命令提示符使用以下命令创建名为 **add-tags-query** 的 Maven 项目。 请注意，这是一条很长的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 在命令提示符下，导航到 `add-tags-query` 文件夹。

4. 使用文本编辑器打开 `add-tags-query` 文件夹中的 `pom.xml` 文件，在 **dependencies** 节点中添加以下依赖项。 通过此依赖项可以使用应用中的 **iot-service-client** 包来与 IoT 中心通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

5. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 保存并关闭 `pom.xml` 文件。

7. 使用文本编辑器打开 `add-tags-query\src\main\java\com\mycompany\app\App.java` 文件。

8. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. 将以下类级变量添加到 **App** 类。 将`{youriothubconnectionstring}`替换为之前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. 更新 **main** 方法签名，以包含以下 `throws` 子句：

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. 将以下代码添加到 **main** 方法，以创建 **DeviceTwin** 和 **DeviceTwinDevice** 对象。 **DeviceTwin** 对象处理与 IoT 中心之间的通信。 **DeviceTwinDevice** 对象使用设备孪生的属性和标记来表示设备孪生：

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. 将以下 `try/catch` 块添加到 **main** 方法：

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. 若要更新设备孪生中的 **region** 和 **plant** 设备孪生标记，请在 `try` 块中添加以下代码：

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. 若要在 IoT 中心查询设备孪生，请将以下代码添加到 `try` 块（添加到上一步骤中添加的代码后面）。 该代码运行两个查询。 每个查询最多返回 100 个设备：

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. 保存并关闭 `add-tags-query\src\main\java\com\mycompany\app\App.java` 文件

16. 生成 **add-tags-query** 应用并更正任何错误。 在命令提示符下，导航到 `add-tags-query` 文件夹并运行以下命令：

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>创建设备应用

本部分创建一个 Java 控制台应用，用于设置要发送到 IoT 中心的报告属性值。

1. 在命令提示符下使用以下命令，在 `iot-java-twin-getstarted` 文件夹中创建名为 **simulated-device** 的 Maven 项目。 请注意，这是一条很长的命令：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到 `simulated-device` 文件夹。

3. 使用文本编辑器打开 `simulated-device` 文件夹中的 `pom.xml` 文件，在 **dependencies** 节点中添加以下依赖项。 通过此依赖项可以使用应用中的 **iot-device-client** 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-device-client**。

4. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. 保存并关闭 `pom.xml` 文件。

6. 使用文本编辑器打开 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

7. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. 将以下类级变量添加到 **App** 类。 将 `{youriothubname}` 替换为 IoT 中心名称，将 `{yourdevicekey}` 替换为在“创建设备标识”部分中生成的设备密钥值：

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    本示例应用在实例化 **DeviceClient** 对象时使用 **protocol** 变量。 

1. 将以下方法添加到 **App** 类以输出有关孪生更新的信息：

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. 将以下代码添加到 **main** 方法，以便：
    * 创建用来与 IoT 中心通信的设备客户端。
    * 创建一个 **Device** 对象用于存储设备孪生属性。

      ```java
      DeviceClient client = new DeviceClient(connString, protocol);

      // Create a Device object to store the device twin properties
      Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
      };
      ```

10. 将以下代码添加到 **main** 方法，创建 **connectivityType** 报告属性并将其发送到 IoT 中心：

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

11. 将以下代码添加到 **main** 方法的末尾。 按 **Enter** 键，等待一段时间让 IoT 中心报告设备孪生操作的状态：

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. 修改 **main** 方法的签名，包含如下所示的异常：

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

1. 保存并关闭 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

13. 生成 **simulated-device** 应用并更正任何错误。 在命令提示符下，导航到 `simulated-device` 文件夹并运行以下命令：

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>运行应用

现在可以运行控制台应用了。

1. 在 `add-tags-query` 文件夹中的命令提示符下，运行以下命令以运行 **add-tags-query** 服务应用：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT 中心服务应用会更新标记值并运行设备查询](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    可以看到，**plant** 和 **region** 标记已添加到设备孪生。 第一个查询返回设备，但第二个查询则不会。

2. 在 `simulated-device` 文件夹中的命令提示符下，运行以下命令将 **connectivityType** 报告属性添加到设备孪生：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![设备客户端会添加 **connectivityType** 报告属性](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. 在 `add-tags-query` 文件夹中的命令提示符下，再次运行以下命令以运行 **add-tags-query** 服务应用：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT 中心服务应用会更新标记值并运行设备查询](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    现在，设备已将 **connectivityType** 属性发送到 IoT 中心，第二个查询返回了设备。

## <a name="next-steps"></a>后续步骤

在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了一个设备应用用于报告设备孪生中的设备连接信息。 此外，还学习了如何使用类似于 SQL 的 IoT 中心查询语言查询设备孪生信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门](quickstart-send-telemetry-java.md)教程学习如何从设备发送遥测数据。

* 通过[使用直接方法](quickstart-control-device-java.md)教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。