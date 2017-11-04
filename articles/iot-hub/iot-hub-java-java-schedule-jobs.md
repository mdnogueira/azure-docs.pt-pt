---
title: Agendar tarefas IoT hub do Azure (Java) | Microsoft Docs
description: "Como agendar um trabalho do IoT Hub do Azure para invocar um método direto e definir uma propriedade pretendida em vários dispositivos. Utilizar o dispositivo IoT do Azure SDK para Java para implementar as aplicações do dispositivo simulado e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço para executar a tarefa."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: 9009afc83b40064c6dbf10100f514c681af9b96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="schedule-and-broadcast-jobs-java"></a>Tarefas de agendamento e de difusão (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilize o IoT Hub do Azure para agendar e controlar as tarefas que atualizam milhões de dispositivos. Utilize tarefas para:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Uma tarefa encapsula num wrapper uma destas ações e controla a execução em relação a um conjunto de dispositivos. Uma consulta do dispositivo duplo define o conjunto de dispositivos que a tarefa executa contra. Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método direto em 10 000 dispositivos que reinicia os dispositivos. Especifique o conjunto de dispositivos com uma consulta do dispositivo duplo e agendar a tarefa para ser executada num momento futuro. A evolução da tarefa controla como cada um dos dispositivos receber e executar o método direta de reinício.

Para obter mais informações sobre cada uma destas capacidades, consulte:

* Dispositivo duplo e propriedades: [começar a utilizar dispositivos duplos](iot-hub-java-java-twin-getstarted.md)
* Direcionar métodos: [guia para programadores do IoT Hub - métodos diretas](iot-hub-devguide-direct-methods.md) e [Tutorial: utilizar métodos diretos](iot-hub-java-java-direct-methods.md)

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo que implementa um método direto chamado **lockDoor**. A aplicação de dispositivo recebe também alterações de propriedade pretendido da aplicação de back-end.
* Criar uma aplicação de back-end que cria uma tarefa para chamar o **lockDoor** método direto em vários dispositivos. Outra tarefa envia atualizações da propriedade pretendido para vários dispositivos.

No final deste tutorial, terá de uma aplicação de dispositivo de consola java e uma aplicação de back-end de consola java:

**simulated-device** que liga ao seu IoT hub, implementa o **lockDoor** direcionar o método e identificadores pretendido alterações de propriedade.

**agenda de tarefas** que utilizar tarefas para chamar o **lockDoor** método direto e atualizar as propriedades do dispositivo duplo pretendida em vários dispositivos.

> [!NOTE]
> O artigo [SDKs IoT do Azure](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar um [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se preferir para criar a identidade do dispositivo através de programação, leia a secção correspondente do [ligar o seu dispositivo ao seu IoT hub utilizando Java](iot-hub-java-java-getstarted.md#create-a-device-identity) artigo. Também pode utilizar o [iothub explorer](https://github.com/Azure/iothub-explorer) ferramenta para adicionar um dispositivo ao seu IoT hub.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, crie uma aplicação de consola do Java que utiliza as tarefas:

* Chamar o **lockDoor** método direto em vários dispositivos.
* Envie propriedades pretendidas para vários dispositivos.

Para criar a aplicação:

1. No computador de desenvolvimento, crie uma pasta vazia designada `iot-java-schedule-jobs`.

1. No `iot-java-schedule-jobs` pasta, crie um projeto Maven designado **agenda de tarefas** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para o `schedule-jobs` pasta.

1. Com um editor de texto, abra o `pom.xml` ficheiros o `schedule-jobs` pasta e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o **cliente do serviço iot** pacote na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** utilizando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adicione o seguinte **criar** nó após a **dependências** nós. Esta configuração dá instruções ao Maven utilizar 1.8 do Java para criar a aplicação:

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

1. Guarde e feche o `pom.xml` ficheiro.

1. Com um editor de texto, abra o `schedule-jobs\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` com a cadeia de ligação do IoT hub que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Adicione o seguinte método para o **aplicação** classe para agendar uma tarefa para atualizar o **edifício** e **piso** pretendido propriedades no dispositivo duplo:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. Para agendar uma tarefa para chamar o **lockDoor** método, adicione o seguinte método para o **aplicação** classe:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. Para monitorizar uma tarefa, adicione o seguinte método para o **aplicação** classe:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. Para consultar os detalhes das tarefas que executou, adicione o método seguinte:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. Atualização do **principal** assinatura de método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Para executar e monitorizar as duas tarefas sequencialmente, adicione o seguinte código para o **principal** método:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. Guarde e feche o `schedule-jobs\src\main\java\com\mycompany\app\App.java` ficheiro

1. Criar o **agenda de tarefas** aplicação e corrigir os eventuais erros. Na sua linha de comandos, navegue para o `schedule-jobs` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, crie uma aplicação de consola do Java que processa as propriedades pretendidas enviadas pelo IoT Hub e implementa a chamada de método direto.

1. No `iot-java-schedule-jobs` pasta, crie um projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para o `simulated-device` pasta.

1. Com um editor de texto, abra o `pom.xml` ficheiros o `simulated-device` pasta e adicione as seguintes dependências para o **dependências** nó. Esta dependência permite-lhe utilizar o **cliente do dispositivo iot** pacote na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do dispositivo iot** utilizando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adicione o seguinte **criar** nó após a **dependências** nós. Esta configuração dá instruções ao Maven utilizar 1.8 do Java para criar a aplicação:

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

1. Guarde e feche o `pom.xml` ficheiro.

1. Com um editor de texto, abra o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substituir `{youriothubname}` com o nome do seu IoT hub e `{yourdevicekey}` com a chave de dispositivo valor que gerou no *criar uma identidade de dispositivo* secção:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**.

1. Para imprimir o dispositivo duplo enviar notificações para a consola, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Para imprimir notificações de método direta para a consola, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Para processar as chamadas de método direta do IoT Hub, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. Atualização do **principal** assinatura de método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Adicione o seguinte código para o **principal** método para:
    * Crie um cliente de dispositivo para comunicar com IoT Hub.
    * Criar um **dispositivo** objeto para armazenar as propriedades do dispositivo duplo.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. Para iniciar os serviços do cliente de dispositivo, adicione o seguinte código para o **principal** método:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. A aguardar que o utilizador prima a **Enter** chave antes de encerrar, adicione o seguinte código ao fim do **principal** método:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Guarde e feche o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Criar o **simulated-device** aplicação e corrigir os eventuais erros. Na sua linha de comandos, navegue para o `simulated-device` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar as aplicações

Agora está pronto para executar as aplicações de consola.

1. Numa linha de comandos do `simulated-device` pasta, execute o seguinte comando para iniciar a aplicação de dispositivo à escuta de alterações de propriedade pretendido e chamadas de método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![O cliente do dispositivo é iniciado](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. Numa linha de comandos do `schedule-jobs` pasta, execute o seguinte comando para executar o **agenda de tarefas** do app service para executar tarefas de dois. O primeiro define os valores de propriedade pretendido, o segundo chama o método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Serviço de aplicações Java IoT Hub cria duas tarefas](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. A aplicação de dispositivo processa a alteração da propriedade pretendida e a chamada de método direto:

    ![O cliente do dispositivo responde às alterações](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Criar uma aplicação de back-end para executar tarefas de dois. A primeira tarefa definir valores de propriedade pretendida e a segunda tarefa chamou um método direto.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub](iot-hub-java-java-getstarted.md) tutorial.
* Controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador) com o [utilizar métodos diretos](iot-hub-java-java-direct-methods.md) tutorial.
