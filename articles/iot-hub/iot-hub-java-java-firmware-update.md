---
title: "Atualização de firmware do dispositivo IoT hub do Azure (Java/Java) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos no IoT Hub do Azure para iniciar uma atualização de firmware do dispositivo. Utilizar o dispositivo IoT do Azure SDK para Java para implementar uma aplicação de dispositivo simulado e implementar uma aplicação de serviço que aciona a atualização de firmware."
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 024c2e9bf580f97b412a85913ca29d757872556a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Utilize a gestão de dispositivos para iniciar uma atualização de firmware do dispositivo (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

No [introdução à gestão de dispositivos] [ lnk-dm-getstarted] tutorial, vimos como utilizar o [dispositivo duplo] [ lnk-devtwin] e [direcionar métodos] [ lnk-c2dmethod] primitivos reiniciar remotamente um dispositivo. Este tutorial utiliza os mesmos primitivos do IoT Hub e mostra como efetuar uma atualização de firmware simulada ponto-a-ponto.  Este padrão é utilizado na implementação de atualização de firmware para o [exemplo de implementação do dispositivo Raspberry Pi][lnk-rpi-implementation].

Este tutorial mostrar-lhe como:

* Criar uma aplicação de consola do Java que chama o **firmwareUpdate** método direto da aplicação de dispositivo simulado através do seu IoT hub.
* Criar uma aplicação de consola do Java que simula o dispositivo e implementa o **firmwareUpdate** método direto. Este método inicia um processo de fase multi que aguarda para transferir a imagem de firmware, transfere a imagem de firmware e, finalmente, aplica-se a imagem do firmware. Durante cada fase da atualização, o dispositivo utiliza as propriedades que relatados para elaborar relatórios sobre o progresso.

No final deste tutorial, tem duas aplicações de consola Java:

**atualização de firmware**, chama um método direto no dispositivo simulado, mostra a resposta e periodicamente atualizações da propriedade comunicado

**simulated-device**, liga ao seu IoT hub com a identidade de dispositivo criado anteriormente, recebe a chamada de método direto firmwareUpdate e é executada através de uma simulação de atualização de firmware

Para concluir este tutorial, precisa do seguinte:

* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Acionar uma atualização de firmware remota no dispositivo com um método direto
Nesta secção, crie uma aplicação de consola do Java que inicia uma atualização de firmware remota num dispositivo. A aplicação utiliza um método direto para iniciar a atualização e utiliza consultas de duplo de dispositivo para obter periodicamente o estado da atualização de firmware do Active Directory.

1. Crie uma pasta vazia designada fw-get-started.

1. Na pasta fw-get-started, crie um projeto Maven designado **atualização de firmware** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para a pasta de atualização de firmware.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta de atualização de firmware e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. Guarde e feche o ficheiro pom.xml.

1. Com um editor de texto, abra o ficheiro de origem firmware-update\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua **{youriothubconnectionstring}** com a cadeia de ligação do IoT hub que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Para implementar um método que lê as propriedades que relatados do dispositivo duplo, adicione o seguinte para o **aplicação** classe:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Modificar a assinatura do **principal** método para emitir as seguintes exceções:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Para invocar o método direto firmwareUpdate no dispositivo simulado, adicione o seguinte código para o **principal** método:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Para consultar as propriedades do dispositivo simulado comunicadas, adicione o seguinte código para o **principal** método:

    ```java
    ShowReportedProperties();
    ```

1. Para ativar a parar a aplicação, adicione o seguinte código para o **principal** método:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Guarde e feche o ficheiro firmware-update\src\main\java\com\mycompany\app\App.java.

1. Criar o **atualização de firmware** aplicações back-end e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta de atualização de firmware e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Simular um dispositivo para processar as chamadas de método direto
Nesta secção, crie uma aplicação de dispositivo simulado de consola Java que pode receber o método direto firmwareUpdate. A aplicação é executada através de um processo com múltiplos estado para simular a atualização de firmware reportedProperties a utilizar para comunicar estado.

1. Na pasta fw-get-started, crie um projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na linha de comandos, navegue para a pasta simulated-device.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta de atualização de firmware e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
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
    import java.util.HashMap;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua **{yourdeviceconnectionstring}** com a cadeia de ligação do dispositivo que anotou no *criar uma identidade de dispositivo* secção:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Para implementar a funcionalidade de método direto, forneça as chamadas de retorno adicionando as seguintes classes aninhadas para o **aplicação** classe:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
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

1. Para implementar funcionalidades do dispositivo duplo, forneça as chamadas de retorno adicionando as seguintes classes aninhadas para o **aplicação** classe:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Para implementar a atualização de firmware, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Modificar a assinatura do **principal** método para emitir as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Para iniciar o métodos diretos e rotina do dispositivos duplos, adicione o seguinte código para o **principal** método:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Para ativar a parar a aplicação, adicione o seguinte código ao fim do **principal** método:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java.

1. Criar o **simulated-device** aplicação e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. Numa linha de comandos do **simulated-device** pasta, execute o seguinte comando para começar a escutar o método de direta de atualização de firmware.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Numa linha de comandos do **atualização de firmware** pasta, execute o seguinte comando para invocar a atualização de firmware e consultar os dispositivos duplos no seu dispositivo simulado do seu IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Pode ver o dispositivo simulado responder ao método direto na consola do.

    ![Firmware atualizada com êxito][img-fwupdate]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, é utilizado um método direto para acionar uma atualização de firmware remota num dispositivo e utilizado as propriedades que relatados para seguir o progresso da atualização de firmware.

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device