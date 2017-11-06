---
title: "Introdução à gestão de dispositivos do IoT Hub do Azure (Java) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos do IoT Hub do Azure para iniciar um reinício do dispositivo remoto. Utilizar o dispositivo IoT do Azure SDK para Java para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: .java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7e3837582e2020dc560a2b624352f7326ea87c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-management-java"></a>Introdução à gestão de dispositivos (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no seu IoT hub.
* Crie uma aplicação de dispositivo simulado que implementa um método direto para reiniciar o dispositivo. Métodos diretos são invocados a partir da nuvem.
* Crie uma aplicação que invoca o método direto reiniciar a aplicação de dispositivo simulado através do seu IoT hub. Esta aplicação, em seguida, monitoriza as propriedades comunicadas do dispositivo para ver quando a operação de reinício estiver concluída.

No final deste tutorial, tem duas aplicações de consola Java:

**simulated-device**. Esta aplicação:

* Estabelece ligação ao seu IoT hub com a identidade de dispositivo que criou anteriormente.
* Recebe uma chamada de método direta de reinício.
* Simula o reinício do sistema físico.
* Os relatórios da hora da última reinicialização através de uma propriedade que relatados.

**reinício de Acionador**. Esta aplicação:

* Chama um método direto na aplicação do dispositivo simulado.
* Mostra a resposta para a chamada de método direto enviada pelo dispositivo simulado
* Apresenta a atualização comunicadas propriedades.

> [!NOTE]
> Para obter informações sobre os SDKs que pode utilizar para criar aplicações a executar em dispositivos e a sua solução de back-end, consulte [SDKs IoT do Azure][lnk-hub-sdks].

Para concluir este tutorial, precisa de:

* Java SE 8. <br/> [Preparar o seu ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.
* Maven 3.  <br/> [Preparar o seu ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o [Maven][lnk-maven] para este tutorial no Windows ou no Linux.
* [Versão do node.js 0.10.0 ou posterior](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar um reinício remoto no dispositivo com um método direto

Nesta secção, criar uma aplicação de consola do Java que:

1. Invoca o método direto reiniciar a aplicação de dispositivo simulado.
1. Mostra a resposta.
1. Consultar as propriedades que relatados enviados do dispositivo para determinar quando o reinício está concluído.

Esta aplicação de consola liga ao seu IoT Hub para invocar o método direto e ler as propriedades que relatados.

1. Crie uma pasta vazia designada dm-get-started.

1. Na pasta dm-get-started, crie um projeto Maven designado **acionador reinício** utilizando o seguinte comando na sua linha de comandos. O seguinte mostra um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para a pasta de reinício de Acionador.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta de reinício do acionador e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-service-client** utilizando a [pesquisa Maven][lnk-maven-service-search].

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

1. Guarde e feche o ficheiro pom.xml.

1. Com um editor de texto, abra o ficheiro de origem trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` com a cadeia de ligação do IoT hub que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Para implementar um thread que lê as propriedades que relatados do dispositivo duplo cada 10 segundos, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Modificar a assinatura do **principal** método para acionar a seguinte exceção:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Para invocar o método direta de reinício do dispositivo simulado, adicione o seguinte código para o **principal** método:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Para iniciar o thread para consultar as propriedades do dispositivo simulado comunicadas, adicione o seguinte código para o **principal** método:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Para ativar a parar a aplicação, adicione o seguinte código para o **principal** método:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Guarde e feche o ficheiro trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Criar o **acionador reinício** aplicações back-end e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta de reinício do acionador e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, crie uma aplicação de consola do Java que simula um dispositivo. A aplicação escuta o reinício direcionar a chamada de método do seu IoT hub e imediatamente responde nesse chamada. A aplicação, em seguida, permanecerá suspenso durante o tempo simular o processo de reinício antes de utilizar uma propriedade que relatados para notificar o **acionador reinício** aplicações de back-end que o reinício está concluído.

1. Na pasta dm-get-started, crie um projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Segue-se um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na linha de comandos, navegue para a pasta simulated-device.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta simulated-device e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-device-client** utilizando a [pesquisa Maven][lnk-maven-device-search].

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

1. Guarde e feche o ficheiro pom.xml.

1. Com um editor de texto, abra o ficheiro de origem simulated-device\src\main\java\com\mycompany\app\app.Java.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{yourdeviceconnectionstring}` com a cadeia de ligação do dispositivo que anotou no *criar uma identidade de dispositivo* secção:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Para implementar um processador de chamada de retorno para eventos de estado de método direto, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Para implementar um processador de chamada de retorno para eventos de estado do dispositivo duplo, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Para implementar um processador de chamada de retorno para eventos de propriedade, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Para implementar um thread para simular o reinício de dispositivo, adicione a seguinte classe aninhada para o **aplicação** classe. O thread permaneça suspenso durante cinco segundos e, em seguida, define o **lastReboot** comunicadas propriedade:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Para implementar o método direto no dispositivo, adicione a seguinte classe aninhada para o **aplicação** classe. Quando a aplicação simulada recebe uma chamada para o **reiniciar** método direto, devolve uma confirmação para o autor da chamada e, em seguida, inicia um thread para processar o reinício:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Modificar a assinatura do **principal** método para emitir as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Ao instanciar um **DeviceClient**, adicione o seguinte código para o **principal** método:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Iniciar a deteção para chamadas de método direto, adicione o seguinte código para o **principal** método:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Para encerrar o simulador de dispositivo, adicione o seguinte código para o **principal** método:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java.

1. Criar o **simulated-device** aplicações back-end e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Numa linha de comandos da pasta simulated-device, execute o seguinte comando para começar a escutar para chamadas de método de reinício do seu IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicação de dispositivo simulada Java IoT Hub para escutar reiniciar chamadas de método direto][1]

1. Numa linha de comandos na pasta acionador reinício, execute o seguinte comando para chamar o método de reinício no seu dispositivo simulado do seu IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Serviço de aplicações Java IoT Hub para chamar o método direta de reinício][2]

1. O dispositivo simulado responde a chamada de método direta de reinício:

    ![Aplicação de dispositivo simulada Java IoT Hub responde a chamada de método direto][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22